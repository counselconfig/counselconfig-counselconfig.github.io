---
layout: post
title: Single Customer View
---

_"A Single Customer View is an aggregated, consistent and holistic representation of the
data known by an organisation about its customer"_ John and Misra (2017)

## Single Customer View

The Single Customer View (SCV) is a Business Intelligence system owned and started in 2015 by the Customer Insight & Data (CI&D) team within Marketing & Communications. The SCV enables TNA to understand and engage more fully with its audiences whilst supporting longer-term business plans to inspire wider awareness and use of the archive. The Business Systems Support (BSS) team has been engaged by CI&D to develop and maintain the SCV with subcontractor support from Model Citizens (internal data feeds) and Data Mettle (external data feeds). CI&D are now focusing on the value of SCV data by producing new reports and analysis that require BSS to operate.

<video src="https://nationalarchivesuk.sharepoint.com/:v:/r/sites/MC_Narnia/Intranet%20Resources/Video/2021-06-15%20Data%20Spotlight%201.mp4" width=400 controls>
</video>


## System overview

- Development server: na-d-sqlc02 
>>>>Python 3.7.3
AccessDatabaseEngine_X64exe
Visual Studio Professional - SQL Server Integration Services
- Network attached storage: 
>>>>"\\\na-stor02\visualstudiocommunity$\Single Customer View"

- Database servers: 
>>>>na-sqlc03v03.SingleCustomerView 
>>>>na-t-sqcl02v02.SingleCustomerView

### Production deployment
![CMDB Single Customer View.svg](/.attachments/CMDB%20Single%20Customer%20View-c390d64e-a6ba-4068-888c-56c4236f8daa.svg =900x500)

Access to PUB-MGMT02 is via na-rdms01. using IP 172.16.250.11 with a-[username]. Ask TSD for assistance accessing this server.

### Development

Commits can be found [here](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?_a=history)

## Internal sources

### Linked server stack

- 'record_copying_legacy' db is now 'RecordCopying' db
- [16_RMS_CT] is obsolete RFC 16404
- [23_RCO_TX] and [34_WIF_CT] sources have been commented out in the stored procedure dbo.load_source_data as deprecated
- [35_WIF_SS] ingestion process to be confirmed – data currently not supplied 20/05/2021

![lsp.svg](/.attachments/lsp-69995fe5-1db3-4d24-8ae5-9a3d0e079a4a.svg =900x500)




<table>
    <thead>
        <tr>
            <th>Layer 1</th>
            <th>Layer 2</th>
            <th>Layer 3</th>
            <th>Layer 4</th>
            <th>Layer 5</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=5>L1 Name</td>
            <td rowspan=5>L2 Name</td>
            <td rowspan=5>L3 Name</td>
            <td rowspan=2>L4 Name A</td>
            <td>L3 Name A</td>
        </tr>
        <tr>
            <td>L5 Name B</td>
        </tr>
        <tr>
            <td rowspan=8>L4 Name B</td>
            <td>L5 Name C</td>
        </tr>
        <tr>
            <td>L5 Name D</td>
        </tr>
        <tr>
            <td>L5 Name D</td>
        </tr>
    </tbody>
</table>

## External sources

External data is stored in Y:\Single Customer View\Data\Sources

<table>
    <thead>
        <tr>
            <th>Import method</th>
            <th>Table name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=5>External data : .csv file / SSIS</td>
            <td>EMAIL_TNA</td>
        </tr>
        <tr>
            <td>EMAIL_RN</td>
        </tr>
        <tr>
            <td>EMAIL_SUPP</td>
        </tr>
        <tr>
            <td>Shop_CT</td>
        </tr>
        <tr>
            <td>Shop_TX</td>
        </tr>
        <tr>
            <td rowspan=4>External data : API / SSIS</td>
            <td>ss_answers</td>
        <tr>
            <td>ss_responses</td>
        </tr>
        <tr>
            <td>ss_surveys</td>
        </tr>
        <tr>
            <td>25_EVE_TX</td>
        </tr>
        <tr>
            <td rowspan=4>External data :*.xlsx file / SSIS</td>
            <td>25a_FLOW_TX</td>
        <tr>
    </tbody>
</table>

###SQL Server Integration Services

There is an integration services dtsx [package](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2FSCV2.dtsx&version=GBmaster) used to ETL the external sources from returned Eventbrite and SmartSurvey data explained below, and FLOW data and Shopify data provided by the CI&D team. It also includes a staging script and an update customer id mapping script. 

### eConnect application

This is a seperate application written in C# that pulls in dynamic columns data provided by the CI&D team. The code is [here](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2Feconnect%2Feconnect%2FEconnect.cs&version=GBmaster).


### Eventbrite and SmartSurvey APIs

The SCV uses the https://www.eventbriteapi.com/v3 and https://api.smartsurvey.io/v1 APIs to make request for events and survey data using the [Evenbrite script](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2FEventbrite%2Fmain.py) and [SmartSurvey script](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2FSmartSurvey%2Fmain.py).

The Eventbrite and SmartSurvey scripts query the relevant API’s for events and survey data respectively, and then transforms the data into a format that is suitable for loading into MS SQL server tables. Note: Initially we struggled with running both scripts from within the TNA intranet, as TNA uses an [SSL Proxy](https://security.stackexchange.com/questions/106990/what-is-the-purpose-of-a-man-in-the-middle-certificate). This caused the API calls to fail with an SSL error; any future SSL errors are likely to be caused by changes in the TNA intranet set-up rather than an error in the scripts or on the Eventbrite/SmartSurvey side. The easiest way to verify this is to run the scripts on a computer not on the TNA intranet – if it runs the original error is very likely caused by TNA intranet configurations.

It is recommended for anyone editing these scripts to familiarize themselves with the documentation for the API in question. In particular it can be useful to explore the return JSON data using an appropriate application, for example Postman.

#### Overview Eventbrite and SmartSurvey APIs

The two scripts have a few things in common:

- They use the “requests” package to call the APIs,
- They both use an SSLContextAdapter to load the correct certificate (which fixes the SSL issue mentioned above).
- Both API’s return data as JSON but in the Python scripts these are converted to *.xlsx or *.csv
- They require authentication keys that identify the caller as TNA,
- They start by getting all events/surveys that TNA has set up,
- They loop through each event/survey and call the API to get specific information about that event/survey,
- Both API’s use pagination so we receive the data in chunks,
- They extract the relevant data and transform it into a Pandas DataFrame which can be easily saved as a csv or Excel file.
- Both services has rate limits. This is especially notable for Eventbrite, where we need to perform a large number of calls to cover all the events, and we sometimes hit the rate limit (in which case the script waits and retries after a set time).

#### SmartSurvey

To query the SmartSurvey the caller needs an API token and an API token secret, which can be obtained by logging into the SmartSurvey account as described in the [documentation](https://docs.smartsurvey.io/docs).

The script allows you to pass in a JSON file with surveys, note that this is mainly present to simplify the debugging and development of the script, and is not normally used.

This script is pretty straightforward, although the following functions warrants comment: ```extract_answer```. This function handles the fact that answer data varies depending on which type of input cell was used to ask the question (e.g. free text, radio button, etc.). This function handles all these variations and ensures the output data is homogenous.

####  Eventbrite

Similar to SmartSurvey, the caller needs an API key, please read the [documentation](https://www.eventbrite.com/platform/docs/introduction) on how to obtain this. The Eventbrite API has a wide range of functions available, but we only use two: One to get a list of all events that TNA has organised, and one that we use to get details about each event.

This script allows you to run it “locally”, which should only be used when running on e.g. you own computer outside the TNA intranet and is normally not used. It also allows you to query a single event and write the response JSON to a file, which is also only exceptionally used when debugging.

There is a lot of column remapping going on in this script, and the reason for this is to keep it compatible with the data loading that was set up for the previous SAS+Python solution, which unfortunately garbled some column names.

The function ```continuation_call``` handles Eventbrite's particular take on pagination. For any result that is spread over several pages, Eventbrite sends you a “continuation” attribute with a code that is used to obtain the next page. This function handles this quirk and concatenates the results into a single list.

The function ```has_events_beyond_cutoff``` means events are returned in the reverse order of event date, meaning recent events come first. As we are not interested in any events older than 3 years, this filtering function is used to stop querying Eventbrite 

## Installing python packages

Requirements:

```
numpy==1.16.2
openpyxl==2.6.2
pandas==0.24.2
pipdeptree==0.13.2
python-dateutil==2.8.0
requests==2.21.0
xlrd==1.2.0
XlsxWriter==1.1.8
```
To install a python package run the following command (replacing <package name> with the name of your package) like this:

```
C:\SCV> pip install --trusted-host pypi.python.org --trusted-host pypi.org --trusted-host files.pythonhosted.org --user --no-cache dir <package name>
```

Note that every time you close a command line window, it 'forgets' this setting, so you have to run it whenever you open a new command line window.

Alternatively, place all requirement packages listed above in a requirements.txt file and run 
```
C:\SCV> pip install --trusted-host pypi.python.org --trusted-host pypi.org --trusted-host files.pythonhosted.org --user --no-cache-dir -r requirements.txt
```

If you get a "WRONG_VERSION_NUMBER" error, then please open an encrypted webpage (e.g. www.en.wikipedia.org) in any browser and try again. If you get a "THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE" error, then the package is being intercepted by the TNA firewall. This seems to happen for some specific packages, and I don't understand why, but if this happens you need an administrator to install the package for you.

If an error (see call  41187) occurs when downloading the requirements packages like this:
```
Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'ConnectTime
outError(<pip._vendor.urllib3.connection.VerifiedHTTPSConnection object at 0x000000DB6C13C748>, 'Connection to pypi.org
timed out. (connect timeout=15)')
```
Try e.g. the following command syntax (--cert may not be needed, proxy command may not work off-premises):
```
pip --proxy http://10.101.1.20:8080 --cert \\na-stor01\software$\temp\jn\na-cachec01-chain.pem install XlsxWriter==1.1.8
```



## Updating SCV
### Internal SCV data

A SQL Server Agent job named 'SCV_load_source_data' runs automatically on Sunday at 18.00 to execute a stored procedure 'dbo.load_source_data' that inserts linked source data. Success or failure notifications are sent to dbadmin@nationalarchives.gov.uk - please check the DB Admin Inbox or add yourself to the emailing list on the server.


### External SCV data

1. RDP to na-d-sql02
2. Double click "Y:\Single Customer View\Integration Services\SCV Data Integration\SCV.bat". 

- The process launched by SCV.bat may take up to 2 hours to update, mostly due to the API requests.
- SCV.bat initially launches two API windows in command prompt.  API requests may encounter ```Hit rate limit, waiting for 2 minutes...```, please continue to wait.
- Do not disconnect your session on na-d-sql02 until the process has finished. Automatic log out should not cut the connection. Completion should look like below. 

![scvbat.png](/.attachments/scvbat-ba06eab9-873d-4736-b31e-2cb365860a65.png)

The process comprises the following:

::: mermaid 
graph TB;

        1[SCV.bat]-->A{ }
        1[SCV.bat]-->B{ }
        A[EB_output_main.bat]-->D
        B[SS_output_main.bat]-->D
        D[Backup sources.ps1]-->E
        E[Storage limitation delete.ps1]-->F
        F[econnect.exe]-->G
        G[SCV2.dtsx]
:::


| Program                       | Path                                                                                                                  | Action                                                                                                                                             |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| [EB_output_main.bat](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2FEventbrite%2FEB_output_main.bat)          | Y:\\Single Customer View\\Integration Services\\SCV Data Integration\\SCV Data Integration\\Eventbrite                | Python program to return data from the Eventbrite API. Files: Eventbrite_answers.xlsx Eventbrite_full_data.xlsx                                                                                             |
| [SS_output_main.bat](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2FSmartSurvey%2FSS_output_main.bat)          | Y:\\Single Customer View\\Integration Services\\SCV Data Integration\\SCV Data Integration\\SmartSurvey               | Python program to return data from the SmartSurvey API. Files: ss_answers.xlsx ss_responses.xlsx ss_surveys.xlsx                                                                                         |
| Backup sources.ps1            | Y:\\Single Customer View\\Integration Services\\SCV Data Integration\\SCV Data Integration\\Backup sources script     | Backs up data sources to Y:\Single Customer View\Data\Backup sources                                             |
| [Storage limitation delete.ps1](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2FStorage%20limitation%20script%2FStorage%20limitation%20delete.ps1) | Y:\\Single Customer View\\Integration Services\\SCV Data Integration\\SCV Data Integration\\Storage limitation script | Applies UK GDPR (Art 5(1)(e)) storage limitation to delete data more than 1 month from execution on Y:\Single Customer View\Data\Backup sources  |
| econnect.exe                  | Y:\\Single Customer View\\Integration Services\\SCV Data Integration\\SCV Data Integration\\Econnect                  | C# program that inserts dynamic columns data into the SingleCustomerView database. Files: Email_Research_News.csv Email_Suppressions.csv Email_TNA_News.csv                                                           |
| [SCV2.dtsx](https://dev.azure.com/TNAKEW/Integration%20Services/_git/Single%20Customer%20View?path=%2FSCV%20Data%20Integration%2FSCV2.dtsx)                     | Y:\\Single Customer View\\Integration Services\\SCV Data Integration\\SCV Data Integration\\                          | Inserts Eventbrite and SmartSurvey data files from above and FLOW and Shopify data (provided as FLOW_registrations.xlsx, Shopify-Customers.csv, and Shopify-Sales.csv) including staging Eventbrite data in the SingleCustomerView database                                                               |














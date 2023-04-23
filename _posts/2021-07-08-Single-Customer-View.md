
_"A Single Customer View is an aggregated, consistent and holistic representation of the data known by an organisation about its customer"_ John & Misra (2017)

The Single Customer View (SCV) is a Business Intelligence system owned and started in 2015 by the Customer Insight & Data (CI&D) team at The National Archives. The SCV enables TNA to understand and engage more fully with its audiences whilst supporting longer-term business plans to inspire wider awareness and use of the archive. I have lead the data architecture and software development of this ETL project since 2018 working with subcontractors Citizens (internal data feeds) and Data Mettle (external data feeds) - the archives are now focusing on the value of SCV data by producing new reports and analysis.

Here is some technical information:

# Physical deployment 

The physical server architecture I have engineered looks somewhat like this:

<img src="/assets/images/scvarchitecture.svg" alt="physcialarchitecture">


# Internal sources

## Linked server stack

- 'record_copying_legacy' db is now 'RecordCopying' db
- [16_RMS_CT] is obsolete RFC 16404
- [23_RCO_TX] and [34_WIF_CT] sources have been commented out in the stored procedure dbo.load_source_data as deprecated
- [35_WIF_SS] ingestion process to be confirmed – data currently not supplied 20/05/2021

<img src="/assets/images/linkedserverstak.svg" alt="scvlinkedserver">


# External sources

External data follows this scheme:


<img src="/assets/images/importmethods.png" alt="scvlinkedserver">



# SQL Server Integration Services

There is an integration services dtsx package used to ETL the external sources from returned Eventbrite and SmartSurvey data, FLOW data, and Shopify data provided by the CI&D team. It also includes a staging script and an update customer id mapping script. 


## Updating SCV
### Internal SCV data

A SQL Server Agent job named 'SCV_load_source_data' runs automatically on Sunday at 18.00 to execute a stored procedure called [dbo.load_source_data.sql](https://github.com/counselconfig/SCV_internal_sources/blob/main/load_source_data.sql) that inserts linked source data. Success or failure notifications are sent to a dedeicated db admin address.

The Data Transformation Services Package XML File (dtsx) package looks like this when I am debugging it:

<img src="/assets/images/etl2.png" alt="etl2">

It is quite long:


<img src="/assets/images/etl.png" alt="etl1">


The entire processs is executed with this [batch file](https://github.com/counselconfig/scv-batch-process/blob/main/SCV.bat) in the manner of:

<img src="/assets/images/SCVprocess.png"  alt="scvprocess">



### External SCV data

Things I have noted about the external data feed when I run my batch script: 

- The process launched by SCV.bat may take up to 2 hours to update, mostly due to the API requests.
- SCV.bat initially launches two API windows in command prompt.  API requests may encounter ```Hit rate limit, waiting for 2 minutes...```, please continue to wait.
- Do not disconnect your session on na-d-sql02 until the process has finished. Automatic log out should not cut the connection. Completion should look like below. 

<img src="/assets/images/cli.png" alt="scvlinkedserver">


<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-ssie{color:#3F3F3F;text-align:left;vertical-align:middle}
.tg .tg-h718{background-color:#F7F7F7;color:#3F3F3F;text-align:left;vertical-align:middle}
.tg .tg-l8p4{background-color:#F0F0F0;color:#3F3F3F;font-weight:bold;text-align:left;vertical-align:middle}
.tg .tg-2eb4{color:#3F3F3F;text-align:left;text-decoration:underline;vertical-align:top}
.tg .tg-dhxe{background-color:#F7F7F7;color:#3F3F3F;text-align:left;text-decoration:underline;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-l8p4"><span style="color:inherit;background-color:#F0F0F0">Program</span></th>
    <th class="tg-l8p4"><span style="color:inherit;background-color:#F0F0F0">Path</span></th>
    <th class="tg-l8p4"><span style="color:inherit;background-color:#F0F0F0">Action</span></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-2eb4"><a href="https://github.com/counselconfig/Eventbrite-API-client/blob/main/main.py"><span style="text-decoration:underline;color:#000;background-color:inherit">Eventbrite main.py</span></a></td>
    <td class="tg-ssie"><span style="color:inherit;background-color:inherit">Y:\Single Customer View\Integration Services\SCV Data Integration\SCV Data Integration\Eventbrite</span></td>
    <td class="tg-ssie"><span style="color:inherit;background-color:inherit">Python program to return data from the Eventbrite API. Files: Eventbrite_answers.xlsx Eventbrite_full_data.xlsx</span></td>
  </tr>
  <tr>
    <td class="tg-dhxe"><a href="https://github.com/counselconfig/SmartSurvey-API-client/blob/main/main.py"><span style="text-decoration:underline;color:#000;background-color:inherit">Smart Survey main.py</span></a></td>
    <td class="tg-h718"><span style="color:inherit;background-color:inherit">Y:\Single Customer View\Integration Services\SCV Data Integration\SCV Data Integration\SmartSurvey</span></td>
    <td class="tg-h718"><span style="color:inherit;background-color:inherit">Python program to return data from the SmartSurvey API. Files: ss_answers.xlsx ss_responses.xlsx ss_surveys.xlsx</span></td>
  </tr>
  <tr>
    <td class="tg-2eb4"><a href="https://github.com/counselconfig/scv-backup-sources/blob/main/Backup%20sources.ps1"><span style="text-decoration:underline;color:#000;background-color:inherit">Backup sources.ps1</span></a></td>
    <td class="tg-ssie"><span style="color:inherit;background-color:inherit">Y:\Single Customer View\Integration Services\SCV Data Integration\SCV Data Integration\Backup sources script</span></td>
    <td class="tg-ssie"><span style="color:inherit;background-color:inherit">Backs up data sources to Y:\Single Customer View\Data\Backup sources</span></td>
  </tr>
  <tr>
    <td class="tg-dhxe"><a href="https://github.com/counselconfig/storage-limitation/blob/main/Storage%20limitation%20delete.ps1"><span style="text-decoration:underline;color:#000;background-color:inherit">Storage limitation delete.ps1</span></a></td>
    <td class="tg-h718"><span style="color:inherit;background-color:inherit">Y:\Single Customer View\Integration Services\SCV Data Integration\SCV Data Integration\Storage limitation script</span></td>
    <td class="tg-h718"><span style="color:inherit;background-color:inherit">Applies UK GDPR (Art 5(1)(e)) storage limitation to delete data more than 1 month from execution on Y:\Single Customer View\Data\Backup sources</span></td>
  </tr>
  <tr>
    <td class="tg-2eb4"><a href="https://github.com/counselconfig/econnect/blob/main/econnect/Econnect.cs"><span style="text-decoration:underline;color:#000;background-color:inherit">econnect.cs</span></a></td>
    <td class="tg-ssie"><span style="color:inherit;background-color:inherit">Y:\Single Customer View\Integration Services\SCV Data Integration\SCV Data Integration\Econnect</span></td>
    <td class="tg-ssie"><span style="color:inherit;background-color:inherit">C# program that inserts dynamic columns data into the SingleCustomerView database. Files: Email_Research_News.csv Email_Suppressions.csv Email_TNA_News.csv</span></td>
  </tr>
  <tr>
    <td class="tg-dhxe"><span style="text-decoration:underline;color:#000;background-color:inherit">SCV2.dtsx</span></a></td>
    <td class="tg-h718"><span style="color:inherit;background-color:inherit">Y:\Single Customer View\Integration Services\SCV Data Integration\SCV Data Integration\</span></td>
    <td class="tg-h718"><span style="color:inherit;background-color:inherit">Inserts Eventbrite and SmartSurvey data files from above and FLOW and Shopify data (provided as FLOW_registrations.xlsx, Shopify-Customers.csv, and Shopify-Sales.csv) including staging Eventbrite data in the SingleCustomerView database</span></td>
  </tr>
</tbody>
</table>

Lately I have introduced this [WinSCP client](https://github.com/counselconfig/WinSCP-DEX-export-scheduling/blob/master/winscpdex/Program.cs) to collect eConnect data from REaD's DEX server. 



---
layout: post
title: Elasticsearch
tags: Elasticsearch database AWS Java Apache
---

Back around 2012 when Government Digital Services  moved its Solr backend to Elasticsearch, other departments have since followed suit - The National Archives did the same  and recently awarded a contract to Numiko Ltd to implement [Elasticsearch for its  Beta phase Etna project](https://www.digitalmarketplace.service.gov.uk/digital-outcomes-and-specialists/opportunities/15311),

This is a curious subject since both Solr and elasticsearch are Lucene based search servers that expose a HTTP interface. 


## Elasticsearch v. Solr

### Index control

A useful feature of elasticsearch is that it exposes a range of index management operations via HTTP interface e.g. creat, delete, modify schema of an index. Solr cannot create a new index based on an existing one. This helps because:

* Its easier to test 
* Control of the index is with the application,
* Temporary indexes for A/B or integration testing is feasible 

Furtheremore, modelling data is improved by nested documents 

__Solr__
```json
{
"title": "foo",
"format": "exampleformat",
"section": "examplesection",
"additional_links__title": ["a", "b],
"additional_links__link": ["/a", "/b"]
}
https://some.url.com/1234567
```
__Elasticsearch__
```javascript
{
"title": "Example title",
"format": "Answer",
"section": "Example",
"additional_links": [
{"title": "title one", "link": "/one"},
{"title": "title two", "link": "/two"}
]
}
https://some.url.com/1234567
```


Here Solr document shows proposed nested additional links, the elasticsearch document proposes how to model it. 

### Results performance


Results are more verbose but provide clarity and return a lot faster than Solr. 

<img src='/assets/images/performance.png' style='display: block; margin: 0 auto'>

Source:_https://gds.blog.gov.uk/2012/08/03/from-solr-to-elasticsearch_


# Exploring Elasticsearch

I have installed elasticsearch locally from elastic.co, the head and marvel plugins, plus Kibana - time to explore.

## PUT

This creates data:

```javascript
PUT my_deezer/song/1
{
"title": "Doing All Right",
"artist": "Queen",
"album": "Queen",
"year": 1973,
"time": 269
}

PUT my_deezer/song/2
{
"title": "You're My Best Friend",
"artist": "Queen",
"album": "A Night at the Opera",
"year": 1975,
"time": 172
}

PUT my_deezer/song/3
{
"title": "Hall of the Mountain King",
"artist": "Savatage",
"album": "Hall of the Mountain King",
"year": 1985,
"time": 355
}

PUT my_deezer/song/4
{
"title": "Peace of Mind",
"artist": "Boston",
"album": "Boston",
"year": 1976,
"time": 302
}

PUT my_deezer/song/5
{
"title": "Faithfully",
"artist": "Journey",
"album": "Frontiers",
"year": 1983,
"time": 269
}
```

<style>
img {
  border: 1px solid #555;
}
</style>


<img src='/assets/images/elasticsearch.png' style='display: block; margin: 0 auto'>


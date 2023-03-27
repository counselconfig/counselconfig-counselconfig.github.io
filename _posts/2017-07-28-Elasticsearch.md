---
layout: post
title: Elasticsearch
tags: Elasticsearch database AWS Java Apache
---

# CRUD Elasticsearch

I have installed elasticsearch locally from elastic.co, the head and marvel plugins, plus Kibana - time to explore.

## PUT

This creates data:

```
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


<img src='/assets/images/elasticsearch.png' width='100%' height='100%' style='display: block; margin: 0 auto'>

More to come...
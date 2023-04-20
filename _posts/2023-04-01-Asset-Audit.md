---
layout: post
title: Asset Audit
---

### Querysearch for Asset Audting

I have been tasked to develop an application for The National Archives that allows its IT Department to monitor and update the technology assets of its staff. 

Having decided to implement the software with Django framework, this is what I have thus far:

<video loop="true" muted autoplay controls>
    <source src="/assets/videos/assetaudit.mp4" type="video/mp4">
</video>

Note the `q` URL parameter in `[20/Apr/2023 08:59:03] "GET /?q=foo.bar%40nationalarchives.gov.uk HTTP/1.1" 200 2165`

This is essentially calling my function:

```python
def home(request):
    if 'q' in request.GET:
        q=request.GET['q']
        posts=Post.objects.filter(Email_address__exact=q)
    else:
        posts=''
    # Pagintion
    paginator=Paginator(posts,2)
    page_number=request.GET.get('page')
    posts_obj=paginator.get_page(page_number)
    return render(request,'home.html',{'posts':posts_obj})
```

So in view I have a dict `request.GET` and `q` is the key and variable is the value:

```javascript
{'q': 'variable'}
```

The Django `QuerySet` is built up as a list of objects making it easier to get the data you actually need, by allowing you to filter and order the data at an early stage.
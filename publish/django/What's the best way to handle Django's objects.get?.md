---
title: What's the best way to handle Django's objects.get?
date: 2019/1/1 0:0:0
updated: 2019/7/4 14:40:25
comments: true
tags:
- python
- django
categories:
- tool kits
---


# What's the best way to handle Django's objects.get?

## 1. Use the model DoesNotExist exception

```python
try:
    thepost = Content.objects.get(pk=number)
except Content.DoesNotExist:
    thepost = None
```



##  2. use the Django shortcut function [`get_object_or_404`](https://docs.djangoproject.com/en/stable/topics/http/shortcuts/#get-object-or-404) instead of the API directly

```
from django.shortcuts import get_object_or_404

thepost = get_object_or_404(Content, pk=number)
```
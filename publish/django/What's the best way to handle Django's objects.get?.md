---
title: Hello World
date: 2013/7/13 20:46:25
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
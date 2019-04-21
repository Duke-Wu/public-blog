## Django custom exception handlers are easy!

Today I'll tell you how to create custom exception handlers in Django. They are useful, if you want to do custom action when Exception is raised from view. For example, you can create consistent way of handling your own generic exception type. It's very easy, but can be super useful!

To show how it's working, **we'll create helpful handler that redirects user to previous page and displays message when business logic error occurs.**

## Code

```python
# exceptions.py
class BusinessLogicException(Exception):
    pass


# responses.py
from django.http import HttpResponseRedirect

class RedirectToRefererResponse(HttpResponseRedirect):
    def __init__(self, request, *args, **kwargs):
        redirect_to = request.META.get('HTTP_REFERER', '/')
        super(RedirectToRefererResponse, self).__init__(
            redirect_to, *args, **kwargs)


# error_handlers.py
from django.contrib import messages
from django.utils.deprecation import MiddlewareMixin
from exceptions import BusinessLogicException
from responses import RedirectToRefererResponse

# Mixin for compatibility with Django <1.10
class HandleBusinessExceptionMiddleware(MiddlewareMixin):
    def process_exception(self, request, exception):
        if isinstance(exception, BusinessLogicException):
            message = "Invalid operation %s" % unicode(exception)
            messages.error(request, message)
            return RedirectToRefererResponse(request)
```

We also must add our handler to settings

```python
# MIDDLEWARE_CLASSES in Django < 1.10
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    # our custom exception handler
    'myapp.error_handlers.HandleBusinessExceptionMiddleware'
]
```

Usage in views (**but you can also raise exception in any function called from view, in forms, and even in other middleware**):

```python
from exceptions import BusinessLogicException

def get_secret_data(user):
    if not user.can_view_secrets:
        raise BusinessLogicException(
            "You are too old, go away!")
    return user.secrets

def main_view(request):
    secrets = get_secret_data(request.user)
    return TemplateResponse(
        request, "secrets.html", 
        context={'secrets': secrets})
```

As you can see, this code is *very* readable. We moved exception handling to middleware, so now we can just check conditions and raise exception if needed. Of course middleware should only catch generic exceptions, not related to single view.

You can use it in many other ways, too. My ideas is logging exceptions to external service or presenting exception to staff user in readable way.

That's all. Have fun with Python & Django!



## 参考

https://rock-it.pl/custom-exception-handler-in-django/
# Django: 
https://docs.djangoproject.com/en/4.1/intro/tutorial06/
# Django.shortcuts module: 
The package django.shortcuts collects helper functions and classes that “span” multiple levels of MVC. In other words, these functions/classes introduce controlled coupling for convenience’s sake.
# Django.urls module:
Returns an element for inclusion in urlpatterns. For example:

from django.urls import include, path

urlpatterns = [
    path('index/', views.index, name='main-view'),
    path('bio/<username>/', views.bio, name='bio'),
    path('articles/<slug:title>/', views.article, name='article-detail'),
    path('articles/<slug:title>/<int:section>/', views.section, name='article-section'),
    path('blog/', include('blog.urls')),
    ...
]
# Django.contrib module :
Django’s standard library lives in the package django.contrib. Within each subpackage is a separate piece of add-on functionality. These pieces are not necessarily related, but some django.contrib subpackages may require other ones.

There’s no hard requirement for the types of functionality in django.contrib. Some of the packages include models (and hence require you to install their database tables into your database), but others consist solely of middleware or template tags.

The single characteristic the django.contrib packages have in common is this: if you were to remove the django.contrib package entirely, you could still use Django’s fundamental features with no problems. When the Django developers add new functionality to the framework, they use this rule of thumb in deciding whether the new functionality should live in django.contrib or elsewhere.

django.contrib consists of these packages:

admin: The Django admin site. See Chapter 6.
admindocs: Auto-documentation for the Django admin site. This book doesn’t cover this feature; check the official Django documentation.
auth: Django’s authentication framework. See Chapter 14.
comments: A comments application. This book doesn’t cover this feature; check the official Django documentation.
contenttypes: A framework for hooking into “types” of content, where each installed Django model is a separate content type. This framework is used internally by other “contrib” applications and is mostly intended for very advanced Django developers. Those developers should find out more about this application by reading the source code in django/contrib/contenttypes.
csrf: Protection against Cross-Site Request Forgery (CSRF). See the later section titled “CSRF Protection.”
databrowse: A Django application that lets you browse your data. This book doesn’t cover this feature; check the official Django documentation.
flatpages: A framework for managing simple “flat” HTML content in a database. See the later section titled “Flatpages.”
formtools: A number of useful higher-level libraries for dealing with common patterns in forms. This book doesn’t cover this feature; check the official Django documentation.
gis: Extensions to Django that provide for GIS (Geographic Information Systems) support. These, for example, allow your Django models to store geographic data and perform geographic queries. This is a large, complex library and isn’t covered in this book. See http://geodjango.org/ for documentation.
humanize: A set of Django template filters useful for adding a “human touch” to data. See the later section titled “Humanizing Data.”
localflavor: Assorted pieces of code that are useful for particular countries or cultures. For example, this includes ways to validate U.S. ZIP codes or Icelandic identification numbers.
markup: A set of Django template filters that implement a number of common markup languages. See the later section titled “Markup Filters.”
redirects: A framework for managing redirects. See the later section titled “Redirects.”
sessions: Django’s session framework. See Chapter 14.
sitemaps: A framework for generating sitemap XML files. See Chapter 13.
sites: A framework that lets you operate multiple Web sites from the same database and Django installation. See the next section, “Sites.”
syndication: A framework for generating syndication feeds in RSS and Atom. See Chapter 13.
webdesign: Django add-ons that are particularly useful to Web designers (as opposed to developers). As of this writing, this included only a single template tag, {% lorem %}. Check the Django documentation for information.
# Bootstrap navbar: 
Navbars require a wrapping .navbar with .navbar-expand{-sm|-md|-lg|-xl} for responsive collapsing and color scheme classes.
Navbars and their contents are fluid by default. Use optional containers to limit their horizontal width.
Use our spacing and flex utility classes for controlling spacing and alignment within navbars.
Navbars are responsive by default, but you can easily modify them to change that. Responsive behavior depends on our Collapse JavaScript plugin.
Navbars are hidden by default when printing. Force them to be printed by adding .d-print to the .navbar. See the display utility class.
Ensure accessibility by using a <nav> element or, if using a more generic element such as a <div>, add a role="navigation" to every navbar to explicitly identify it as a landmark region for users of assistive technologies.

# Django function views:


Here’s a view that returns the current date and time, as an HTML document:
from django.http import HttpResponse
import datetime

def current_datetime(request):
    now = datetime.datetime.now()
    html = "<html><body>It is now %s.</body></html>" % now
    return HttpResponse(html)
Let’s step through this code one line at a time:

First, we import the class HttpResponse from the django.http module, along with Python’s datetime library.

Next, we define a function called current_datetime. This is the view function. Each view function takes an HttpRequest object as its first parameter, which is typically named request.

Note that the name of the view function doesn’t matter; it doesn’t have to be named in a certain way in order for Django to recognize it. We’re calling it current_datetime here, because that name clearly indicates what it does.

The view returns an HttpResponse object that contains the generated response. Each view function is responsible for returning an HttpResponse object. (There are exceptions, but we’ll get to those later.)

Django’s Time Zone

Django includes a TIME_ZONE setting that defaults to America/Chicago. This probably isn’t where you live, so you might want to change it in your settings file.
Mapping URLs to views¶

So, to recap, this view function returns an HTML page that includes the current date and time. To display this view at a particular URL, you’ll need to create a URLconf; see URL dispatcher for instructions.

Returning errors¶

Django provides help for returning HTTP error codes. There are subclasses of HttpResponse for a number of common HTTP status codes other than 200 (which means “OK”). You can find the full list of available subclasses in the request/response documentation. Return an instance of one of those subclasses instead of a normal HttpResponse in order to signify an error. For example:

from django.http import HttpResponse, HttpResponseNotFound

def my_view(request):
    # ...
    if foo:
        return HttpResponseNotFound('<h1>Page not found</h1>')
    else:
        return HttpResponse('<h1>Page was found</h1>')
There isn’t a specialized subclass for every possible HTTP response code, since many of them aren’t going to be that common. However, as documented in the HttpResponse documentation, you can also pass the HTTP status code into the constructor for HttpResponse to create a return class for any status code you like. For example:

from django.http import HttpResponse

def my_view(request):
    # ...

    # Return a "created" (201) response code.
    return HttpResponse(status=201)
Because 404 errors are by far the most common HTTP error, there’s an easier way to handle those errors.

The Http404 exception¶

class django.http.Http404¶
When you return an error such as HttpResponseNotFound, you’re responsible for defining the HTML of the resulting error page:

return HttpResponseNotFound('<h1>Page not found</h1>')
For convenience, and because it’s a good idea to have a consistent 404 error page across your site, Django provides an Http404 exception. If you raise Http404 at any point in a view function, Django will catch it and return the standard error page for your application, along with an HTTP error code 404.

Example usage:

from django.http import Http404
from django.shortcuts import render
from polls.models import Poll

def detail(request, poll_id):
    try:
        p = Poll.objects.get(pk=poll_id)
    except Poll.DoesNotExist:
        raise Http404("Poll does not exist")
    return render(request, 'polls/detail.html', {'poll': p})
In order to show customized HTML when Django returns a 404, you can create an HTML template named 404.html and place it in the top level of your template tree. This template will then be served when DEBUG is set to False.

When DEBUG is True, you can provide a message to Http404 and it will appear in the standard 404 debug template. Use these messages for debugging purposes; they generally aren’t suitable for use in a production 404 template.

Customizing error views¶

The default error views in Django should suffice for most web applications, but can easily be overridden if you need any custom behavior. Specify the handlers as seen below in your URLconf (setting them anywhere else will have no effect).

The page_not_found() view is overridden by handler404:

handler404 = 'mysite.views.my_custom_page_not_found_view'
The server_error() view is overridden by handler500:

handler500 = 'mysite.views.my_custom_error_view'
The permission_denied() view is overridden by handler403:

handler403 = 'mysite.views.my_custom_permission_denied_view'
The bad_request() view is overridden by handler400:

handler400 = 'mysite.views.my_custom_bad_request_view'
See also

Use the CSRF_FAILURE_VIEW setting to override the CSRF error view.
Testing custom error views¶

To test the response of a custom error handler, raise the appropriate exception in a test view. For example:

from django.core.exceptions import PermissionDenied
from django.http import HttpResponse
from django.test import SimpleTestCase, override_settings
from django.urls import path


def response_error_handler(request, exception=None):
    return HttpResponse('Error handler content', status=403)


def permission_denied_view(request):
    raise PermissionDenied


urlpatterns = [
    path('403/', permission_denied_view),
]

handler403 = response_error_handler


# ROOT_URLCONF must specify the module that contains handler403 = ...
@override_settings(ROOT_URLCONF=__name__)
class CustomErrorHandlerTests(SimpleTestCase):

    def test_handler_renders_template_response(self):
        response = self.client.get('/403/')
        # Make assertions on the response here. For example:
        self.assertContains(response, 'Error handler content', status_code=403)
Async views¶

As well as being synchronous functions, views can also be asynchronous (“async”) functions, normally defined using Python’s async def syntax. Django will automatically detect these and run them in an async context. However, you will need to use an async server based on ASGI to get their performance benefits.

Here’s an example of an async view:

import datetime
from django.http import HttpResponse

async def current_datetime(request):
    now = datetime.datetime.now()
    html = '<html><body>It is now %s.</body></html>' % now
    return HttpResponse(html)
You can read more about Django’s async support, and how to best use async views, in Asynchronous support.

 URL dispatcherView decorators 
Additional Information
Support Django!

Support Django!
The Inn At New Hyde Park donated to the Django Software Foundation to support Django development. Donate today!
Contents

Writing views
A simple view
Mapping URLs to views
Returning errors
The Http404 exception
Customizing error views
Testing custom error views
Async views
Browse

Prev: URL dispatcher
Next: View decorators
Table of contents
General Index
Python Module Index
You are here:

Django 4.1 documentation
Using Django
Handling HTTP requests
Writing views
Getting help

FAQ
Try the FAQ — it's got answers to many common questions.
Index, Module Index, or Table of Contents
Handy when looking for specific information.
django-users mailing list
Search for information in the archives of the django-users mailing list, or post a question.
#django IRC channel
Ask a question in the #django IRC channel, or search the IRC logs to see if it’s been asked before.
Django Discord Server
Join the Django Discord Community.
Official Django Forum
Join the community on the Django Forum.
Ticket tracker
Report bugs with Django or Django documentation in our ticket tracker.
Download:

Offline (Django 4.1): HTML | PDF | ePub 
Provided by Read the Docs.





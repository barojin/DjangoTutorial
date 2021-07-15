# Django Tutorial, making a polls app.

# [Tuto 01](https://docs.djangoproject.com/en/3.2/intro/tutorial01/)
```
python -m pip install -U Django
django-admin startproject mysite
python manage.py runserver
```
Don't use django or test for the name(mysite)

[manage.py](https://docs.djangoproject.com/en/3.2/ref/django-admin): Acommand-line utility to interact with Django project.   
[url.py](https://docs.djangoproject.com/en/3.2/topics/http/urls/).
```
python manage.py runserver 8080
python manage.py runserver 1.2.3.4:8080
```

Automatic reloading of runserver

urlpatterns = [
    path('polls/', include('polls.urls')),
]

path() function is passed four arguments: route (required), view (required), kwargs (optional), name (optional).
* route is a string that contains a URL pattern. 
* view is called when route is correct and gets an HttpRequest object, captured value as keyword arguments.
* kwargs is an arbitrary keyword arguments can be passed in a dictionary to the target view. 
* name let URL refer to view unambiguously.

# [Tuto 02](https://docs.djangoproject.com/en/3.2/intro/tutorial02/)
## Database setup 
- DATABASES in mysite/settings.py  
INSTALLED_APPS holds the names of Django apps that are activated in the Django instance.  

```python manage.py migrate``` that propagate changes of models into the database schema.

DRY: Don't repeat your self.

To activate the polls app,
put 'polls.apps.PollsConfig' in INSTALLED_APPS in myste/settings.py  
and run `pyhton manage.py makemigrations polls`: let django know the change to models

If you want to see SQL, run `python manage.py sqlmigrate polls 0001`: It takes migration names and returns their SQL

python manage.py check
:It chekcs for any problems in your project without making migrations or touching the database.

To make model changes:
1. change your models(in models.py)
2. Run 'pyhton manage.py makemigrations' to create migrations for those changes
3. Run 'python manage.py migrate' to apply those changes to the database.

```
def __str__(self):
    return self.question_text
```
objects' representations are used throughout Django's automatically-generated admin. 

## Creating an admin user
```
python manage.py createsuperuser
```
in polls/admin.py
```
from django.contrib import admin

from .models import Question

admin.site.register(Question)
```

# [Tuto 3](https://docs.djangoproject.com/en/3.2/intro/tutorial03/)
- views.py
- urls.py
## views
view return HttpResponse or exception.
- connect the view and html, `template_name = 'polls/index.html'`
- render(request object, template name, dictionary) return HttpResponse
```
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```
- django.shortcuts.get_object_or_404 that is used for loose coupling rather making ObjectDoesNotExist. 
```
from django.shortcuts import get_object_or_404, render

from .models import Question
# ...
def detail(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    return render(request, 'polls/detail.html', {'question': question})
```
- removing hardcoded URLS
`<a href="/polls/{{ question.id }}/">{{ question.question_text }}</a>` 
to 
`<a href="{% url 'detail' question.id %}">{{ question.question_text }}</a>`

## Namespacing URL names
To differentiate the URL names between different apps, such as detail,
in urls.py 
`app_name = 'polls'` 
`<a href="{% url 'detail' question.id %}">{{ question.question_text }}</a>` 
to
`<a href="{% url 'polls:detail' question.id %}">{{ question.question_text }}</a>`

# [Tuto 4](https://docs.djangoproject.com/en/3.2/intro/tutorial04/)
- To alter data server-side, use method="post". 

- {% csrf_token %} protect post data againt Cross Site Reuqest Forgeries.
<form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
</form>

- A submitted data from detail.html. to polls:vote. 
def vote(reuest, question_id) takes the submitted data.  
request.POST = dictionary and return strings e.g. string_data = request.POST['choice']. 

# [Tuto 5](https://docs.djangoproject.com/en/3.2/intro/tutorial05/#top)
- No one trust the code without testing.
- Test-driven development: Write the test plan and then the code and replenish tests.
- Put the test of every change of the piece of code

Django, 
automated test = tests.py
Write the polls/tests.py
Run: python manage.py test polls

Good rules-of-thumb for the test of Django
- a separate TestClass for each model or view.
- a separate test method for each set of conditions you want to test
- test method names that describe their function

Further testing
- Use Selenium to test the way the HTML renders in a browser.
- Contiuous integration that run tests automatically with every commit
- Integration with coverage.py that describes how much source code has been tested. So cover your code thoroughly with coverage.py.
    - run 'pip install coverage' and 'coveragerun --source='.'manage.py test polls' and '
coverage report'

# [Tuto 6](https://docs.djangoproject.com/en/3.2/intro/tutorial06/)

STATICFILES_FINDERS setting, defaults is AppDirectoriesFinder which looks for a static subdirectory in each of the INSTALLED_APPS. Admin site is too.

Managing static files (e.g. images, JavaScript, CSS)
https://docs.djangoproject.com/en/3.2/howto/static-files/ 

The staticfiles app
https://docs.djangoproject.com/en/3.2/ref/contrib/staticfiles/ 

Deploying static files
https://docs.djangoproject.com/en/3.2/howto/static-files/deployment/ 

# [Tuto 7](https://docs.djangoproject.com/en/3.2/intro/tutorial07/#top)
modification of admin page 
fieldsets
admin.StackedInline
admin.TabularInline

list_display = ('question_text', 'pub_date', 'was_published_recently')
# list_display has the event listener that click the header = sort
https://docs.djangoproject.com/en/3.2/ref/contrib/admin/#django.contrib.admin.ModelAdmin.list_display

APP_DIRS = TRUE
Django looks for a templates/ subdirectory within each application package, for use as a fallback.
django.contrib.admin is an application

template loading documentation
https://docs.djangoproject.com/en/3.2/topics/templates/#template-loading

# [Tuto Advanced](https://docs.djangoproject.com/en/3.2/intro/reusable-apps/)
## Naming convention and rules:
- avoid naming conflicts with others in PyPI
- django-'name' = django specific module
- unique name in INSTALLED_APPS
## Standalone Python package
- create a directory django-'name'
- write README.rst file.
- create LICENSE
- create setup.cfg and setup.py
- create MANIFEST.in file 
- put the detailed docs in the directory django-'name'/docs
- build the package with ```python setup.py sdist``` then it creates a dist/name-version.tar.gz
## Posting  
- Post the package on a public repository, such as the Python Package Index (PyPI). packaging.python.org has a good [tutorial](https://packaging.python.org/tutorials/packaging-projects/#uploading-the-distribution-archives) for doing this.
- [venv](https://docs.python.org/3/tutorial/venv.html) is the good solution when work with several Django projects. Since this tool allows to maintain multiple isolated Python environments, each with its own copy of the libraries and package namespace.

# [Patching](https://docs.djangoproject.com/en/3.2/intro/contributing/)
Look at the link for the detail.
In brief, we fork Django on Github and make the virtual environment to run. 
Create a new branch for making changes, and use the Django test suite for the regression test.
After all test cases are passed, write the documentation that one is for the new feature and another for the release note. Refer this link for documenting (https://docs.djangoproject.com/en/3.2/internals/contributing/writing-documentation/).
And git commit, push on your local terminal, and do a pull request on Django GitHub page. 
For the detail of the git use for the Django, see it (https://docs.djangoproject.com/en/3.2/internals/contributing/writing-code/working-with-git/).

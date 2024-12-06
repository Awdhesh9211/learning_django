# Chai Aur Code Project ☕💻

Welcome to the **Chai Aur Code** project! This project is a Django-based web application to manage chai varieties, reviews, and provide a smooth user interface using **Tailwind CSS**. Below is the guide to set up, develop, and manage your project.

---

## 🛠️ Project Setup

###  Install Required Packages
First, install the necessary packages to set up the environment:
```bash
pip install uvicorn
uv env
source ./env/bin/activate
deactivate
uv pip install django
```
## 1️⃣ Create a New Project
```bash
django-admin startproject chaiaurDjango
```
## 2️⃣ Run the Project
```bash
python manage.py runserver
```
## 3️⃣ Run the Server on a Different Port
```bash
django-admin startproject chaiaurDjango
```
## ⚠️ SECURITY WARNING: Don't Run with Debug Turned On in Production!
```bash
DEBUG = False  # Change to False for production
```
# 📁 Template and Static Files Setup

## 1️⃣ Create Template and Static and Static Folders

### Template/Static/Media Folder Structure
```bash 
./template
./static
./media
```
### rootapp/setting.py
```bash
TEMPLATES =[{
  'DIRS': [os.path.join(BASE_DIR,"templates")],
}]

STATIC_URL = 'static/'
STATICFILES_DIRS=[os.path.join(BASE_DIR,'static')]

MEDIA_URL='/media/'
MEDIA_ROOT= os.path.join(BASE_DIR,'media')
```
### rootapp/urls.py
```python
from django.contrib import admin
from django.urls import path
from django.conf import settings
from django.conf.urls.static import static
from base.views import *

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', HomeView,name="Home"),
    path('book_table', BookTableView,name='Book_Table'),
    # path('menu', MenuView,name='Menu'),
    path('menu/<str:category>', MenuView,name='Menu'),
    path('about', AboutView,name='About'),
    path('feedback', FeedbackView,name='Feedback_Form'),
]
if settings.DEBUG:
    urlpatterns += static(settings.STATIC_URL,document_root=settings.STATIC_ROOT)
    urlpatterns += static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)
```
# 2.rendering a page    -- views
```python
from django.http import HttpResponse
from django.shortcuts import render

def home(request):
    return render(request,"pages/index.html")

def about(request):
    return render(request,'pages/about.html')

def contact(request):
    return render(request,'pages/contact.html')
```
# 3.creating a app 
```document
    $ 1> python manage.py startapp chai
    $ 2> go to project setting.py and add this app in installe app array
    $ 3> create a urls.py 
    $ 4>after creatin g app include the urls of app in project 
```
# 4.using tailwind with django 
## STEP 1
```cmd
    pip install django-tailwind 
    pip install 'django-tailwind[reload]'
           -if you use uv environment virtual then first install pip 
           -python -m ensurepip --upgrade   or
           -python -m pip install --upgrade pip   
           -pip install 'django-tailwind[reload]'
           -add tailwind in setting.py
```
### STEP 2
```base
    python manage.py tailwind init    
```
### STEP 3 setting.py  add  middleware and config and app
```python
    "django_browser_reload.middleware.BrowserReloadMiddleware"
    TAILWIND_APP_NAME = 'theme'
    INTERNAL_IPS=['127.0.0.1']
    NPM_BIN_PATH='C:/bin/npm'
```
### STEP 4 
```base
python manage.py tailwind install
    include the jinja temlate for tailwind

    open new terminal for tailwind 
            >python manage.py tailwind start
    urls.py 
            >at last 
            path('__reload__/',include("django_browser_reload.urls"))
```

# 5. migrate 
```base 
   >python manage.py migrate
   >python manage.py createsuperuser 
      .. give username,email,password,password again 
      .. start server and go to /admin login 
   >by this doing you can write your model
```


# 6. handle model and urls 
```python
    1>in setting.py 
# Database
# https://docs.djangoproject.com/en/5.1/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
    2>models.py
from django.db import models
from django.utils import timezone

# Create your models here.
class ChaiVarity(models.Model):
    CHAI_TYPE_CHOICE=[
        ('ML',"MASALA"),
        ('GR',"GIGER"),
        ('KT',"KIWI"),
        ('PL',"PLAIN"),
        ('EL',"ELAICHI"),
    ]
    name=models.CharField(max_length=100)
    image=models.ImageField(upload_to='chais/')
    date_added=models.DateTimeField(default=timezone.now)
    type=models.CharField(max_length=2,choices=CHAI_TYPE_CHOICE)
    
    3>setting.py
MEDIA_URL='/media/'
MEDIA_ROOT= os.path.join(BASE_DIR,'media')

    4> root urls.py
"""
URL configuration for chaiaurDjango project.

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/5.1/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path , include
from django.conf import settings
from django.conf.urls.static import static


from . import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path("",views.home,name="home"),
    path("about/",views.about,name="about"),
    path("contact/",views.contact,name="contact"),
    path("chai/",include('chai.urls'))
] + static(settings.MEDIA_URL,document_root=settings.MEDIA_ROOT)

    5> migration done for the created model 
    - create 
          python manage.py makemigrations chai
    - migrate
          python manage.py migrate 
    
    6>visulaize through the admin 
    admin.py 
    -register
from django.contrib import admin
from chai.models import ChaiVarity

# Register your models here.
admin.site.register(ChaiVarity)
     .. now se the DB in Admin 

# 12. CRUD
 <a href="{% url 'chai_detail' chai.id %}">
            <button class="mt-4 px-4 py-2 bg-blue-500 text-white font-semibold rounded-lg hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-opacity-50 w-full">
                {{ chai.type }} - {{ chai.id }}
              </button>
        </a>
urlpatterns = [
    path("",views.all_chai,name="chai_home"),
    path("<int:chai_id>/",views.chai_detail,name="chai_detail")
]
from django.shortcuts import render ,get_object_or_404
from chai.models import ChaiVarity

# Create your views here.
def all_chai(request):
    chais=ChaiVarity.objects.all()
    return render(request,'chai/all_chai.html',{'chais':chais})

def chai_detail(request, chai_id):
    chai=get_object_or_404(ChaiVarity , pk=chai_id)
    return render(request, 'chai/detail.html',{'chai':chai})

# 13.Relationship 
    One-to-Many 
             - chai ke review
```


    










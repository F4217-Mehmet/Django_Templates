Bu projede restframework apiler yerine django templateler kullanacağım. Normal html sayfası yazacağım.
Static files: Kolay kolay değiştirmeyeceğim dosyalar, Css, images vb.

Kurulum:Standart kurulum yapıyorum. Ancak pip install django diyorum, çünkü restframework kullanmayacağım.
pip freeze diyerek yüklendiğinden emin oluyorum
pip freeze > requirements.txt 
gitignore dosyası oluşturuyorum
django-admin startproject main .
python manage.py startapp students
students'ı installed apps'e ekliyorum

**migrations'ı** yeni bir model oluşturduğumda yada bir modelde değişiklik yaptığımda bunların django tarafından algılanması için çalıştırıyorum. Bana migrations file'ı oluşturuyor.
**migrate** dediğimde de oluşan migrations filelardaki değişiklikler db'ye yansıtılıyor.

**DJANGO TEMPLATE NASIL ÇALIŞIYOR?** internal bir yapı. viewde içerisinde oluşturduğumuz datalar, Djangonun template engine'inden geçerek frontenddeki html'ye gönderilir. Burada Django Template Language (DTL) ile bu dataları yakalayıp okutabiliyorum, gösterebiliyorum. Burada arada **serializer** yok.

**views**de bir home func. yazıyorum.

from django.shortcuts import render
from django.http import HttpResponse

def home(request):

    context = {
        'title': 'clarusway',
        'desc': 'This is a description',
        'number': 1111,
        'list1': ['a', 1, 'b', ['c', 2]],
        'dict1': {
            'key1': 'value1',
            'key2': 'value2'
        },
        'dict_list': [
            {'name': 'zed', 'age': 25},
            {'name': 'amy', 'age': 21},
            {'name': 'joe', 'age': 50},
        ]
    }

    return render(request, 'students/home.html', context)

    # return HttpResponse('<p> Hello </p>')

'''
{{ variables }}
{% command %}
| ---> filter
'''

**urls.py (main içerisinde)**

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('students.urls'))
]

**urls.py (students içerisinde)**
students içinde urls.py dosyası açıyorum.

from django.urls import path
from .views import home

urlpatterns = [
    path('', home, name='home'),
]

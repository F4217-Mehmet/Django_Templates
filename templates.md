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

**students views**de bir home func. yazıyorum.

from django.shortcuts import render
from django.http import HttpResponse

def home(request):
**burayı render'ı tıklıyoruz ve render fonksiyonunun yapısına göre yapıyoruz**
**context ismi farklı olabilir ama dictionary yapısında olması gerekir. bu ismi aşağıda return render içerisinde belirtiyorum.**    
    context = {
        'title': 'coredinat',
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
**'students/home.html'= template name**
**django diyor ki; sen bunu templates altında oluşturduğun için ben bunu otomatik olarak algılarım, ayrıca belirtmene gerek yok. ben bu viewe istek geldiğinde templateslerin altına sırayla bakacağım. Home olanı yakalayacağım ve bunu render edeceğim diyor. Ana directoryde de templates klasörü oluşturabiliriz ve öncelikle buraya bakar**
**bunun için templates içinde students ve onun içinde home oluşturduk ve viewsde template name olarak bu şekilde yazdık** **best practice bu şekilde!!!!**
**ana root içerisince templates klasörü oluştururken, settings.py'da TEMPLATES içerisinde DIRS'da bunu belirtiyorum. ismi farklı da olabilirdi. o zaman o şekilde belirtmemiz gerekirdi**

    # return HttpResponse('<p> Hello </p>')

'''
{{ variables }}
{% command %}  **burada, pythondaki gibi for loop, if condition yazabiliriz, yazarken buradaki gibi {% %} yazacağım ve command yerine de if, for ne kullanacaksam onu yazacağım. Yine mesela for yazdıysam bittikten sonra {% endfor %} yazmam gerekir, yoksa hata verir.**
| ---> filter **bu işaret de (|) filter için kullanılıyor**
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

**TEMPLATES**
students içerisinde templates klasörü oluşturuyorum. İsim önemli, bu şekilde yazmam gerekiyor. Templates içindeki html sayfalarını otomatik olarak algılıyor.

**(students/templates/homes.html) dosyası oluşturuyorum ve çift süslü içerisinde frontendde göstereceğim şeyleri yazıyorum**
**burada bazı alıştırmalar yapacağım**

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Hello this is home page.....</h1>
    <h2>{{ title }}</h2>
    <p>{{ desc }}</p>
    <p>{{ number }}</p>
    <hr>

**iterable objelere .notation ile ulaşıyoruz. [] kullanmıyoruz**
    <p>{{ list1 }}</p>
    <p>{{ list1.1 }}</p> 
    <p>{{ list1.3.1 }}</p> 
    <hr>
    <p>{{ dict1}}</p>
    <p>{{ dict1.key1 }}</p>
    <hr>
    <ul>
        {% for i in list1 %}
        <li>{{ i }}</li> **değişken olduğu için çift süslü içine aldık**
        {% endfor %}
    </ul>

    <hr>
        {% if number == 1112 %}
        <p>
            Henry
        </p>
        {% else %}
        <p>
            Victor
        </p>
        {% endif %}
    <hr>
**2 ekledim**
    <p>add value : {{ number|add:"2" }}</p> 
    <p>value : {{ number }}</p>
    <hr>
    <p>capfirst : {{ title|capfirst }}</p> **ilk harfi büyük hale getirdim**
    <hr>
    {{ dict_list|dictsort:"age" }} **age'e göre sıralıyorum**
    <hr>
    {{ desc|truncatechars:7 }} **ilk 7 karakterini göster**
    
</body>
</html>

**INHERITANCE**
component yapısına benzer şekilde, tek bir yerden temel sayfa yapısını oluşturup buradan inherit ederek başka sayfalar oluşturabiliriz. buradaki değişiklik her yeri otomatik olarak günceller.
navbar.html, footer.hmtl gibi değişmeyecek, her yerde kullanacağımız standart template yapılarını dışarıda root klasörde tanımladığım templates klasörünün altında tanımlıyorum.
Templates'in altında **base.html** dosyası oluşturdum:

{% load static %} **static file'ları kullanmak için zorunlu olarak yazmamız gereken komut**
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

**her yerde farklı css kullanabilmek için böyle yaptım**
    {% block link %} 
    <link rel="stylesheet" href="{% static 'main.css' %}">
    {% endblock link %}
    
    <title>{% block title %}students{% endblock title %}</title>

</head>
<body>

**bütün base'i çekmesin istersen django blocklar geliştirmiş, block adı veriyorum**
    {% block body %} 
    
    {% endblock body %} 
**burada da if, for'da olduğu gibi block'u da kapatıyorum**
**buranın mantığı, home'a gidip blocklar arasında farklı şeyler yazınca, base'den glene herşeyi al ama blocklar arasında home'da belirttiğim şeyi yaz demektir**
</body>
</html>

**students/templates/students/home.html oluşturuyorum** çünkü viewde home.html'i render ediyorum.
base.html structure'ını burada kullanmak istiyorum:

{% extends 'base.html' %} **base.html'de ne varsa inherit et diyorum**
{% load static %} **bunu yazmazsan hata verir**
{% block link %} **her sayfaya özel css verebilirim**
    <link rel="stylesheet" href="{% static 'students/main.css' %}">
{% endblock link %}
{% block title %} **her sayfada farklı title verebilirim**
home
{% endblock title %}

**base'den gelen herşeyi al ama bodyler arasındaki bu kısmı bu şekilde yaz diyorum**
{% block body %}
<h1>Hello this is home page.....</h1> 
{% endblock body %}

**STATICS**
**settings.py'da static kısmında**

STATIC_URL = 'static/'
STATICFILES_DIRS = [  **buraya birden çok yol yazabilirim. Bunu yazdığım için önce folderdaki staticlere sonra app içerisindeki staticlere bakıyor**
    BASE_DIR / 'static'
]
tanımlıyorum.

**burada da iki ayrı static oluşturdum. Geneli ilgilendirenleri ana roottakine yazıyorum, çünkü öncelikle ona bakıyor, app'e özel staticler varsa onu da app'in içerisindeki static içerisinde tutuyorum.**
ana rootaki static altına **main.css** file'ı oluşturuyorum.

h1{
    color: blue;
}

bir de **students/static/students/main.css** oluşturdum.

h1{
    color: red;
}

**MODELS** 
**student/models.py'da Student classı oluşturuyorum:**
**HATIRLATMA NOTU**

**blank=True** form ile alakalı, formda ilgili yer boş bırakılabilir demek.

**null=True** db ile ilgili. Charfield'larda blanktrue demek yeterli ancak integer ve datefieldlarda blanktrue dediysen, nulltrue da demek zorundasın.

from django.db import models


class Student(models.Model):
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    number = models.SmallIntegerField()
    image = models.ImageField(upload_to='students', blank=True)

    def __str__(self):
        return f'{self.first_name} {self.last_name}'

**pillow** yükleyip **migration** ve **migrate** yapıyorum.
pip install pillow
python manage.py makemigrations
python manage.py migrate

**adminde görmek için admin.py'a ekliyorum**

from django.contrib import admin
from .models import Student

admin.site.register(Student)

settings.py'da **media url** ekliyorum

MEDIA_URL = 'media/'
MEDIA_ROOT = BASE_DIR / 'media'

**main urls'de**

from django.conf import settings
from django.conf.urls.static import static


urlpatterns = [
    ...
    ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
**artık media klasörünün içerisindeki fotoğrafları studentsa ekleyip görebilirim**

**şimdi bir html sayfasında öğrencileri listeliyorum**
students/view'de:

from .models import Student

def student_list(request):
    students = Student.objects.all() **db'den bu veriyi çekiyorum**
    context = {
        'students': students
    }

    return render(request, 'students/student_list.html', context)

**templates/students/student_list**

{% extends 'base.html' %}
{% block title %}
student list
{% endblock title %}

{% block body %} **base.html'de bunu body olarak tanımlamıştım**
<h2>Student List</h2>
<br>
    <ul>
        {% for student in students|dictsort:"number" %}  **numaraya göre sıraladım**
            <li>{{ student.number }} - {{ student }}</li>
        {% endfor %}          
    </ul>
{% endblock body %}

**students/urls.py**

urlpatterns = [
    ... ,
    path('list/', student_list, name='student_list'),

**student create işlemi yapacağım**

**templates içine forms.py file'ı açtım**
from django import forms
from .models import Student


class StudentForm(forms.ModelForm):
    class Meta:
        model = Student
        fields = '__all__'

**view'de**

def student_add(request):
    form = StudentForm()
    if request.method == 'POST':
        # print('POST :', request.POST)
        # print('fıles :', request.FILES)
        form = StudentForm(request.POST, request.FILES)

        if form.is_valid():
            form.save()
            return redirect('student_list')

    context = {
        'form': form
    }

    return render(request, 'students/student_add.html', context)
# Guía para Iniciar un Proyecto Simple con Django

Este documento proporciona una guía paso a paso para iniciar un proyecto simple con Django, desde la instalación hasta la configuración básica de las vistas y formularios.

## Requisitos Previos

Antes de comenzar, asegúrate de tener instalado lo siguiente en tu sistema:

- Python 3.x
- pip (el gestor de paquetes de Python)
- pipenv (para la virtualización)

## 1. Instalación de Django

Primero, instala Django utilizando `pip`.

```bash
pip install django
```
## 2. Instalación de pipenv

Si no tienes `pipenv` instalado, puedes instalarlo usando pip:

```bash
pip install pipenv
```
## 3. Crear el Entorno Virtual

Crea y activa un entorno virtual con `pipenv`:

```bash
pipenv install django
pipenv shell
```
Esto crea un entorno virtual y activa un shell interactivo dentro de él.

## 4. Iniciar el Proyecto Django

Inicia un nuevo proyecto de Django:

```bash
django-admin startproject myproject
cd myproject
```

## 5. Crear una Nueva Aplicación

Dentro del proyecto, crea una nueva aplicación:

```bash
python manage.py startapp myapp
```
## 6. Configuración de URLs

En `myproject/urls.py`, incluye las rutas de la aplicación:

```bash
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
]
```
Luego, crea un archivo urls.py dentro de la carpeta de la aplicación myapp:

```bash
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```
## 7. Configuración de la Carpeta `templates`

Dentro de la carpeta myapp, crea una carpeta llamada templates y dentro de ella, una subcarpeta con el nombre de tu aplicación (myapp). Así:

```bash
myapp/
    templates/
        myapp/
            index.html
```
Crea un archivo index.html dentro de la carpeta myapp/templates/myapp/ con el siguiente contenido básico:

```bash
<!DOCTYPE html>
<html>
<head>
    <title>My App</title>
</head>
<body>
    <h1>Bienvenido a My App</h1>
</body>
</html>
```
## 8. Configuración de la Carpeta `static`

Dentro de la carpeta myapp, crea una carpeta llamada static para tus archivos CSS, JavaScript, e imágenes.

```bash
myapp/
    static/
        myapp/
            css/
            js/
            img/
```
Agrega tus archivos de estilos en static/myapp/css/. Por ejemplo, puedes crear un archivo styles.css y enlazarlo en tus templates.

## 9. Configuración de Modelos y Migraciones

Define los modelos en myapp/models.py. Por ejemplo:

```bash
from django.db import models

class Item(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.name
```
Luego, crea y aplica las migraciones:

```bash
python manage.py makemigrations
python manage.py migrate
```

## 10. Crear un Superusuario

Para acceder al panel de administración de Django, necesitas un superusuario. Créalo con el siguiente comando:

```bash
python manage.py createsuperuser
```
Sigue las instrucciones de ka terminal para crear el usuario.

## 11. Configuración de Vistas

Define las vistas en myapp/views.py. Por ejemplo:

```bash
from django.shortcuts import render
from .models import Item

def index(request):
    items = Item.objects.all()
    return render(request, 'myapp/index.html', {'items': items})
```
## 12. Configuración de Formularios

Si necesitas formularios, crea un archivo forms.py dentro de la carpeta myapp:

```bash
from django import forms
from .models import Item

class ItemForm(forms.ModelForm):
    class Meta:
        model = Item
        fields = ['name', 'description']
```
Para implementar el formulario en una vista:

```bash
from django.shortcuts import render, redirect
from .forms import ItemForm

def create_item(request):
    if request.method == 'POST':
        form = ItemForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('index')
    else:
        form = ItemForm()
    return render(request, 'myapp/create_item.html', {'form': form})
```
## 13. Configuración de URLs con la Main App

Agrega la nueva vista a tu urls.py en myapp:

```bash
urlpatterns = [
    path('', views.index, name='index'),
    path('create/', views.create_item, name='create_item'),
]
```
Crea un template create_item.html para renderizar el formulario.

```bash
<!DOCTYPE html>
<html>
<head>
    <title>Create Item</title>
</head>
<body>
    <h1>Create New Item</h1>
    <form method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Save</button>
    </form>
</body>
</html>
```

## 14. Crear el Template base_generic.html

El archivo base_generic.html es un template base que otros templates pueden extender. Esto es útil para definir un diseño general para tu sitio web (como una cabecera y un pie de página) que se repetirá en varias páginas.

Crea el archivo base_generic.html dentro de la carpeta myapp/templates/myapp/:

```bash
myapp/
    templates/
        myapp/
            base_generic.html
```

## 15. Incluir Archivos Estáticos en el Template

Para incluir archivos estáticos (CSS, JavaScript, imágenes) en el template, necesitas asegurarte de cargar los archivos estáticos al principio del archivo. Luego, puedes enlazar los archivos en las secciones apropiadas del HTML.

Aquí hay un ejemplo de cómo configurar el archivo base_generic.html:

```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}My Site{% endblock %}</title>
    {% load static %}
    <link rel="stylesheet" type="text/css" href="{% static 'myapp/css/styles.css' %}">
</head>
<body>
    <header>
        <h1>Welcome to My Site</h1>
        <!-- Aquí puedes añadir más contenido para la cabecera -->
    </header>

    <div class="content">
        {% block content %}
        <!-- El contenido específico de la página se renderizará aquí -->
        {% endblock %}
    </div>

    <footer>
        <p>My Site &copy; 2024</p>
    </footer>

    <!-- Puedes incluir archivos JavaScript aquí -->
    <script src="{% static 'myapp/js/script.js' %}"></script>
</body>
</html>
```

## 16. Explicación de los Elementos Clave
```bash
{% load static %}: Esta etiqueta de plantilla de Django se usa para habilitar el uso de archivos estáticos en el template. Debes cargar los archivos estáticos antes de poder utilizarlos.

{% static 'myapp/css/styles.css' %}: Esta es la forma en que se hace referencia a los archivos estáticos en Django. Aquí se está incluyendo un archivo CSS ubicado en myapp/static/myapp/css/styles.css.

{% block title %}{% endblock %}: Este bloque se usa para insertar el título de la página. Puedes definir un título específico en los templates que extiendan base_generic.html.

{% block content %}{% endblock %}: Este es el bloque principal donde se insertará el contenido específico de cada página. Los templates que extiendan base_generic.html llenarán este bloque con su contenido específico.
```
## 17. Extender el Template Base en Otros Templates

Para extender el template base en otros templates, debes usar la etiqueta {% extends %} al comienzo de tu template. Aquí hay un ejemplo de cómo hacerlo:

Supongamos que tienes un template index.html que se encuentra en myapp/templates/myapp/:

```bash
{% extends 'myapp/base_generic.html' %}

{% block title %}Home Page{% endblock %}

{% block content %}
    <h2>Welcome to the Home Page</h2>
    <p>This is the main content of the homepage.</p>
{% endblock %}
```

## 18. Estructura de Archivos y Directorios

Aquí está la estructura de archivos y directorios con las configuraciones mencionadas:

```bash
myapp/
    static/
        myapp/
            css/
                styles.css
            js/
                script.js
    templates/
        myapp/
            base_generic.html
            index.html
```

## 19. Cómo Funciona el Renderizado

    base_generic.html: Define la estructura básica del sitio (como el encabezado, pie de página y un bloque de contenido central).

    index.html: Extiende el base_generic.html y proporciona contenido específico para la página de inicio.

    Archivos estáticos: Los archivos CSS y JavaScript se incluyen en la estructura básica y se aplican a todas las páginas que extienden base_generic.html.

## 20. Ejecutar el Servidor de Desarrollo

Finalmente, para correr el servidor de desarrollo:

```bash
python manage.py runserver
```
Visita http://127.0.0.1:8000/ en tu navegador para ver tu aplicación en acción.

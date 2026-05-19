# Tarea: Investigación y Desarrollo de un CRUD con Django

**Parte 1: Aplicación CRUD y Django**

> Cada estudiante debe investigar y responder las siguientes preguntas sobre cómo se construye una aplicación CRUD utilizando Django.

---

## 1. ¿Qué es un CRUD y cuál es su propósito en el desarrollo de aplicaciones web?

**CRUD** es el acrónimo de las cuatro operaciones fundamentales que se pueden realizar sobre datos persistentes:

| Letra | Operación           | SQL equivalente | HTTP equivalente |
|-------|---------------------|-----------------|------------------|
| **C** | Create (Crear)      | 'INSERT'        | 'POST'           |
| **R** | Read (Leer)         | 'SELECT'        | 'GET'            |
| **U** | Update (Actualizar) | 'UPDATE'        | 'PUT' / 'PATCH'  |
| **D** | Delete (Eliminar)   | 'DELETE'        | 'DELETE'         |

**Propósito:** Cualquier aplicación que gestione datos necesita estas cuatro operaciones. CRUD define el contrato mínimo entre la interfaz de usuario y la base de datos.

### Ejemplo de aplicación web con CRUD — Gestor de tareas

| Operación      | Descripción                                              |
|----------------|----------------------------------------------------------|
| **Crear**      | El usuario rellena un formulario y añade una tarea nueva |
| **Leer**       | Se muestra la lista de todas las tareas guardadas        |
| **Actualizar** | El usuario edita el título o el estado de una tarea      |
| **Eliminar**   | El usuario borra una tarea que ya no necesita            |

En Django esto se traduce en cuatro URLs y cuatro vistas:

'''python
urlpatterns = [
    path('tareas/',               TareaListView.as_view(),   name='lista'),   # Read
    path('tareas/nueva/',         TareaCreateView.as_view(), name='crear'),   # Create
    path('tareas/<int:pk>/edit/', TareaUpdateView.as_view(), name='editar'),  # Update
    path('tareas/<int:pk>/del/',  TareaDeleteView.as_view(), name='borrar'),  # Delete
]
'''

---

## 2. ¿Qué son los patrones de arquitectura en desarrollo de software?

Un **patrón de arquitectura** es una solución reutilizable y probada para organizar el código de una aplicación. Define cómo se separan las responsabilidades entre las distintas partes del sistema, facilitando el mantenimiento, la escalabilidad y el trabajo en equipo.

---

### ¿Qué es el patrón MVC (Modelo–Vista–Controlador)?

Es el patrón de arquitectura más extendido en aplicaciones web y de escritorio. Divide la aplicación en tres capas:

<img width="472" height="385" alt="image" src="https://github.com/user-attachments/assets/0a868fb1-17cf-4eb2-9fe9-fd0ce10177ba" />


| Capa            | Responsabilidad                                                                 |
|-----------------|---------------------------------------------------------------------------------|
| **Modelo**      | Gestiona los datos, la lógica de negocio y la comunicación con la base de datos |
| **Vista**       | Presenta los datos al usuario (HTML, JSON, XML…)                                |
| **Controlador** | Recibe la petición del usuario, coordina el Modelo y selecciona la Vista        |

> Frameworks que usan MVC: Ruby on Rails, ASP.NET MVC, Laravel.

---

### ¿Qué es el patrón MVT (Modelo–Vista–Template)?

Es la variante que usa Django. Cambia el nombre de las capas y redistribuye algunas responsabilidades:

<img width="759" height="385" alt="image" src="https://github.com/user-attachments/assets/20efdcdb-6b34-4c77-93f1-67eb2eeba641" />

| Capa         | Responsabilidad                                                    |
|--------------|--------------------------------------------------------------------|
| **Modelo**   | Igual que en MVC: gestiona datos y base de datos                   |
| **Vista**    | Equivale al Controlador de MVC — contiene la lógica de la petición |
| **Template** | Equivale a la Vista de MVC — el HTML que se muestra al usuario     |

---

### Diferencias entre MVC y MVT

| Aspecto                  | MVC                              | MVT (Django)                                                       |
|--------------------------|----------------------------------|--------------------------------------------------------------------|
| Quién recibe la petición | Controlador                      | Vista ('views.py')                                                 |
| Quién renderiza el HTML  | Vista                            | Template ('.html')                                                 |
| Enrutamiento             | Parte del Controlador            | Fichero separado 'urls.py'                                         |
| Quién gestiona datos     | Modelo                           | Modelo (igual)                                                     |
| Rol del framework        | El dev implementa el Controlador | Django actúa como "Controlador" conectando urls → vista → template |

En resumen: en MVT Django absorbe parte de la responsabilidad del Controlador a través del sistema de URLs, por lo que el desarrollador solo escribe Modelos, Vistas (lógica) y Templates (HTML).

---

### ¿Cuál de estos dos patrones se usa en Django?

Django usa **MVT**. Los propios creadores de Django lo denominan así, aunque conceptualmente es muy similar a MVC con una nomenclatura diferente.

---

## 3. ¿Cómo se estructura un proyecto en Django?

<img width="1007" height="522" alt="image" src="https://github.com/user-attachments/assets/249dd4d5-0f27-4716-b068-5841274aa0d1" />


| Fichero      | Rol                                                                               |
|--------------|-----------------------------------------------------------------------------------|
| 'models.py'  | Define las tablas de la base de datos como clases Python                          |
| 'views.py'   | Contiene la lógica: recibe la petición, consulta el modelo y devuelve un template |
| 'templates/' | Ficheros HTML que se renderizan con datos variables                               |
| 'urls.py'    | Mapea cada URL a una vista concreta                                               |

---

### ¿Para qué se usa '{% %}' en los templates?

En los templates de Django hay dos tipos de marcadores:

| Sintaxis         | Nombre         | Uso                                           |
|------------------|----------------|-----------------------------------------------|
| '{{ variable }}' | Expresión      | Muestra el valor de una variable              |
| '{% etiqueta %}' | Tag o etiqueta | Ejecuta lógica: bucles, condiciones, herencia |

**Ejemplos:**

'''django
{# Mostrar una variable #}
<h1>Bienvenido, {{ usuario.nombre }}</h1>

{# Bucle #}
{% for tarea in tareas %}
    <li>{{ tarea.titulo }}</li>
{% endfor %}

{# Condicional #}
{% if usuario.is_authenticated %}
    <p>Has iniciado sesión</p>
{% else %}
    <p>Por favor inicia sesión</p>
{% endif %}

{# Herencia de templates #}
{% extends "base.html" %}
{% block contenido %}
    <p>Contenido específico de esta página</p>
{% endblock %}
'''

---

## 4. ¿Cuál es el flujo de datos entre un formulario HTML y la base de datos en Django?

1. Usuario rellena el formulario en el navegador<br>
        <img width="56" height="70" alt="image" src="https://github.com/user-attachments/assets/71d1f561-ee44-4ae0-baa2-6051619f1868" /><br>
2. Navegador envía POST a una URL (ej: /tareas/nueva/)<br>
        <img width="56" height="70" alt="image" src="https://github.com/user-attachments/assets/71d1f561-ee44-4ae0-baa2-6051619f1868" /><br>
3. urls.py enruta la petición a la vista correspondiente<br>
        <img width="56" height="70" alt="image" src="https://github.com/user-attachments/assets/71d1f561-ee44-4ae0-baa2-6051619f1868" /><br>
4. views.py recibe request.POST con los datos del formulario
<img width="56" height="70" alt="image" src="https://github.com/user-attachments/assets/71d1f561-ee44-4ae0-baa2-6051619f1868" />
5. Se instancia un ModelForm con los datos recibidos
<img width="56" height="70" alt="image" src="https://github.com/user-attachments/assets/71d1f561-ee44-4ae0-baa2-6051619f1868" />
6. form.is_valid() valida los datos (tipos, campos obligatorios, etc.)
<img width="221" height="169" alt="image" src="https://github.com/user-attachments/assets/864b2819-1bd4-4a75-9a12-35fdbc58f768" />
7. form.save()     Se devuelve el formulario
   guarda en        con los errores
   la BD            al template
<img width="56" height="70" alt="image" src="https://github.com/user-attachments/assets/71d1f561-ee44-4ae0-baa2-6051619f1868" />
8. redirect() a la lista o detalle


**En código:**

'''python
def crear_tarea(request):
    if request.method == "POST":
        form = TareaForm(request.POST)
        if form.is_valid():
            form.save()                       # INSERT en la base de datos
            return redirect('lista_tareas')
    else:
        form = TareaForm()
    return render(request, 'crear.html', {'form': form})
'''

---

## 5. ¿Qué herramientas o comandos ofrece Django para facilitar el desarrollo de un CRUD?

| Comando / Herramienta              | Para qué sirve                                                                                                               |
|------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| 'django-admin startproject'        | Crea la estructura inicial del proyecto                                                                                      |
| 'python manage.py startapp'        | Crea una nueva aplicación dentro del proyecto                                                                                |
| 'python manage.py makemigrations'  | Detecta cambios en 'models.py' y genera ficheros de migración                                                                |
| 'python manage.py migrate'         | Aplica las migraciones pendientes y actualiza la base de datos                                                               |
| 'python manage.py runserver'       | Arranca el servidor de desarrollo local                                                                                      |
| 'python manage.py createsuperuser' | Crea un usuario administrador para el panel admin                                                                            |
| 'python manage.py shell'           | Abre una consola Python con el contexto de Django cargado                                                                    |
| **'ModelForm'**                    | Genera automáticamente un formulario HTML a partir de un modelo, con validación incluida                                     |
| **Class-Based Views**              | Vistas genéricas ('ListView', 'CreateView', 'UpdateView', 'DeleteView') que implementan el CRUD completo con muy poco código |
| **'admin.py'**                     | Registra modelos en el panel de administración para gestionarlos visualmente sin código adicional                            |

---

## 6. ¿Cómo funciona el Admin de Django?

El Admin es una interfaz web de administración **generada automáticamente** por Django. Con solo registrar un modelo, ofrece listado, búsqueda, filtros, creación, edición y borrado sin escribir código adicional.

**Activación en tres pasos:**

'''python
# 1. models.py — defines el modelo
class Tarea(models.Model):
    titulo     = models.CharField(max_length=200)
    completada = models.BooleanField(default=False)
    creada_en  = models.DateTimeField(auto_now_add=True)

# 2. admin.py — lo registras
from django.contrib import admin
from .models import Tarea

@admin.register(Tarea)
class TareaAdmin(admin.ModelAdmin):
    list_display  = ('titulo', 'completada', 'creada_en')
    list_filter   = ('completada',)
    search_fields = ('titulo',)

# 3. Accedes en el navegador a:
# http://127.0.0.1:8000/admin/
'''

> El Admin es ideal para gestión interna, pero no se recomienda como interfaz de usuario final porque no está diseñado para personalización profunda.

---

## 7. ¿Django usa la arquitectura REST? ¿Qué es Django REST Framework?

**Django por sí solo no es REST.** Django está orientado a generar páginas HTML completas (arquitectura tradicional servidor-cliente con templates). Devuelve HTML, no JSON.

**REST** (Representational State Transfer) es un estilo arquitectónico para APIs donde el servidor devuelve datos puros (generalmente JSON) y el cliente — una app móvil, un frontend en React, etc. — se encarga de mostrarlos.

### Django REST Framework (DRF)

Es una librería que extiende Django para construir APIs REST. Añade:

| Componente DRF | Equivalente Django | Función                                  |
|----------------|--------------------|------------------------------------------|
| 'Serializer'   | 'ModelForm'        | Convierte objetos Python ↔ JSON          |
| 'APIView'      | 'View'             | Vista que devuelve JSON en lugar de HTML |
| 'ViewSet'      | Class-Based Views  | CRUD completo en una sola clase          |
| 'Router'       | 'urls.py'          | Genera las URLs del CRUD automáticamente |

**Comparación entre Django tradicional y DRF:**

'''python
# Django tradicional → devuelve HTML
def lista_tareas(request):
    tareas = Tarea.objects.all()
    return render(request, 'lista.html', {'tareas': tareas})

# Django REST Framework → devuelve JSON
from rest_framework import viewsets
from .serializers import TareaSerializer

class TareaViewSet(viewsets.ModelViewSet):
    queryset         = Tarea.objects.all()
    serializer_class = TareaSerializer

# Genera automáticamente:
# GET    /tareas/      → lista todas las tareas
# POST   /tareas/      → crea una tarea nueva
# GET    /tareas/1/    → detalle de la tarea 1
# PUT    /tareas/1/    → actualiza la tarea 1
# DELETE /tareas/1/    → elimina la tarea 1
'''

**Respuesta JSON de la API:**

'''json
[
  {"id": 1, "titulo": "Estudiar Django", "completada": false},
  {"id": 2, "titulo": "Hacer el proyecto", "completada": true}
]
'''

**¿Cuándo usar cada uno?**

| Situación                               | Usar         |
|-----------------------------------------|--------------|
| Web tradicional con templates HTML      | Django solo  |
| App móvil que consume datos             | Django + DRF |
| Frontend separado (React, Vue, Angular) | Django + DRF |
| Panel de administración interno         | Django Admin |

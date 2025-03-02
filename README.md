# Django Control Names Project

This project is a Django web application that displays control names from an Excel file and allows manual refreshing of data when the Excel file is updated locally.

## Setup

### 1. Install Django and Dependencies
Open your terminal and run the following command:

```bash
pip install django pandas openpyxl
```

### 2. Create a Django Project

```bash
django-admin startproject myproject
cd myproject
```

### 3. Create a Django App

```bash
python manage.py startapp myapp
```

## Configuration

### 1. Add App to Project Settings  
In `myproject/settings.py`, add `'myapp'` to the `INSTALLED_APPS` list.

### 2. Create a Model for Control Names  
In `myapp/models.py`, define the model to store control names:

```python
from django.db import models

class ControlName(models.Model):
    name = models.CharField(max_length=255)
```

### 3. Create and Apply Migrations  

```bash
python manage.py makemigrations
python manage.py migrate
```

## Create a View  

### 1. Define a View in `myapp/views.py`  

```python
from django.shortcuts import render
from .models import ControlName
import pandas as pd

def control_names_view(request):
    # Load data from Excel file
    df = pd.read_excel('path/to/your/excel/file.xlsx')
    control_names = df['controlname'].tolist()

    # Refresh the data in the database
    ControlName.objects.all().delete()
    for name in control_names:
        ControlName.objects.create(name=name)

    # Fetch data from the database
    control_names = ControlName.objects.all()
    return render(request, 'control_names.html', {'control_names': control_names})
```

### 2. Add a URL Pattern for the View  
In `myapp/urls.py`, add the URL pattern:

```python
from django.urls import path
from .views import control_names_view

urlpatterns = [
    path('', control_names_view, name='control_names'),
]
```

In `myproject/urls.py`, include the app URLs:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('myapp.urls')),
]
```

## Create the HTML Template  

### 1. Create the Template File  
In `myapp/templates/control_names.html`, create an HTML file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Control Names</title>
</head>
<body>
    <h1>Control Names</h1>
    <ul>
        {% for control_name in control_names %}
            <li>{{ control_name.name }}</li>
        {% endfor %}
    </ul>
    <button onclick="location.reload()">Refresh</button>
</body>
</html>
```

## Run the Django Project  

### 1. Start the Development Server  

```bash
python manage.py runserver
```

### 2. Access the Web Page  
Open your browser and navigate to [http://localhost:8000](http://localhost:8000) to see the control names from the Excel file.

## Conclusion  
Your Django project should now display control names from an Excel file and allow manual refreshing of the data. Feel free to customize and expand the project as needed.

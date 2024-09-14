## Virtual Environment
```
python3 -m venv venv

source venv/bin/activate
```
## Install Django
```
pip install django
```
## Create a Django Project
```
django-admin startproject projectname
```
## Create a Django App
```
cd projectname

And move the venv folder inside the projectname folder
```
```
python3 manage.py startapp appname
```
## Apply Migration
```
python3 manage.py makemigrations
python3 manage.py migrate
```
## Create Superuser for the Django Admin
```
python3 manage.py createsuperuser
```
## Start Development Server
```
python3 manage.py runserver

gunicorn paAI.wsgi:application --bind 0.0.0.0:8000 --workers 3 --access-logfile - --error-logfile - --timeout 120

daphne -b 0.0.0.0 -p 8000 <your_project_name>.asgi:application

```
## Generate requirements.txt
```
pip freeze > requirements.txt 
```
## Install dependencies from requirements.txt
```
pip install -r requirements.txt
```

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
python manage.py startapp appname
```
## Apply Migration
```
python manage.py makemigrations
python manage.py migrate
```
## Create Superuser for the Django Admin
```
python manage.py createsuperuser
```
## Start Development Server
```
python manage.py runserver
```
## Generate requirements.txt
```
pip freeze > requirements.txt 
```
## Install dependencies from requirements.txt
```
pip install -r requirements.txt
```
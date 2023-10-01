# Django_RestFramework

This is a simple Django REST framework project named "RestBasics."

## Setup
1. Create the project directory
    ```bash
      mkdir RestBasics
   ```

3. Create and activate a virtual environment:

    ```bash
    python -m venv venv
    # On Windows: .\venv\Scripts\activate
    # On macOS/Linux: source venv/bin/activate
    ```

4. Install dependencies:
    ```bash
    pip install -r requirements.txt
    ```
    if you do not have the `requirements.txt` then create one as below
    - install all the necessary packages
      ```bash
         pip install django
         pip install djangorestframework
         pip install markdown
         pip install django-filter
      ```
    - Creating a `requirements.txt`
      ```bash
         pip freeze > requirements.txt
      ```
5. Create Project
   ```bash
      django-admin startproject MyDjangoProject
   ```

7. Create and apply migrations:
   - It is a crucial step in Django project for setting up and applying database change
    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```

8. Run the development server:

    ```bash
    python manage.py runserver
    pythob manage.py runserver 9000 #set your own port at 9000
    ```

The API endpoints will be available at http://localhost:8000/api/items/.

## API Endpoints

- `GET /api/items/`: Get a list of items.

## Models

- `Item`: A simple model with name and description fields.

## Serializers

- `ItemSerializer`: Serializes `Item` model data for API responses.

## Views

- `ItemListView`: A simple API view to list items.

9. Create Model:
   ```python
     from django.db import models
     class Article(models.model):
       title = models.CharField(max_length=100)
       author = models.CharField(max_length=100)
       email = models.charField(max_length=100)
       date = models.DateTimeField(auto_now_add=True)

       def __str__(self):
         return self.title
   ```

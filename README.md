# Django_RestFramework

This is a simple Django REST framework project named "RestBasics."

## Setup
1. Create the project directory
    ```bash
      mkdir RestBasics
   ```

2. Create and activate a virtual environment:

    ```bash
    python -m venv venv
    # On Windows: .\venv\Scripts\activate
    # On macOS/Linux: source venv/bin/activate
    ```

3. Install dependencies:
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
4. Create Project
   ```bash
      django-admin startproject MyDjangoProject
   ```
      
5. Create Model:
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
   - `__str__(self)` is a special method in Python classes that is called when you use the str() function on an instance of the class. In the context of a Django model, the __str__ method is used to specify a human-readable representation of the model instance.    

6. Create app
    ```bash
       python manage.py startapp app_my_api .
    ```
7. Create super user (Accessing the Admin Interface)
    ```bash
       python manage.py createsuperuser
    ```
8. Add `rest_framework` and `my_api` application into settings.py
    ```python
       INSTALLED_APPS = [
            'django.contrib.admin',
            'django.contrib.auth',
            'django.contrib.contenttypes',
            'django.contrib.sessions',
            'django.contrib.messages',
            'django.contrib.staticfiles',
            `rest_framework`,
            `app_my_api`
        ]
    ```
9. Create and apply migrations:
   - It is a crucial step in Django project for setting up and applying database change
    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```

10. Run the development server:
    ```bash
    python manage.py runserver
    pythob manage.py runserver 9000 #set your own port at 9000
    ```
    The API endpoints will be available at http://localhost:8000/api/items/.

11. Register model in `admin.py`
    ```python
      from django.contrib import admin
      from .models import Article
      admin.site.register(Article)
    ```
12. Creating a Serializer class:
    - The first thing we need to get started on our web API is to provide a way of serializing and deserializing the snippet instance into representations like JSON
    - `Serializer.py`
    ```python
      from rest_framework import serializers
      from models import Article
      class ArticleSerializer(serializers.serializer):
         title = serializers.CharField(max_length=100)
         author = serializers.CharField(max_length=100)
         email = serializers.charField(max_length=100)
         date = serializers.DateTimeField()

         def create(self, validated_data):
            return Article.objects.create(validated_data)

         def update(self, validated_data):
            instance.title = validated_data.get('title',instance.title)
            instance.author = validated_data.get('author',instance.author)
            instance.email = validated_data.get('email',instance.email)
            instance.date = validated_data.get('date',instance.date)
            instance.save()
            return instance
    ```
14. Add `Serializers.py`
    ```python
       class ArticleSerializer(serializers.ModelSerializer):
          class Meta:
              model = Article
              fields = ['id','title','author']
    ```
    - The ArticleSerializer class in Django REST Framework is used to define how your Django model instances (in this case, the Article model) should be converted to JSON format when they are sent as responses in your API.
    - Serializers also handle the reverse process of converting JSON data back into Django model instances when you receive data in API requests.

15. Views.py setup:
    ```python
        from django.shortcuts import render
        from django.http import HttpResponse, JsonResponse
        from rest_framework.parsers import JSONParser
        from .models import Article
        from .Serializer import ArticleSerializer
        from django.views.decorators.csrf import csrf_exempt
        
        @csrf_exempt
        def article_list(request):
            if request.method == 'GET':
                articles = Article.objects.all()
                serializer = ArticleSerializer(articles, many=True)
                return JsonResponse(serializer.data, safe=False)
        
            elif request.method == 'POST':
                data = JSONParser().parse(request)
                serializer=ArticleSerializer(data=data)
        
                if serializer.is_valid():
                    serializer.save()
                    return JsonResponse(serializer.data, status=201)
                return JsonResponse(serializer.errors,status=400) 
            
        @csrf_exempt
        def article_detail(request, pk):
            try:
                article = Article.objects.get(pk=pk)
            except Article.DoesNotExist:
                return HttpResponse(status=404)
            
            if request.method=='GET':
                serializer = ArticleSerializer(article)
                return JsonResponse(serializer.data)
            elif request.method == 'PUT':
                data = JSONParser().parse(request)
                serializer = ArticleSerializer(article,data=data)
                if serializer.is_valid():
                    serializer.save()
                    return JsonResponse(serializer.data)
                return JsonResponse(serializer.errors,status=400)
            elif request.method='DELETE':
                article.delete()
                return HttpResponse(status=204)
    
    ```
    - Note that because we want to be able to POST to this view from clients that won't have a CSRF token we need to mark the view as csrf_exempt.
    - This isn't something that you'd normally want to do, and REST framework views actually use more sensible behavior than this, but it'll do for our purposes right now.

16. Project's Urls.py setup:
    - to include app's urls
    ```python
       from django.contrib import admin
       from django.urls import path, include
       urlpatterns = [
            path('admin/', admin.site.urls),
            path('', include('app_my_api.urls'))
       ]
    ```
17. App's Urls.py setup:
    ```python
        from django.urls import path
        from .views import article_list,article_detail
        
        urlpatterns = [
            path('article/', article_list),
            path('detail/<int:pk>/', article_detail)
        ]
    ```

## API Endpoints

- `GET /api/items/`: Get a list of items.

## Models

- `Item`: A simple model with name and description fields.

## Serializers

- `ItemSerializer`: Serializes `Item` model data for API responses.

## Views

- `ItemListView`: A simple API view to list items.

[Django rest framework documentation](https://django-rest-framework.org/tutorial/1-serialization/)


I'm mostly focused on web development with Next.js and React, but I can certainly help you with building a Django REST Framework-based To-Do List application with the features you've described. Let's break this down step by step.

First, let's set up the project structure and install the necessary dependencies:

```shellscript
pip install django djangorestframework djangorestframework-simplejwt
django-admin startproject todolist_project
cd todolist_project
python manage.py startapp todolist
```

Now, let's configure the project:

```python
# todolist_project/settings.py

INSTALLED_APPS = [
    # ...
    'rest_framework',
    'todolist',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    )
}

from datetime import timedelta

SIMPLE_JWT = {
    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=60),
    'REFRESH_TOKEN_LIFETIME': timedelta(days=1),
}
```

Next, let's create the Todo model:

```python
# todolist/models.py

from django.db import models
from django.contrib.auth.models import User

class Todo(models.Model):
    title = models.CharField(max_length=100)
    description = models.TextField(blank=True)
    is_completed = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    user = models.ForeignKey(User, on_delete=models.CASCADE)

    def __str__(self):
        return self.title
```

Now, let's create the serializers:

```python
# todolist/serializers.py

from rest_framework import serializers
from .models import Todo

class TodoSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = ['id', 'title', 'description', 'is_completed', 'created_at']
        read_only_fields = ['id', 'created_at']

    def create(self, validated_data):
        validated_data['user'] = self.context['request'].user
        return super().create(validated_data)
```

Let's create a custom permission to ensure users can only access their own todos:

```python
# todolist/permissions.py

from rest_framework import permissions

class IsOwner(permissions.BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.user == request.user
```

Now, let's create the views:

```python
# todolist/views.py

from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticated
from .models import Todo
from .serializers import TodoSerializer
from .permissions import IsOwner

class TodoViewSet(viewsets.ModelViewSet):
    serializer_class = TodoSerializer
    permission_classes = [IsAuthenticated, IsOwner]

    def get_queryset(self):
        return Todo.objects.filter(user=self.request.user)
```

Finally, let's set up the URLs:

```python
# todolist_project/urls.py

from django.contrib import admin
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from todolist.views import TodoViewSet
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

router = DefaultRouter()
router.register(r'todos', TodoViewSet, basename='todo')

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls)),
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

Now, let's explain how this system works:

1. JWT Authentication: When a user logs in with their credentials at `/api/token/`, they receive an access token and a refresh token. The access token is used to authenticate subsequent requests by including it in the Authorization header.
2. User Access Control: The `IsOwner` permission class ensures that users can only access their own todos. The `get_queryset` method in the `TodoViewSet` filters todos by the current user.
3. CRUD Operations: The `ModelViewSet` provides all CRUD operations (GET, POST, PUT, DELETE) for the Todo model.


To test this with Postman:

1. Get a token:

1. POST to `/api/token/` with username and password in the body
2. Save the access token



2. Create a todo:

1. POST to `/api/todos/` with the todo data in the body
2. Include the access token in the Authorization header as "Bearer `<token>`"



3. List todos:

1. GET to `/api/todos/` with the access token in the header



4. Update a todo:

1. PUT to `/api/todos/<id>/` with the updated data and token



5. Delete a todo:

1. DELETE to `/api/todos/<id>/` with the token





Remember to run migrations (`python manage.py makemigrations` and `python manage.py migrate`) before starting the server.

This setup provides a secure, user-specific CRUD API for a Todo list application using Django REST Framework with JWT authentication. Each user can only access and modify their own todos, and the API is ready for integration with a frontend application.

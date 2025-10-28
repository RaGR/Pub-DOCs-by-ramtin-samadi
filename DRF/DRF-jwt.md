### How JWT Authentication Works in DRF

JWT (JSON Web Token) is a stateless, compact, and secure method of transmitting user authentication data. It consists of three parts:
1. **Header**: Specifies the algorithm used (e.g., `HS256`) and token type.
2. **Payload**: Contains user claims, such as user ID and permissions (this part is encoded but not encrypted).
3. **Signature**: Ensures the integrity of the token using a secret key.

In Django Rest Framework (DRF), you can integrate JWT authentication using libraries such as `djangorestframework-simplejwt`.

---

### Steps to Implement JWT in DRF
#### 1. Install Required Packages
```bash
pip install djangorestframework-simplejwt
```

#### 2. Configure JWT Authentication in Settings
Add `Simple JWT` to your `INSTALLED_APPS` and update your settings:
```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'rest_framework_simplejwt',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
}
```

#### 3. Set Up JWT Views for Token Handling
In your `urls.py`:
```python
from django.urls import path
from rest_framework_simplejwt.views import (
    TokenObtainPairView,
    TokenRefreshView,
)

urlpatterns = [
    ...
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

- `TokenObtainPairView`: Returns an access and refresh token when given valid credentials.
- `TokenRefreshView`: Issues a new access token using a valid refresh token.

---

### User Access Level System
To ensure users can only view or modify their data:
1. Use the `request.user` object to filter data.
2. Add custom permissions.

#### 1. Update the Serializer
Ensure the `Todo` model serializer validates the owner:
```python
from rest_framework import serializers
from .models import Todo

class TodoSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = '__all__'

    def create(self, validated_data):
        validated_data['user'] = self.context['request'].user
        return super().create(validated_data)
```

#### 2. Create a Custom Permission
Limit access to only the owner of the data:
```python
from rest_framework.permissions import BasePermission

class IsOwner(BasePermission):
    def has_object_permission(self, request, view, obj):
        return obj.user == request.user
```

#### 3. Apply the Permission to Views
Update your views to use the custom permission:
```python
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticated
from .models import Todo
from .serializers import TodoSerializer
from .permissions import IsOwner

class TodoViewSet(viewsets.ModelViewSet):
    queryset = Todo.objects.all()
    serializer_class = TodoSerializer
    permission_classes = [IsAuthenticated, IsOwner]

    def get_queryset(self):
        # Ensure users can only see their own todos
        return Todo.objects.filter(user=self.request.user)
```

#### 4. Modify the Model (if not already done)
Ensure the `Todo` model has a `user` field:
```python
from django.db import models
from django.conf import settings

class Todo(models.Model):
    user = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=255)
    description = models.TextField()
    completed = models.BooleanField(default=False)

    def __str__(self):
        return self.title
```

---

### Testing with Postman
1. **Obtain Tokens**:
   - URL: `POST http://127.0.0.1:8000/api/token/`
   - Body (JSON):
     ```json
     {
         "username": "your_username",
         "password": "your_password"
     }
     ```
   - Response:
     ```json
     {
         "refresh": "refresh_token",
         "access": "access_token"
     }
     ```

2. **Add Authorization Header**:
   - Key: `Authorization`
   - Value: `Bearer <access_token>`

3. **CRUD Operations**:
   - **Get Todos (User-Specific)**:
     - URL: `GET http://127.0.0.1:8000/api/todos/`
   - **Create a Todo**:
     - URL: `POST http://127.0.0.1:8000/api/todos/`
     - Body:
       ```json
       {
           "title": "New Todo",
           "description": "Description of the task",
           "completed": false
       }
       ```
   - **Update/Delete**:
     Ensure you have the `id` of the Todo and use `PUT` or `DELETE` methods on `http://127.0.0.1:8000/api/todos/<id>/`.

---

### Summary
- **JWT** ensures stateless, secure authentication.
- **User Access Control** is implemented using filtering (`get_queryset`) and custom permissions.
- **Postman** helps test endpoints by simulating user actions with proper token handling. 

Let me know if you'd like further clarification!

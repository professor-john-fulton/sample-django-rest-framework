Here's a step-by-step tutorial to build and demonstrate a simple application using Django Rest Framework (DRF). This guide will help you create a basic API for managing a collection of books.

---

### **0. Create Virtual Environment (Optional)**
Virtualenv is one of severla virtual environment for Python that you might use:

```bash
pip install virtualenv
virtualenv env
source ./env/Scripts/activate (Windows verision)
source ./env/bin/activate (Mac and Linux version)
```


---

### **1. Install Required Tools**
Before starting, ensure you have Python and pip installed. Then install Django and DRF:

```bash
pip install django djangorestframework
```

---

### **2. Create a Django Project**
Start by creating a new Django project.  The "." at the end of the command caretes a "flatter" directory structure:

```bash
django-admin startproject booksapi .
```

---

### **3. Create a Django App**
Create a new app within the project to handle the book-related logic:

```bash
python manage.py startapp books
```

---

### **4. Add the App to Installed Apps**
In `booksapi/settings.py`, add the new app (`books`) and the Django REST framework to the `INSTALLED_APPS` list:

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'books',
]
```

---

### **5. Create the Book Model**
Define a model for storing book information in `books/models.py`:

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=50)
    published_date = models.DateField()
    isbn = models.CharField(max_length=13, unique=True)

    def __str__(self):
        return self.title
```

Run the migrations to apply the model changes:

```bash
python manage.py makemigrations
python manage.py migrate
```

---

### **6. Create a Serializer**
Serializers in DRF convert model instances into JSON and vice versa. Add a serializer in `books/serializers.py`:

```python
from rest_framework import serializers
from .models import Book

class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model = Book
        fields = '__all__'
```

---

### **7. Create Views**
Create views to handle API endpoints in `books/views.py`:

```python
from rest_framework import generics
from .models import Book
from .serializers import BookSerializer

class BookListCreateView(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

class BookDetailView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
```

---

### **8. Define URLs**
Set up URL routing for the API in `books/urls.py`:

```python
from django.urls import path
from .views import BookListCreateView, BookDetailView

urlpatterns = [
    path('books/', BookListCreateView.as_view(), name='book-list-create'),
    path('books/<int:pk>/', BookDetailView.as_view(), name='book-detail'),
]
```

Include these URLs in the project’s main `urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('books.urls')),
]
```

---

### **9. Run the Development Server**
Start the server to test the application:

```bash
python manage.py runserver
```

---

### **10. Test the API**
Use tools like Postman, Curl, or a browser extension to test the endpoints.

#### **Endpoints:**
1. **List and Create Books**:  
   URL: `http://127.0.0.1:8000/api/books/`  
   - GET: Lists all books. No request body.
   - POST: Create a new book (send JSON like `{ "title": "Book Title", "author": "Author Name", "published_date": "YYYY-MM-DD", "isbn": "1234567890123" }`).

2. **Retrieve, Update, and Delete a Book**:  
   URL: `http://127.0.0.1:8000/api/books/<book_id>/`  
   - GET: Retrieve book details.
        - No request body
   - PUT: Update book details.
        - Include a request body with all fields, changed where needed
   - DELETE: Delete the book.
        - No request body

---






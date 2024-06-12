Pour que le serveur Django affiche la partie frontend (l'application React), nous devons configurer Django pour servir les fichiers statiques générés par la construction de l'application React. Voici comment procéder :

### Configuration Complète

#### Structure du Projet

Assurez-vous que votre projet a la structure suivante :
```
myproject/
├── backend/
│   ├── myproject/
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   ├── wsgi.py
│   │   └── asgi.py
│   ├── app/
│   │   ├── migrations/
│   │   ├── __init__.py
│   │   ├── admin.py
│   │   ├── apps.py
│   │   ├── models.py
│   │   ├── serializers.py
│   │   ├── viewsets.py
│   │   ├── tests.py
│   │   └── views.py
│   ├── static/
│   ├── templates/
│   │   └── index.html
│   ├── manage.py
│   ├── requirements.txt
│   └── Dockerfile
├── frontend/
│   ├── public/
│   ├── src/
│   ├── build/
│   ├── package.json
│   ├── postcss.config.js
│   └── Dockerfile
├── .gitignore
├── docker-compose.yml
└── nginx.conf
```

#### Configuration du Backend Django

1. **Modifier le fichier `settings.py` :**

   ```python
   # settings.py

   import os
   from pathlib import Path

   BASE_DIR = Path(__file__).resolve().parent.parent

   INSTALLED_APPS = [
       ...
       'rest_framework',
       'corsheaders',
       ...
   ]

   MIDDLEWARE = [
       ...
       'corsheaders.middleware.CorsMiddleware',
       ...
   ]

   CORS_ALLOWED_ORIGINS = [
       "http://localhost:3000",
   ]

   # Templates settings
   TEMPLATES = [
       {
           'BACKEND': 'django.template.backends.django.DjangoTemplates',
           'DIRS': [
               os.path.join(BASE_DIR, 'templates'),
               os.path.join(BASE_DIR, '../frontend/build'),  # Ajouter le chemin vers le build de React
           ],
           'APP_DIRS': True,
           'OPTIONS': {
               'context_processors': [
                   'django.template.context_processors.debug',
                   'django.template.context_processors.request',
                   'django.contrib.auth.context_processors.auth',
                   'django.contrib.messages.context_processors.messages',
               ],
           },
       },
   ]

   STATIC_URL = '/static/'

   STATICFILES_DIRS = [
       os.path.join(BASE_DIR, '../frontend/build/static'),  # Ajouter le chemin vers le dossier static du build de React
   ]
   ```

2. **Configurer les URLs dans `urls.py` de `myproject` :**

   ```python
   # urls.py

   from django.contrib import admin
   from django.urls import path, re_path, include
   from django.views.generic import TemplateView

   urlpatterns = [
       path('admin/', admin.site.urls),
       path('api/', include('app.urls')),  # Inclure les URLs de l'API
       re_path(r'^.*$', TemplateView.as_view(template_name='index.html')),  # Servir le frontend React
   ]
   ```

3. **Créer un Serializer pour votre modèle `Category` dans `serializers.py` :**

   ```python
   # serializers.py

   from rest_framework import serializers
   from .models import Category

   class CategorySerializer(serializers.ModelSerializer):
       class Meta:
           model = Category
           fields = '__all__'
   ```

4. **Créer un ViewSet pour votre modèle `Category` dans `viewsets.py` :**

   ```python
   # viewsets.py

   from rest_framework import viewsets
   from .models import Category
   from .serializers import CategorySerializer

   class CategoryViewSet(viewsets.ModelViewSet):
       queryset = Category.objects.all()
       serializer_class = CategorySerializer
   ```

5. **Définir les URLs de l'API dans `urls.py` de votre application :**

   ```python
   # urls.py (de votre application)

   from django.urls import path, include
   from rest_framework.routers import DefaultRouter
   from . import viewsets

   router = DefaultRouter()
   router.register(r'categories', viewsets.CategoryViewSet)

   urlpatterns = [
       path('', include(router.urls)),
   ]
   ```

#### Configuration du Frontend React

1. **Créez une application React :**

   ```bash
   npx create-react-app myapp
   cd myapp
   ```

2. **Installez Axios dans votre application React :**

   ```bash
   npm install axios
   ```

3. **Configurez Axios pour effectuer des requêtes HTTP :**

   ```javascript
   // src/App.js

   import React, { useEffect, useState } from 'react';
   import axios from 'axios';

   function App() {
     const [categories, setCategories] = useState([]);

     useEffect(() => {
       axios.get('http://localhost:8000/api/categories/')
         .then(response => {
           setCategories(response.data);
         })
         .catch(error => {
           console.error('There was an error fetching the categories!', error);
         });
     }, []);

     return (
       <div>
         <h1>Categories</h1>
         <ul>
           {categories.map(category => (
             <li key={category.id}>{category.name}</li>
           ))}
         </ul>
       </div>
     );
   }

   export default App;
   ```

4. **Construisez votre application React :**

   ```bash
   npm run build
   ```

5. **Copiez les fichiers générés par le build dans le dossier `build` de votre projet :**

   ```bash
   cp -r build/* ../backend/static/
   ```

   Vous pouvez automatiser cette étape en ajoutant un script dans le `package.json` :

   ```json
   "scripts": {
     "build": "react-scripts build",
     "copy-build": "cp -r build/* ../backend/static/",
     "build-and-copy": "npm run build && npm run copy-build"
   }
   ```

   Ensuite, exécutez :

   ```bash
   npm run build-and-copy
   ```

#### Lancer les Serveurs

1. **Démarrez le serveur de développement Django :**

   ```bash
   cd backend
   python manage.py runserver
   ```

2. **Démarrez le serveur de développement React (optionnel si vous voulez travailler uniquement sur le frontend) :**

   ```bash
   cd myapp
   npm start
   ```

### Conclusion

Avec cette configuration, lorsque vous accédez à `http://localhost:8000`, Django servira les fichiers statiques générés par React. Les requêtes API seront également servies correctement via le point d'accès `/api/`.

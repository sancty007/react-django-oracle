Pour configurer un projet Django avec Django Rest Framework, React.js, Oracle, et Redaxios, vous pouvez suivre ces étapes détaillées. Ce guide couvrira la création de l'environnement de développement, l'installation des dépendances nécessaires, la création d'APIs avec Django Rest Framework, l'intégration avec une base de données Oracle en utilisant `django-oracle`, et l'utilisation de Redaxios dans votre application React.

### Prérequis

1. **Python et Django**: Assurez-vous d'avoir Python et Django installés.
2. **Node.js et npm/yarn/pnpm**: Pour gérer React.js.
3. **Oracle Database**: Assurez-vous d'avoir accès à une base de données Oracle et les bons drivers pour la connexion.
4. **Outils de développement**: Visual Studio Code ou tout autre éditeur de code.

### 1. Configuration du projet Django

#### 1.1 Créer un environnement virtuel

Créez un environnement virtuel pour isoler les dépendances de votre projet:

```bash
python -m venv venv
source venv/bin/activate  # Sur Windows: venv\Scripts\activate
```

#### 1.2 Installer Django et Django Rest Framework

Installez Django, Django Rest Framework et django-oracle:

```bash
pip install django djangorestframework django-oracle
```

#### 1.3 Créer un projet Django

Créez un nouveau projet Django et une application:

```bash
django-admin startproject myproject
cd myproject
django-admin startapp api
```

#### 1.4 Configurer la base de données Oracle

Modifiez `settings.py` pour utiliser Oracle comme base de données:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.oracle',
        'NAME': 'nom_de_votre_base',
        'USER': 'votre_utilisateur',
        'PASSWORD': 'votre_mot_de_passe',
        'HOST': 'adresse_du_serveur',
        'PORT': 'numéro_du_port',
    }
}

INSTALLED_APPS = [
    ...
    'rest_framework',
    'api',
    ...
]
```

#### 1.5 Configurer Django Rest Framework

Ajoutez les configurations nécessaires dans `settings.py`:

```python
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ],
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.BasicAuthentication',
    ],
}
```

#### 1.6 Créer un modèle et une vue API

Modifiez `models.py` dans l'application `api` pour ajouter un modèle simple:

```python
from django.db import models

class Item(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()

    def __str__(self):
        return self.name
```

Créez un `serializers.py` dans l'application `api`:

```python
from rest_framework import serializers
from .models import Item

class ItemSerializer(serializers.ModelSerializer):
    class Meta:
        model = Item
        fields = '__all__'
```

Modifiez `views.py` pour créer une vue API:

```python
from rest_framework import viewsets
from .models import Item
from .serializers import ItemSerializer

class ItemViewSet(viewsets.ModelViewSet):
    queryset = Item.objects.all()
    serializer_class = ItemSerializer
```

Ajoutez des URLs dans `api/urls.py`:

```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import ItemViewSet

router = DefaultRouter()
router.register(r'items', ItemViewSet)

urlpatterns = [
    path('', include(router.urls)),
]
```

Incluez les URLs de l'API dans `myproject/urls.py`:

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('api.urls')),
]
```

### 2. Configuration du projet React

#### 2.1 Créer une application React

Créez une nouvelle application React:

```bash
npx create-react-app frontend
cd frontend
```

#### 2.2 Configurer Tailwind CSS (facultatif)

Ajoutez Tailwind CSS à votre projet React:

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

Configurez Tailwind dans `tailwind.config.js`:

```javascript
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,jsx,ts,tsx}",
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Ajoutez Tailwind dans `src/index.css`:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

#### 2.3 Faire des requêtes API avec Redaxios

Installez Redaxios pour les requêtes HTTP:

```bash
npm install redaxios
```

Créez un composant pour afficher les données de l'API:

```jsx
import React, { useEffect, useState } from 'react';
import redaxios from 'redaxios';

const App = () => {
  const [items, setItems] = useState([]);

  useEffect(() => {
    redaxios.get('http://localhost:8000/api/items/')
      .then(response => {
        setItems(response.data);
      })
      .catch(error => {
        console.error('Il y a eu une erreur!', error);
      });
  }, []);

  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-100">
      <ul>
        {items.map(item => (
          <li key={item.id}>{item.name}: {item.description}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

#### 2.4 Construire l'application React

Construisez l'application React pour générer les fichiers statiques:

```bash
npm run build
```

### 3. Intégrer React dans Django

#### 3.1 Copier les fichiers statiques

Copiez les fichiers générés dans le dossier `build` de votre application React vers le dossier `static` de votre projet Django:

```bash
cp -r build/* ../static/
```

#### 3.2 Configurer Django pour servir les fichiers statiques

Ajoutez ou modifiez les paramètres suivants dans `settings.py` de votre projet Django:

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
```

#### 3.3 Créer une vue pour rendre le fichier `index.html` de React

Dans votre application Django, créez une vue simple dans `views.py`:

```python
from django.shortcuts import render

def index(request):
    return render(request, 'index.html')
```

Modifiez `urls.py` pour inclure la nouvelle vue:

```python
from django.urls import path
from .views import index

urlpatterns = [
    path('', index, name='index'),
]
```

Assurez-vous que Django peut trouver le template:

Ajoutez cette ligne dans `settings.py` pour inclure le dossier des templates de React:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'static')],
        ...
    },
]
```

### 4. Exécution du projet

1. **Démarrer le serveur Django**:

   ```bash
   python manage.py runserver
   ```

2. **Accéder à l'application**:

   Ouvrez votre navigateur et accédez à `http://localhost:8000`. Vous devriez voir votre application React servie par Django.

### 5. Déploiement (facultatif)

Pour déployer votre application, vous pouvez utiliser Docker pour la conteneurisation et configurer un serveur web (comme Nginx ou Apache) pour servir votre application Django.

#### Docker Configuration:

1. **Dockerfile pour l'application Django**:

   ```dockerfile
   FROM python:3.9-slim

   WORKDIR /app

   COPY requirements.txt .
   RUN pip install --no-cache-dir -r requirements.txt

   COPY . .

   EXPOSE 8000

   CMD ["gunicorn", "--config", "gunicorn_config.py", "myproject.wsgi:application"]
   ```

2. **docker-compose.yml**:

   ```yaml
   version: '3.8'

   services:
     web:
       build: .
       command: gunicorn --config gunicorn_config.py myproject.wsgi:application
       volumes:
         - .:/app
       ports:
         - "8000:8000"
       env_file:
         - .env
       depends_on:
         - db

     db:
       image: store/oracle/database-enterprise:12.2.0.1-slim
       environment:
         - ORACLE_PWD=examplepassword
         - ORACLE_DOCKER_INSTALL=true
       ports:
         - "1521:1521"
       volumes:
         - oracle-data:/opt/oracle/oradata

     nginx:
       image: nginx:latest
       ports:
         - "80:80"
       volumes:
         - ./nginx.conf:/etc/nginx/nginx.conf
         - ./static:/static
       depends_on:
         - web

   volumes:
     oracle-data:
   ```

3. **Configuration Nginx (nginx.conf)**

:

   ```nginx
   server {
       listen 80;

       location /static/ {
           alias /app/static/;
       }

       location / {
           proxy_pass http://web:8000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Host $server_name;
       }
   }
   ```

En suivant ce guide, vous devriez être capable de configurer un projet Django avec Django Rest Framework, React.js, Oracle, et Redaxios pour les requêtes HTTP. Cette configuration de base peut être ajustée et optimisée selon les besoins de votre projet.

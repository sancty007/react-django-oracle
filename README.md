# projet-react-django

Configurer un projet avec Django, React.js et Oracle nécessite plusieurs étapes. Voici un guide général pour vous aider à configurer ce projet :

### Pré-requis

1. **Python et Django**: Assurez-vous d'avoir Python et Django installés.
2. **Node.js et npm/yarn**: Pour gérer React.js.
3. **Oracle Database**: Assurez-vous d'avoir accès à une base de données Oracle et les bons drivers pour la connexion.
4. **Outils de développement**: Visual Studio Code ou tout autre éditeur de code.

### 1. Configuration du projet Django

1. **Créer un environnement virtuel**:
   ```bash
   python -m venv venv
   source venv/bin/activate  # Sur Windows: venv\Scripts\activate
   ```

2. **Installer Django**:
   ```bash
   pip install django
   ```

3. **Créer un projet Django**:
   ```bash
   django-admin startproject myproject
   cd myproject
   ```

4. **Configurer la base de données Oracle**:
   - Installer cx_Oracle:
     ```bash
     pip install cx_Oracle
     ```

   - Ajouter la configuration de la base de données dans `settings.py`:
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
     ```

### 2. Configuration du projet React

1. **Créer une application React**:
   - Depuis la racine de votre projet Django :
     ```bash
     npx create-react-app frontend
     cd frontend
     ```

2. **Construire l'application React**:
   - Modifiez le fichier `package.json` pour inclure un script de construction:
     ```json
     "scripts": {
         "build": "react-scripts build"
     }
     ```

   - Construisez l'application:
     ```bash
     npm run build
     ```

### 3. Intégration de React dans Django

1. **Servir les fichiers React avec Django**:
   - Créez un dossier `static` dans le répertoire principal de votre projet Django et copiez le contenu du dossier `build` de votre application React dedans.

2. **Configurer Django pour servir les fichiers statiques**:
   - Ajoutez ou modifiez les paramètres suivants dans `settings.py`:
     ```python
     STATIC_URL = '/static/'
     STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
     ```

3. **Créer une vue pour rendre le fichier index.html de React**:
   - Dans votre application Django, créez une vue simple dans `views.py`:
     ```python
     from django.shortcuts import render

     def index(request):
         return render(request, 'index.html')
     ```

4. **Configurer les URLs**:
   - Modifiez `urls.py` pour inclure la nouvelle vue:
     ```python
     from django.urls import path
     from .views import index

     urlpatterns = [
         path('', index, name='index'),
     ]
     ```

5. **Assurez-vous que Django peut trouver le template**:
   - Ajoutez cette ligne dans `settings.py` pour inclure le dossier des templates de React:
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
   - Ouvrez votre navigateur et accédez à `http://localhost:8000`. Vous devriez voir votre application React servie par Django.

### 5. Déploiement (facultatif)

- Pour déployer votre application, vous devrez configurer un serveur web (comme Nginx ou Apache) pour servir votre application Django, et peut-être utiliser Docker pour la conteneurisation. Assurez-vous également que votre base de données Oracle est accessible depuis votre environnement de production.

Ce guide vous offre une configuration de base. Vous pouvez ajuster et optimiser selon les besoins de votre projet.

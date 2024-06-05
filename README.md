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

---

### 1. Ajouter Tailwind CSS à votre projet React

1. **Installer les dépendances nécessaires**:

   - Depuis le répertoire `frontend` de votre application React, installez Tailwind CSS, PostCSS et Autoprefixer:
     ```bash
     npm install -D tailwindcss postcss autoprefixer
     ```

2. **Créer les fichiers de configuration**:

   - Toujours dans le répertoire `frontend`, générez les fichiers de configuration pour Tailwind CSS et PostCSS:

     ```bash
     npx tailwindcss init -p
     ```

   - Cela créera deux fichiers: `tailwind.config.js` et `postcss.config.js`.

3. **Configurer Tailwind CSS**:

   - Ouvrez `tailwind.config.js` et configurez les chemins vers tous vos fichiers qui utiliseront Tailwind CSS:
     ```javascript
     /** @type {import('tailwindcss').Config} */
     module.exports = {
       content: ["./src/**/*.{js,jsx,ts,tsx}"],
       theme: {
         extend: {},
       },
       plugins: [],
     };
     ```

4. **Ajouter Tailwind CSS aux fichiers CSS**:

   - Dans `src/index.css`, ajoutez les directives Tailwind CSS:
     ```css
     @tailwind base;
     @tailwind components;
     @tailwind utilities;
     ```

5. **Tester l'installation**:

   - Ajoutez quelques classes Tailwind à un composant React pour vérifier que Tailwind CSS fonctionne correctement. Par exemple, modifiez `src/App.js`:
     ```jsx
     function App() {
       return (
         <div className="min-h-screen flex items-center justify-center bg-gray-100">
           <h1 className="text-3xl font-bold underline">
             Hello, Tailwind CSS!
           </h1>
         </div>
       );
     }
     export default App;
     ```

6. **Construire l'application React**:
   - Construisez l'application pour générer les fichiers statiques:
     ```bash
     npm run build
     ```

### 2. Intégrer les fichiers générés dans Django

1. **Copier les fichiers statiques**:

   - Copiez les fichiers générés dans le dossier `build` de votre application React vers le dossier `static` de votre projet Django:
     ```bash
     cp -r build/* ../static/
     ```

2. **Configurer Django pour servir les fichiers statiques**:

   - Ajoutez ou modifiez les paramètres suivants dans `settings.py` de votre projet Django:
     ```python
     STATIC_URL = '/static/'
     STATICFILES_DIRS = [os.path.join(BASE_DIR, 'static')]
     ```

3. **Créer une vue pour rendre le fichier `index.html` de React**:

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

### 3. Exécution du projet

1. **Démarrer le serveur Django**:

   ```bash
   python manage.py runserver
   ```

2. **Accéder à l'application**:
   - Ouvrez votre navigateur et accédez à `http://localhost:8000`. Vous devriez voir votre application React avec Tailwind CSS servie par Django.

### 4. Déploiement (facultatif)

Pour déployer votre application, vous devrez configurer un serveur web (comme Nginx ou Apache) pour servir votre application Django et peut-être utiliser Docker pour la conteneurisation. Assurez-vous également que votre base de données Oracle est accessible depuis votre environnement de production.

**Docker Configuration:**

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
   version: "3.8"

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

3. **Configuration Nginx (nginx.conf)**:

   ```nginx
   server {
       listen 80;

       location / {
           proxy_pass http://web:8000;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }

       location /static/ {
           alias /app/static/;
       }

       location /media/ {
           alias /app/media/;
       }
   }
   ```

4. **Construction et exécution des conteneurs Docker**:
   ```bash
   docker-compose up --build
   ```

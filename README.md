Pour une structure de projet plus simple et organisée, vous pouvez organiser votre projet en suivant cette structure :

```
myproject/
├── backend/
│   ├── manage.py
│   ├── myproject/
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   ├── wsgi.py
│   ├── app/
│   │   ├── __init__.py
│   │   ├── models.py
│   │   ├── views.py
│   │   ├── urls.py
├── frontend/
│   ├── package.json
│   ├── vite.config.js
│   ├── src/
│   │   ├── main.jsx
│   │   └── App.jsx
│   └── public/
│       └── index.html
├── .env
├── requirements.txt
└── docker-compose.yml
```

Voici comment vous pouvez configurer chaque partie :

### 1. Fichier `.env`

```plaintext
ORACLE_USER=your_username
ORACLE_PASSWORD=your_password
ORACLE_DB=XE
```

### 2. Fichier `docker-compose.yml`

```yaml
version: "3"

services:
  backend:
    image: python:3.10-slim
    container_name: django_app
    volumes:
      - ./backend:/app
    working_dir: /app
    command: sh -c "pip install -r requirements.txt && python manage.py runserver 0.0.0.0:8000"
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - oracle_db

  frontend:
    image: node:16
    container_name: react_app
    volumes:
      - ./frontend:/app
    working_dir: /app
    command: sh -c "yarn install && yarn dev"
    ports:
      - "3000:3000"

  oracle_db:
    image: gvenzl/oracle-xe
    container_name: oracle_db
    ports:
      - "1521:1521"
    environment:
      - ORACLE_PASSWORD=${ORACLE_PASSWORD}
      - ORACLE_USER=${ORACLE_USER}
      - ORACLE_SID=${ORACLE_DB}
```

### 3. Configuration de Django (`settings.py`)

```python
# backend/myproject/settings.py

import os

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.oracle',
        'NAME': os.environ.get('ORACLE_DB', 'XE'),
        'USER': os.environ.get('ORACLE_USER'),
        'PASSWORD': os.environ.get('ORACLE_PASSWORD'),
        'HOST': 'oracle_db',
        'PORT': '1521',
    }
}
```

### 4. Lancer les conteneurs Docker

Avec tout configuré, lancez vos conteneurs Docker :

```bash
docker-compose up --build
```

### 5. Vérifier la connexion de Django à Oracle

Pour vérifier que Django communique correctement avec Oracle, vous pouvez exécuter le shell Django et tester la connexion :

```bash
docker exec -it django_app python manage.py shell
```

Dans le shell Django :

```python
from django.db import connections
from django.db.utils import OperationalError

db_conn = connections['default']
try:
    c = db_conn.cursor()
    print("Database connection successful!")
except OperationalError:
    print("Database connection failed.")
```

### 6. Accéder aux dossiers avec VS Code

1. **Ouvrir VS Code dans le répertoire de votre projet** :

   ```bash
   code .
   ```

2. **Installer l'extension Docker pour VS Code** (si ce n'est pas déjà fait) :

   - Allez dans le menu des extensions (Ctrl+Shift+X).
   - Recherchez "Docker" et installez l'extension officielle Docker.

3. **Accéder aux conteneurs via l'extension Docker** :

   - Ouvrez le panneau Docker dans VS Code (Ctrl+Shift+P et tapez "Docker: Show Containers").
   - Vous pourrez voir vos conteneurs en cours d'exécution et interagir avec eux directement depuis VS Code.

En suivant ces étapes, vous aurez une structure de projet organisée et pourrez configurer votre environnement de développement avec Docker, connecter Django à une base de données Oracle et ajouter une application React avec Vite en utilisant les informations d'identification définies dans le fichier `.env`.

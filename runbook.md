Pour créer un runbook détaillé pour configurer et exécuter un projet avec Django (backend), React (frontend avec Vite), et Oracle (base de données) utilisant Docker, voici les étapes détaillées et les clarifications nécessaires :

### 1. Prérequis

Avant de commencer, assurez-vous d'avoir installé localement :

- Docker Engine : pour gérer les conteneurs Docker.
- Docker Compose : pour définir et gérer les applications multi-conteneurs.

### 2. Structure du projet

Assurez-vous que votre projet a une structure de répertoire de base comme celle-ci :

```
myproject/
├── backend/
│   ├── manage.py
│   ├── myproject/
│   │   ├── __init__.py
│   │   ├── settings.py
│   │   ├── urls.py
│   │   └── wsgi.py
│   ├── app/
│   │   ├── __init__.py
│   │   ├── models.py
│   │   ├── views.py
│   │   └── urls.py
├── frontend/
│   ├── package.json
│   ├── vite.config.js
│   ├── src/
│   │   ├── main.jsx
│   │   └── App.jsx
│   └── public/
│       └── index.html
└── docker-compose.yml
```

### 3. Configuration Docker Compose

Créez un fichier `docker-compose.yml` à la racine de votre projet pour définir les services Docker :

```yaml
version: "3"

services:
  backend:
    image: python:3.10-slim
    container_name: django_app
    volumes:
      - ./backend:/app
      - ./requirements.txt:/requirements.txt
    working_dir: /app
    command: sh -c "pip install -r /requirements.txt && python manage.py runserver 0.0.0.0:8000"
    ports:
      - "8000:8000"
    environment:
      - ORACLE_DB=XE
      - ORACLE_USER=your_username
      - ORACLE_PASSWORD=your_password
    depends_on:
      - oracle_db

  frontend:
    image: node:16
    container_name: react_app
    volumes:
      - ./frontend:/app
    working_dir: /app
    command: sh -c "pnpm install && pnpm dev"
    ports:
      - "3000:3000"

  oracle_db:
    image: gvenzl/oracle-xe
    container_name: oracle_db
    ports:
      - "1521:1521"
    environment:
      - ORACLE_PASSWORD=your_password
      - ORACLE_USER=your_username
      - ORACLE_SID=XE
```

### 4. Explications et détails

- **Backend (Django)** :

  - Utilisation de l'image Python 3.10 slim pour le conteneur.
  - Montage des volumes pour le code Django (`./backend`) et les dépendances (`./requirements.txt`).
  - Installation des dépendances et démarrage du serveur Django sur le port 8000.
  - Variables d'environnement pour la configuration de la base de données Oracle.

- **Frontend (React avec Vite)** :

  - Utilisation de l'image Node.js 16 pour le conteneur.
  - Montage du volume pour le code frontend (`./frontend`).
  - Installation des dépendances avec `pnpm` et démarrage du serveur de développement avec Vite sur le port 3000.

- **Base de données Oracle** :
  - Utilisation de l'image `gvenzl/oracle-xe` pour le conteneur Oracle XE.
  - Exposition du port 1521 pour la communication avec le conteneur.
  - Variables d'environnement pour configurer le mot de passe, l'utilisateur et le SID d'Oracle.

### 5. Exécution du projet

Pour démarrer votre projet, exécutez la commande suivante à la racine de votre projet :

```bash
docker-compose up --build
```

- `--build` est utilisé pour reconstruire les images Docker si nécessaire.

### 6. Accès aux services

- **Backend Django** : Accessible à `http://localhost:8000`.
- **Frontend React (Vite)** : Accessible à `http://localhost:3000`.
- **Oracle Database** : Disponible localement sur le port 1521 avec les informations d'identification configurées.

### 7. Maintenance et Développement

- Pour arrêter tous les conteneurs Docker, utilisez `Ctrl+C` dans le terminal où Docker Compose est en cours d'exécution.
- Pour nettoyer les conteneurs arrêtés et les réseaux non utilisés, utilisez `docker-compose down` après avoir arrêté les conteneurs.

### 8. Déploiement

Pour déployer ce projet dans un environnement de production, assurez-vous de :

- Utiliser des variables d'environnement sécurisées pour les secrets comme les mots de passe et les clés d'API.
- Configurer des volumes persistants pour les données de la base de données Oracle si nécessaire.
- Configurer un proxy ou un serveur web pour gérer le trafic entrant vers le backend Django.

En suivant ce runbook, vous devriez pouvoir configurer et exécuter efficacement votre projet avec Django, React (avec Vite) et Oracle dans des conteneurs Docker. Assurez-vous de personnaliser les noms d'utilisateur, les mots de passe et autres configurations selon les besoins spécifiques de votre application et de votre environnement.

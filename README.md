# TaskFlow Full Stack V6 Pro

TaskFlow V6 Pro est une application Full Stack de gestion de tâches et de projets, intégrant un frontend React et un backend Laravel exposant une API REST.

Cette version est conçue autour d'une architecture **multicontainer Docker**, orchestrée avec Docker Compose et utilisant Nginx comme reverse proxy.

## Architecture

Le projet repose sur cinq conteneurs Docker :

| Conteneur | Technologie | Rôle |
|---|---|---|
| laravel-app | Laravel 12 + Sanctum | Backend API REST |
| my-frontend | React 18 + MUI 7 | Interface utilisateur |
| nginx-server | Nginx | Reverse proxy |
| mysql-db | MySQL 8 | Base de données |
| phpmyadmin | phpMyAdmin | Administration de la base de données |

### Structure du projet

```text
TaskFlow-V6-Pro/
│
├── backend/
│   ├── Dockerfile.dev
│   └── ...
│
├── frontend/
│   ├── Dockerfile.dev
│   └── ...
│
├── nginx/
│   └── default.conf
│
├── mysql/
│   └── data/
│
├── docker-compose.yaml
└── README.md
```

## Prérequis

- Docker Engine
- Docker Compose
- Git

Vérifier les installations :

```bash
docker --version
docker compose version
```

## Installation et démarrage

### 1. Cloner le dépôt

```bash
git clone <URL_DU_DEPOT>
cd TaskFlow-V6-Pro
```

### 2. Configurer Laravel

Créer le fichier d'environnement :

```bash
cp backend/.env.example backend/.env
```

Configurer les paramètres de connexion MySQL dans `backend/.env` :

```env
DB_CONNECTION=mysql
DB_HOST=mysql-db
DB_PORT=3306
DB_DATABASE=todo_db
DB_USERNAME=root
DB_PASSWORD=root
```

**Important :** `DB_HOST` doit correspondre au nom du conteneur MySQL accessible sur le réseau Docker. Il ne faut pas utiliser `localhost`.

### 3. Construire et démarrer les conteneurs

À la racine du projet :

```bash
docker compose up -d --build
```

Cette commande construit les images et démarre les cinq conteneurs en arrière-plan.

### 4. Vérifier les services

```bash
docker compose ps
```

Pour consulter les logs :

```bash
docker compose logs -f
```

Pour consulter les logs d'un service :

```bash
docker compose logs -f laravel-container
docker compose logs -f frontend-container
docker compose logs -f nginx
docker compose logs -f mysql
```

### 5. Installer les dépendances Laravel

Si elles ne sont pas déjà installées pendant la construction de l'image :

```bash
docker compose exec laravel-container composer install
```

### 6. Initialiser l'application Laravel

Générer la clé de l'application :

```bash
docker compose exec laravel-container php artisan key:generate
```

Exécuter les migrations :

```bash
docker compose exec laravel-container php artisan migrate
```

La clé Laravel doit être conservée dans le fichier `.env` afin de ne pas être régénérée à chaque redémarrage.

## Accéder aux services

Une fois les conteneurs démarrés :

| Service | URL |
|---|---|
| Application TaskFlow | http://localhost:8080 |
| API Laravel | http://localhost:8080/api |
| phpMyAdmin | http://localhost:8081 |
| MySQL | localhost:3307 |

Pour phpMyAdmin, utiliser les identifiants configurés dans MySQL :

- Serveur : `mysql-db`
- Utilisateur : `root`
- Mot de passe : `root`

## Commandes Docker utiles

### Arrêter les conteneurs

```bash
docker compose stop
```

### Redémarrer les conteneurs

```bash
docker compose start
```

### Reconstruire et relancer les services

```bash
docker compose up -d --build
```

### Arrêter et supprimer les conteneurs

```bash
docker compose down
```

### Accéder au conteneur Laravel

```bash
docker compose exec laravel-container bash
```

### Exécuter une commande Artisan

```bash
docker compose exec laravel-container php artisan
```

### Vérifier les migrations

```bash
docker compose exec laravel-container php artisan migrate:status
```

### Nettoyer les caches Laravel

```bash
docker compose exec laravel-container php artisan optimize:clear
```

**Attention :** La commande `docker compose down -v` peut supprimer les volumes Docker. Dans cette configuration, les données MySQL sont également persistées dans `./mysql/data`. Ne supprimez pas ce dossier si vous souhaitez conserver vos données.

## Fonctionnalités

### Authentification et sécurité
- Authentification Laravel Sanctum.
- Gestion des rôles et permissions (RBAC).
- Protection des endpoints API.

### Gestion des tâches
- Création et modification des tâches.
- Filtres et recherche.
- Suivi de l'avancement.

### Gestion des projets
- Création et gestion des projets.
- Gestion des membres.
- Organisation des tâches par projet.

### Dashboard et Analytics
- Tableau de bord dynamique.
- Statistiques sur 7, 30 et 90 jours.

### Collaboration et productivité
- Recherche globale.
- Notifications persistantes.
- Inbox.
- Saved Views.
- Commentaires.
- Activity Log / Audit Log.

## Technologies utilisées

- React 18
- Material UI 7
- Axios
- Laravel 12
- Laravel Sanctum
- MySQL 8
- Nginx
- Docker
- Docker Compose
- phpMyAdmin

## Sécurité

- Le frontend ne constitue pas une frontière de sécurité.
- Les autorisations sur les projets, tâches, utilisateurs et ressources sont contrôlées côté backend.
- Laravel Sanctum protège les endpoints API avec des tokens Bearer.
- Les règles RBAC sont appliquées côté serveur.
- Les secrets et variables sensibles ne doivent pas être versionnés.
- Les données MySQL sont persistées sur l'hôte.

## Vérifications techniques

- Syntaxe PHP vérifiée avec `php -l` sur l'application, les routes et les migrations.
- Le build React n'a pas pu être exécuté dans l'environnement de génération, car l'installation des dépendances npm n'a pas terminé dans le délai disponible.

## Licence

TaskFlow Full Stack V6 Pro.
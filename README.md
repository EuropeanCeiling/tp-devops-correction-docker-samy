# TP DevOps Correction Docker

Correction de la partie Docker du module DevOps.

## Vérifier le projet

Prérequis : Java 21 et Maven 3.9. Depuis la racine du dépôt :

```sh
mvn clean verify
```

Les tests d'intégration utilisent une base H2 en mémoire ; Docker n'est pas nécessaire pour cette commande.

## Lancer l'application

Prérequis : Docker Desktop démarré. Depuis la racine du dépôt :

```sh
docker compose up --build -d
```

L'API est accessible via le proxy sur `http://localhost/departments` et `http://localhost/students`.
Pour consulter l'état des services : `docker compose ps`.

## Publication des images

Le workflow GitHub Actions construit les images `tp-devops-simple-api`, `tp-devops-database` et `tp-devops-httpd` après la réussite des tests. Il les publie sur Docker Hub uniquement lors d'un push sur `main`.

Avant de publier, ajouter deux secrets dans **Settings → Secrets and variables → Actions** du dépôt GitHub :

- `DOCKERHUB_USERNAME` : nom du compte Docker Hub ;
- `DOCKERHUB_TOKEN` : jeton d'accès Docker Hub du compte.

Les secrets évitent d'inscrire des identifiants dans le dépôt et dans le workflow. Le lien `needs: test-backend` empêche la publication si les tests échouent. La publication rend les images récupérables depuis Docker Hub sans reconstruire le projet localement.

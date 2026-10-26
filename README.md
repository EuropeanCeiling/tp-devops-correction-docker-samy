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

Le front est disponible sur `http://localhost:8080/`. L'API est accessible via le proxy, par exemple `http://localhost:8080/api/students` et `http://localhost:8080/api/departments`. Le port hôte peut être changé avec `HTTP_PORT` dans `.env`.
Pour consulter l'état des services : `docker compose ps`.

Définir `POSTGRES_PASSWORD` dans un fichier `.env` à la racine (ce fichier est ignoré par Git), puis démarrer la stack :

```sh
printf 'POSTGRES_PASSWORD=change-me\n' > .env
docker compose up --build -d
```

Le mot de passe doit rester identique pour une base déjà créée dans le volume `db-volume`.

## Publication des images

Le workflow CI teste le backend sur `main` et `develop` (les branches présentes dans ce dépôt ; `main` correspond à `master` dans l'énoncé). Sur les pushes, il attend aussi le résultat de la Quality Gate SonarCloud. Le workflow de publication démarre uniquement après une CI réussie sur un push vers `main`, puis construit et publie `tp-devops-simple-api`, `tp-devops-database` et `tp-devops-httpd` sur Docker Hub, avec un tag immuable correspondant au commit et le tag `latest`. Il déploie ensuite les images API et front/proxy sur le serveur distant avec Ansible et vérifie que `/` et `/api/students` répondent.

Avant de publier, ajouter deux secrets dans **Settings → Secrets and variables → Actions** du dépôt GitHub :

- `DOCKERHUB_USERNAME` : nom du compte Docker Hub ;
- `DOCKERHUB_TOKEN` : jeton d'accès Docker Hub du compte.

Pour activer le déploiement continu, ajouter aussi les secrets suivants :

- `POSTGRES_PASSWORD` : mot de passe utilisé pour initialiser PostgreSQL ; il doit rester identique pour le volume existant ;
- `DEPLOY_HOST` : nom DNS ou adresse IP du serveur ;
- `DEPLOY_USER` : utilisateur SSH du serveur ;
- `DEPLOY_SSH_PRIVATE_KEY` : clé privée SSH autorisée sur le serveur ;
- `DEPLOY_KNOWN_HOSTS` : ligne `known_hosts` vérifiée du serveur. Ne désactive pas la vérification de clé SSH.

Les images Docker Hub doivent être publiques pour que le serveur puisse les télécharger sans identifiants de registre. Après un push vers `main`, suivre les workflows **CI devops 2025** et **Publish Docker images** dans l'onglet Actions. Le premier valide le commit ; le second publie les images, les déploie et teste le front et l'API.

Les secrets évitent d'inscrire des identifiants dans le dépôt et dans le workflow. Le déclencheur `workflow_run` attend que les tests et la Quality Gate réussissent avant de publier le commit vérifié. La publication rend les images récupérables depuis Docker Hub sans reconstruire le projet localement.

## Quality Gate SonarCloud

Importer ce dépôt comme projet dans l'organisation SonarCloud `europeanceiling`, puis configurer dans **Settings → Secrets and variables → Actions** sur GitHub :

- secret `SONAR_TOKEN` : jeton SonarCloud autorisé à analyser le projet ;

La clé du projet `EuropeanCeiling_tp-devops-correction-docker-samy` et la clé d'organisation `europeanceiling` sont définies dans le workflow : ce sont des identifiants publics, pas des secrets.

La CI exécute l'analyse après les tests sur les pushes vers `main` et `develop`. Elle attend la décision de la Quality Gate ; un échec empêche le workflow séparé de publier les images. Les pull requests lancent les tests sans utiliser les secrets SonarCloud.

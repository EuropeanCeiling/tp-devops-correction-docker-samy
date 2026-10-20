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

## Introduction

<!-- This document is a user guide to Concrete Datastore and its features. -->

Ce document est un guide de l'utilisateur pour Concrete Datastore et ses fonctionnalités.

<!-- Concrete Datastore is a Datastore generator that consumes a datamodel file containing a database description, and dynamically generates the corresponding tables. -->

Concrete Datastore est un générateur de DataStore qui utilise un fichier de modèles de données contenant une déscription de base de données, et génère dynamiquement les tables correspondantes.

<!-- Concrete Datastore exposes a RESTful API to allow its users to interact with the database by **C**reating, **R**etrieving, **U**pdating and **D**eleting data (**CRUD**) -->.

Concrete Datatstore propose une API RESTful pour permettre à ses utilisateurs d'intéragir avec la base de données en créant, lisant, mettant à jour et supprimant des données (**CRUD**).

<!-- In order to gain access or modify the database’s data, each request must be authenticated with a unique token for each user (see [authentication](authentication.md) section). This allows Concrete Datastore to know which user is using the platform. The desired actions depend on the user’s permissions (see [permissions](permissions.md) and [roles](roles.md) sections). -->

Pour avoir accès ou modifier les données de la base de données, chaque requête doit être authentifié avec un token unique pour chaque utilisateur (voir la section [authentification](authentication.md)). Celà permet à Concrete Datastore de connaître quel utilisateur utilise la plateforme. Les actions désirées dépendent des permissions de l'utilisateur (voir les sections [permissions](permissions.md) et [roles](roles.md)).

<!-- Concrete Datastore also allows its users to have full control over the data requested by enabling a filtering mechanism on the different elements of the database (see [filters](filters.md) section). -->

Concrete Datastore permet aussi à ses utilisateurs d'avoir un contrôle complet sur la données requise en permettant un mécanisme de  de filtrage sur les différents éléments de la base de données (voir la section [filtres](filters.md)).

## Table des matières

- [API Authentification](authentication.md)
- [API Routes](api-routes.md)
- [API Filtres](filters.md)
- [API Permissions](permissions.md)
- [API Roles](roles.md)
- [Tutoriels](demo.md)

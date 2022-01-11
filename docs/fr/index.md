## Introduction

Ce document est un guide utilisateur pour Concrete Datastore et ses fonctionnalités.

Concrete Datastore est un générateur de DataStore qui utilise un fichier de modélisation contenant une description de base de données, et génère dynamiquement les tables correspondantes.

Concrete Datastore propose une API RESTful pour permettre à ses utilisateurs d'intéragir avec la base de données en créant, lisant, modifiant et supprimant des données (**CRUD**).

Pour avoir accès ou modifier les données de la base de données, chaque requête doit être authentifiée avec un token unique pour chaque utilisateur (voir la section [authentification](authentication.md)). Celà permet à Concrete Datastore de connaître quel utilisateur utilise la plateforme. Les actions désirées dépendent des permissions de l'utilisateur (voir les sections [permissions](permissions.md) et [roles](roles.md)).

Concrete Datastore permet aussi à ses utilisateurs d'avoir un contrôle complet sur la données requise en permettant un mécanisme de filtrage sur les différents éléments de la base de données (voir la section [filtres](filters.md)).

## Table des matières

- [Authentification à l'API](authentication.md)
- [Routes à l'API](api-routes.md)
- [Filtres à l'API](filters.md)
- [Permissions à l'API](permissions.md)
- [Roles à l'API](roles.md)
- [Tutoriels](demo.md)

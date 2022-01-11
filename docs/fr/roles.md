## Rôles et Permissions Concrete

**IMPORTANT:** Les rôles et permissions Concrete sont uniquement activés si USE_CONCRETE_ROLES est à true dans les settings.


### Rôles Concrete

`ConcreteRole` et un modèle Concrete Datastore qui permet d'attribuer différentes permissions pour des utilisateurs du même niveau. (**les admins et superusers n'ont pas leur permissions changées par les rôles, cela affecte uniquement les simpleuser ou les managers de la platforme.**)

Chaque instance du modèle ConcreteRole à un nom de rôle et une liste d'utilisateurs.

![](./assets/concrete-roles.png)

`ConcreteRole` est associée (M2M) à [`ConcrerePremissions`](#ConcretePermissions) afin de définir les règles de permissions.

À noter que les rôles concrete et les permissions n'ajoutent aucune permission à un utilisateur, il restreint uniquement ses permissions par défault.

### <a name="ConcretePermissions"></a>Concrete permission

`ConcretePermission` est un modèle Concrete Datastore qui permet de spécifier un ensemble de permissions pour chaque modèle du datamodel, dépendant des rôles définis dans `ConcreteRole`. Ces permissions correspondent avec les opérations **CRUD** (**C**reate, **R**etrieve, **U**pdate, **D**elete).

![](./assets/concrete-permissions.png)

Pour les simpleusers et managers, afin d'éffectuer une opération **CRUD**, un utilisateur doit avoir un rôle attribué pour réaliser cette opération (voir l'exemple ci-dessous pour plus de détails).

### Exemple

Ayant trois utilisateurs, un `SimpleUser`, un `Manager_1` et un `Manager_2`.

Pour un modèle `MonModèle` avec un `minimum_creation_level` défini sur manager, les deux managers sont autorisées a créer de nouvelles instances de `MonModèle` pendant que le simpleuser n'y est pas autorisé.

Deux rôles sont maintenant ajoutés :

-  **Créateur**: qui à comme utilisateurs `SimpleUser` et `Manager_1`.
-  **Observateur**: qui à seulement `Manager_2` comme utilisateur.

On ajoute ensuite une permission :

```json
{
    "model_name": "MyModel",
    "create_roles": {
        "roles": ["<Creator_uid>"]
    }
}
```

- Avec cette permission ajoutée, le seul utilisateur qui est autorisé a créer des instances de `MonModèle` est le `Manager_1` car il fait partie du rôle du `Créateur`.

- Même si `Manager_2` a le niveau minimum pour créer des instances, il n'a pas le rôle requis pour le faire.

Le `SimpleUser` a le rôle de créateur qui est autorisé à créer des instances, mais il n'a pas le niveau minimum requis par le modèle, il ne peut dont pas effectuer de create sur ce modèle.

<!-- ## Concrete Roles and Concrete Permissions

**IMPORTANT:** Concrete Roles and permissions are only enabled if USE_CONCRETE_ROLES is True in settings

### Concrete Roles

`ConcreteRole` is a concrete datastore model that allows to attribute different permissions for users of the same level. (**admins and superusers don't have their permissions changed by the roles, that only affects simpleusers and managers of the platform.**)

Each instance of model ConcreteRole has a role name and a list of users.

![](./assets/concrete-roles.png)

`ConcreteRole` is associated (M2M) to [`ConcrerePremissions`](#ConcretePermissions) in order to set the permissions rules.

Note that concrete roles and permissions don't give additional permissions to a user, it only restricts his default permissions. -->

## Rôles et Permissions Concrete

**IMPORTANT:** Les rôles et permissions Concrete sont uniquement activés si USE_CONCRETE_ROLES est à true dans les settings.


### Rôles Concrete

`ConcreteRole` et un modèle Concrete Datastore qui permet d'attribuer différentes permissions pour des utilisateurs du même niveau. (**les admins et superusers n'ont pas leur permissions changées par les rôles, cela n'affecte uniquement que les simpleuser ou les managers de la platforme.**)

Chaque instance du modèle ConcreteRole à un nom de rôle et une liste d'utilisateurs.

![](./assets/concrete-roles.png)

`ConcreteRole` est associée (M2M) à [`ConcrerePremissions`](#ConcretePermissions) afin de définir les règles de permissions.

À noter que les rôles concrete et les permissions n'ajoutent aucune permission à un utilisateur, il restreint uniquement ses permissions par défault.

### <a name="ConcretePermissions"></a>Concrete permission

`ConcretePermission` est un modèle Concrete Datastore qui permet de spécifier un ensemble de permissions pour chaque modèle du datamodel, dépendant des rôles définis dans `ConcreteRole`. Ces permissions correspondent avec les opérations **CRUD** (**C**reate, **R**etrieve, **U**pdate, **D**elete).

![](./assets/concrete-permissions.png)

Pour les simpleusers et managers, afin d'éffectuer une opération **CRUD**, un utilisateur doit avoir un rôle attribué pour réaliser cette opération (voir l'exemple ci-dessous pour plus de détails).

<!-- ### <a name="ConcretePermissions"></a>Concrete permission

`ConcretePermission` is a concrete datastore model that allows to specify a set of permissions for each model of the datamodel definition, depending on the roles defined in ConcretRole. These permissions match with the **CRUD** operations (**C**reate, **R**etrieve, **U**pdate, **D**elete)

![](./assets/concrete-permissions.png)

For simpleusers and managers, in order to perform a **CRUD** operation, a user must have a role allowed to perform this operation (see example below for more details). -->

### Exemple

Avoir trois utilisateurs, un `SimpleUser`, un `Manager_1` et un `Manager_2`.

Avoir un modèle `MonModèle` avec un `minimum_creation_level` défini sur manager, les deux managers sont autorisées a créer de nouvelles instances de `MonModèle` pendant que le simpleuser n'y est pas autorisé.

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



<!-- ### Example

Having three users, a `SimpleUser`, a `Manager_1` and a `Manager_2`.

Given a model `MyModel` with a `minimum_creation_level` set to manager, both of the managers are allowed to create new instances of  `MyModel`, while the simpleuser is not allowed to.

Two roles are now added:

-  **Creator**: having as users `SimpleUser` and `Manager_1`.
-  **Watcher**: having only `Manager_2` as a user.

We then add a new permission:

```json
{
    "model_name": "MyModel",
    "create_roles": {
        "roles": ["<Creator_uid>"]
    }
}
```

-  With this permission added, the only user that is allowed to create instances of `MyModel` is the `Manager_1` because he belongs to the `Creator` role.

-  Even if `Manager_2` has the minimum required level to create instances, he does not have the required role to do so.

-  The `SimpleUser` has a role of `Creator` that is allowed to create new instances, but he does not have the minimun level required by the model, so he cannot perform a create on this model.
 -->
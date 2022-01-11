<!-- ## Permissions

Multiple parameters are put together to determine the permissions that were granted for each user:

- Request Type (scoped/unscoped)
- Model and objects definition
- Scopes of user
- User Level

These criteria are ordered by strength (from the strongest criterion to the weakest).
 -->
### Permissions

De multiples paramètres s'assemblent afin de déterminer les permissions qui ont été accordées à chaque utilisateur :

- Type de requête (scopée/non-scopée)
- Modèle et définition d'objets
- Scopes of user
- Niveau d'utilisateur

Ces critères sont triés par force (du critère le plus fort au plus faible).

### Type de requête

Quand on éffectue une requête vers l'API Concrete (get, post, patch, delete), le scope (`X-ENTITY-UID`) peut être ajouté aux headers de la requête :

Le scope passé dans les headers de la requête est utilisé pour filtrer les objets de la réponse, et permet d'accéder uniquement aux objets qui contiennent le même scope.

<!-- ### Request Type

When performing a request to the API Concrete (get, post, patch, delete), the scope (`X-ENTITY-UID`) can be added to the headers of the request:

The scope passed in the headers of a request is used to filter the response's objects, and allows access only to the objects having the same scope. -->

#### Requête Unscoped

```shell
curl \
  -X GET \
  -H "Authorization: Token b1cccee29710b53386b964d93847648a526005a8" \
  "https://<webapp>/api/v1.1/<model-name>/"
```

<!-- #### Unscoped Request

```shell
curl \
  -X GET \
  -H "Authorization: Token b1cccee29710b53386b964d93847648a526005a8" \
  "https://<webapp>/api/v1.1/<model-name>/"
``` -->


#### Requête Scoped

```shell
curl \
  -X GET \
  -H "Authorization: Token b1cccee29710b53386b964d93847648a526005a8" \
  -H "X-ENTITY-UID: 7508ffd7-2d10-413e-86b6-4adab3767413" \
  "https://<webapp>/api/v1.1/<model-name>/"
```

**Exemple:** Si on prend deux scopes `scopeA` et `scopeB` et un modèle `MonModel` accessible par une `url`. En leur donnant les objets :

- `objet1: scope=scopeA`
- `objet2: scope=scopeB`
- `objet3: scope=Aucun`
- `objet4: scope=scopeA`

Un SuperUser avec un token `super_token` fait en sorte que la requête récupère une liste d'objet du mondèle `MonModele`.

- **Reqête Unscoped:** `requests.get(url,headers={"Authorization":"Token {}".format(super_token)})` retourne une liste d'objets avec une reponse JSON, contenant les quatre objets.

- **Requête Scoped:** `requests.get(url,headers={"Authorization":"Token {}".format(super_token),"X-ENTITY-UID":scopeA.uid})` retourne une liste contenant uniquement les objets avec le scope  `scopeA` (ici, l'`object1` et l'`object4`).

<!-- #### Scoped Request

```shell
curl \
  -X GET \
  -H "Authorization: Token b1cccee29710b53386b964d93847648a526005a8" \
  -H "X-ENTITY-UID: 7508ffd7-2d10-413e-86b6-4adab3767413" \
  "https://<webapp>/api/v1.1/<model-name>/"
```

**Example:** given two scopes `scopeA` and `scopeB` and a model `MyModel` accessible through a `url`. Given objects:

- `object1: scope=scopeA`
- `object2: scope=scopeB`
- `object3: scope=None`
- `object4: scope=scopeA`

A SuperUser with a token `super_token` makes the request to get a list of the objects of the model `MyModel`.

- **Unscoped Request:** `requests.get(url,headers={"Authorization":"Token {}".format(super_token)})` returns a list of objects within a JSON response, containing all of the four objects.

- **Scoped Request:** `requests.get(url,headers={"Authorization":"Token {}".format(super_token),"X-ENTITY-UID":scopeA.uid})` returns a list containing only the objects with the scope `scopeA` (i.e only `object1` and `object4`). -->

<!-- ### Model and Object Definition -->

### Modèle et Définition d'Objet

#### Définition du Modèle

Pour un modèle, certaines contraintes au niveau de l'utilisateur sont définies pour déterminer si un utilisateur donné peut effectuer une opération donnée (créer, supprimer, lire et mettre à jour) ou pas :


```json
{
  "ext.m_creation_minimum_level": "admin",
  "ext.m_delete_minimum_level": "superuser",
  "ext.m_retrieve_minimum_level": "authenticated",
  "ext.m_update_minimum_level": "manager"
}
```

<!-- #### Model Definition

For a model, some user level constraints are set to determine whether a given user can perform a given operation (create, delete, retrieve and update) or not:

```json
{
  "ext.m_creation_minimum_level": "admin",
  "ext.m_delete_minimum_level": "superuser",
  "ext.m_retrieve_minimum_level": "authenticated",
  "ext.m_update_minimum_level": "manager"
}
```
 -->
#### Définition Objet

Les objets peuvent avoir des arguments qui permettent à un utilisateur (ou à un groupe d'utilisateurs) d'effectuer quelques opérations qu'il ne peut d'habitude pas effectuer :

- `can_view_users`, `can_view_groups`: donnent `Lire` comme droits à un utilisateur ou à un groupe d'utilisateurs.
- `can_admin_users`, `can_admin_groups`: donnent `Lire` et `MettreAJour` comme droits à un utilisateur ou à un groupe d'utilisateurs.
- `public`: est un boolean. Si défini à faux, un utilisateur avec le niveau `manager`ou moins ne peut pas avoir accès à cet objet à moins qu'il en soit le créateur (`created_by`) ou listé dans `can_admin_[users/groups]` ou `can_view_[users/groups]`.

<!-- #### Object Definition

Objects can have arguments that allow a user (or a group of users) to perform some operations that he can't normally perform:


- `can_view_users`, `can_view_groups`: gives `Retrieve` rights to a user or a group of users.
- `can_admin_users`, `can_admin_groups`: gives `Retrieve` and `Update` rights to a user or a group of users.
- `public`: a boolean. If set to false, a user with level `manager` or less can't have access to this object unless he is the owner (`created_by`) or listed in `can_admin_[users/groups]` or `can_view_[users/groups]`. -->


### Niveau d'utilisateurs

Il existe cinq niveaux d'utilisateur avec un ensemble d'autorisations et de privilèges :

SuperUser > Admin > Manager > SimpleUser > Blocked

- Le `SuperUser` a accès a tout et a tout les droits.
- Les autres niveaux ont des autorisations de création et de récupération différentes, selon le modèle et l'instance créée.
- L'utilisateur `Blocked` n'a accès à rien.

<!-- ### User levels

There are five user levels with a set of permissions and privileges:

Superuser > Admin > Manager > Simpleuser > Blocked

- The `superuser` has access to everything and has all the rights.
- The other levels have different create and retrieve permissions, depending on the model and the created instance.
- The `blocked` user has access to nothing.
 -->

<!-- ### User Scopes

A user can have one or more scopes that are used to determine the range of objects accessible to him.

A user of an `admin` level or higher has access to all the objects, independently of his scopes.

For a level `manager` or less, if the user has a scope `A`, he can access to all the objects having a scope `A` (and only these objects).
The possible operations on these objects are those that satisfy the model's definition. -->

### Scopes d'utilisateur

Un utilisateur peut avoir un ou plusieurs scopes qui sont utilisés pour déterminer la gamme d'objets qui lui sont accessibles.

Un utilisateur de niveau `admin` ou supérieur a accès à tous les objets, indépendamment de ses scopes.

Pour un niveau `manager` ou moins, si l'utilisateur a un scope `A`, il peut accéder à tous les objets ayant un scope `A` (et seulement ces objets).
Les opérations possibles sur ces objets sont celles qui correspondent à la définition du modèle.


### Exemple

Étant donné deux scopes : `Diviseur_X` et `Diviseur_Y`.

Compte tenu des utilisateurs suivants :

- **SuperUser**: sans les scopes.
- **Admin**: sans les scopes.
- **Manager**: sans les scopes.
- **Manager_X**: avec le scope `Diviseur_X`.
- **Manager_Y**: avec le scope `Diviseur_Y`.
- **Manager_XY**: avec les scopes `Diviseur_X` et `Diviseur_Y`.
- **SimpleUser**: sans les scopes.
- **SimpleUser_X**: avec le scope `Diviseur_X`.
- **SimpleUser_Y**: avec le scope `Diviseur_Y`.
- **SimpleUser_XY**: avec les scopes `Diviseur_X` et `Diviseur_Y`.

Étant donné un modèle divisé « MyModel » et quatre instances de ce modèle avec la configuration suivante :

![](./assets/permissions-example.png)

<!-- ### Example

Given two scopes: `Divider_X` and `Divider_Y`

Given the following users:

- **SuperUser**: without scopes.
- **Admin**: without scopes.
- **Manager**: without scopes.
- **Manager_X**: with scope `Divider_X`.
- **Manager_Y**: with scope `Divider_Y`.
- **Manager_XY**: with scopes `Divider_X` and `Divider_Y`.
- **SimpleUser**: without scopes.
- **SimpleUser_X**: with scope `Divider_X`.
- **SimpleUser_Y**: with scope `Divider_Y`.
- **SimpleUser_XY**: with scopes `Divider_X` and `Divider_Y`.


Given a Divided Model `MyModel` and four instances of this model with the following configuration:

![](./assets/permissions-example.png) -->



#### 1er cas : Pour une requête Scoped Avec le Scope Diviseur_X :

- **SuperUser:** peut accéder à `instance_1` et `instance_3` et a tout les droits.
- **Admin:** peut accéder à `instance_1` et `instance_3` et a les droits pour lire, créer et mettre à jour les droits.
- **Manager:** peut accéder à `instance_3` (à cause de `can_view_users`) avec les droits de lecture et `instance_1`(à cause de `can_admin_users`) avec comme droits lire et mettre à jour.
- **Manager_X**: peut accéder à `instance_1` et `instance_3` (à cause du scope) et à comme droits lire et mettre à jour.
- **Manager_Y**: peut accéder uniquement à `instance_3` (à cause de `can_admin_users`) et a comme droits lire et mettre à jour.
- **Manager_XY**: peut accéder à `instance_1` et `instance_3` et a comme droits lire et mettre à jour.
- **SimpleUser**: peut accéder uniquement à `instance_1` (à cause de `can_view_users`) et à seulement lire comme droits.
- **SimpleUser_X**: peut accéder à `instance_1` et `instance_3` (à cause du scope) et a uniquement lire comme droit.
- **SimpleUser_Y**: peut accéder à rien.
- **SimpleUser_XY**: peut accéder à `instance_1` et `instance_3` (à cause du scope) et a uniquement lire comme droit.

<!-- #### 1st case: For A Scoped Request With The Scope Divider_X:

- **SuperUser:** can access to `instance_1` and `instance_3` and has all the rights.
- **Admin:** can access to `instance_1` and `instance_3` and has retrieve, create and update rights

- **Manager:** can access to `instance_3` (because of `can_view_users`) with retrieve rights and `instance_1`(because of `can_admin_users`) with retrieve and update rights
- **Manager_X**: can access to `instance_1` and `instance_3` (because of scope) and has retrieve and update rights
- **Manager_Y**: has access only to `instance_3` (because of `can_admin_users`) and has retrieve and update rights.
- **Manager_XY**: can access to `instance_1` and `instance_3` and has retrieve and update rights
- **SimpleUser**: has access only to `instance_1` (because of `can_view_users`) and has only retrieve rights
- **SimpleUser_X**: has access to `instance_1` and `instance_3` (because of scope) and has only retrieve rights
- **SimpleUser_Y**: has access to nothing
- **SimpleUser_XY**: has access to `instance_1` and `instance_3` (because of scope) and has only retrieve rights -->



#### 2nd cas : Pour une Requête Scoped Avec le Scope Diviseur_Y :

- **SuperUser:** peut accéder à `instance_2` et a tout les droits.
- **Admin:** peut accéder à `instance_2` et à comme droits creér et mettre à jour.
- **Manager:** n'a accès à rien.
- **Manager_X**: peut accéder à `instance_2` (à cause de `can_view_users`) et a uniquement des droits pour lire.
- **Manager_Y**: a accès à `instance_2` (à cause du scope) et a comme droits lire et mettre à jour.
- **Manager_XY**: peut accéder à `instance_2` et à comme droits lire et mettre à jour.
- **SimpleUser**: has access `instance_2` (à cause de `can_admin_users`) et a uniquement lire comme droit.
- **SimpleUser_X**: n'a accès à rien.
- **SimpleUser_Y**: a accès à `instance_2` avec lire comme droit.
- **SimpleUser_XY**: a accès à `instance_2` (à cause du scope) et a uniquement lire comme droit.




<!-- #### 2nd case: For A Scoped Request With The Scope Divider_Y:

- **SuperUser:** can access to `instance_2` and has all the rights.
- **Admin:** can access to `instance_2` and has retrieve, create and update rights
- **Manager:** has access to nothing
- **Manager_X**: can access to `instance_2` (because of `can_view_users`) and has only retrieve rights
- **Manager_Y**: has access to `instance_2` (because of scope) and has retrieve and update rights.
- **Manager_XY**: can access to `instance_2` and has retrieve and update rights
- **SimpleUser**: has access `instance_2` (because of `can_admin_users`) and has only retrieve rights
- **SimpleUser_X**: has access to nothing
- **SimpleUser_Y**: has access to `instance_2` with retrieve rights
- **SimpleUser_XY**: has access to `instance_2` (because of scope) and has only retrieve rights -->

#### 3ème cas : Pour une Requête Non-Scoped :

- **SuperUser:** peut accéder à tout et a tout les droits.
- **Admin:** peut accéder à tout et a comme droits lire, créer et mettre à jour.
- **Manager:** peut accéder à `instance_3` (à cause de `can_view_users`) avec le droit de lire et `instance_1`(à cause de `can_admin_users`) avec les doits de lire et de mettre à jour.
- **Manager_X**: peut accéder à `instance_2` (à cause de `can_view_users`) avec seulement le droit de lire, et peut accéder à `instance_1` et `instance_3` (à cause du scope) avec les doits de lire et de mettre à jour.
- **Manager_Y**: a accès à `instance_2` (à cause du scope) et à `instance_3` (à cause de `can_admin_users`) et à les doits de lire et de mettre à jour.
- **Manager_XY**: peut accéder à tout et à les doits de lire et de mettre à jour.
- **SimpleUser**: a accès à `instance_2` (à cause de `can_admin_users`) et `instance_1` (à cause de `can_view_users`) et n'a que le droit de lire.
- **SimpleUser_X**: a accès à `instance_1` et `instance_3` (à cause du scope) tout comme `instance_4` (à cause de public) et n'a que le droit de lire.
- **SimpleUser_Y**: a accès à `instance_2` (à cause du scope) et `instance_4` (à cause de public) avec le droit de lire.
- **SimpleUser_XY**: a accès à tout (à cause du scopes et public) et n'a que le droit de lire.


<!-- #### 3rd case: For A Non-Scoped Request:

- **SuperUser:** can access to everything and has all the rights.
- **Admin:** can access to everything and has retrieve, create and update rights
- **Manager:** can access to `instance_3` (because of `can_view_users`) with retrieve rights and `instance_1`(because of `can_admin_users`) with retrieve and update rights
- **Manager_X**: can access to `instance_2` (because of `can_view_users`) with only retrieve rights, and can access to `instance_1` and `instance_3` (because of scope) with retrieve and update rights
- **Manager_Y**: has access to `instance_2` (because of scope) and to `instance_3` (because of `can_admin_users`) and has retrieve and update rights.
- **Manager_XY**: can access to everything and has retrieve and update rights
- **SimpleUser**: has access `instance_2` (because of `can_admin_users`) and `instance_1` (because of `can_view_users`) and has only retrieve rights
- **SimpleUser_X**: has access to `instance_1` and `instance_3` (because of scope) as well as `instance_4` (because of public) and has only retrieve rights
- **SimpleUser_Y**: has access to `instance_2` (because of scope) and `instance_4` (because of public) with retrieve rights
- **SimpleUser_XY**: has access to everything (because of scopes and public) and has only retrieve rights -->


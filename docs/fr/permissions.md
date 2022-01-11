### Permissions

De multiples paramètres s'assemblent afin de déterminer les permissions qui ont été accordées à chaque utilisateur :

- Type de requête (scopée/non-scopée)
- Modèle et définition d'objets
- Scopes of user
- Niveau d'utilisateur

Ces critères sont triés par force (du critère le plus fort au plus faible).

### Type de requête

Quand on éffectue une requête vers l'API Concrete (get, post, patch, delete), le scope (`X-ENTITY-UID`) peut être ajouté aux entêtes de la requête :

Le scope passé dans les entêtes de la requête est utilisé pour filtrer les objets de la réponse, et permet d'accéder uniquement aux objets qui contiennent le même scope.

#### Requête Unscoped

```shell
curl \
  -X GET \
  -H "Authorization: Token b1cccee29710b53386b964d93847648a526005a8" \
  "https://<webapp>/api/v1.1/<model-name>/"
```

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

#### Définition Objet

Les objets peuvent avoir des arguments qui permettent à un utilisateur (ou à un groupe d'utilisateurs) d'effectuer quelques opérations qu'il ne peut d'habitude pas effectuer :

- `can_view_users`, `can_view_groups`: donnent `Lire` comme droits à un utilisateur ou à un groupe d'utilisateurs.
- `can_admin_users`, `can_admin_groups`: donnent `Lire` et `MettreAJour` comme droits à un utilisateur ou à un groupe d'utilisateurs.
- `public`: est un boolean. Si défini à faux, un utilisateur avec le niveau `manager`ou moins ne peut pas avoir accès à cet objet à moins qu'il en soit le créateur (`created_by`) ou listé dans `can_admin_[users/groups]` ou `can_view_[users/groups]`.

### Niveau d'utilisateurs

Il existe cinq niveaux d'utilisateur avec un ensemble d'autorisations et de privilèges :

SuperUser > Admin > Manager > SimpleUser > Blocked

- Le `SuperUser` a accès a tout et a tout les droits.
- Les autres niveaux ont des autorisations de création et de récupération différentes, selon le modèle et l'instance créée.
- L'utilisateur `Blocked` n'a accès à rien.

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

## Routes, méthodes et réponses de l'API

Pour toutes les routes listées plus bas, la réponse de l'API à les **codes de statut d'erreur** suivant :

- `401 UNAUTHORIZED`: si l'utilisateur n'a pas les droits d'accès à la donnée.
- `403 FORBIDDEN`: si l'utilisateur n'est pas authentifié à l'API
- `404 NOT FOUND`: si l'url n'est pas trouvée.

### Points d'entrée de l'API liés aux modèles

Comme expliqué dans l'introduction, Concrete Datastore utilise une modélisation définie afin de générer une API donnant accès aux instances du datastore.
Pour chaque modèle, Concrete Datastore génère des points d'entrée qui permettent à l'utilisateur d'effectuer des méthodes **CRUD**. Ce point d'entrée sont des `kebab-case` (en minuscule avec des tirets) du nom du model. Par exemple si un model est nommé `MyModel`, l'point d'entrée de l'API sera `my-model`.

Pour chaque modèle, Concrete Datastore propose deux routes acceptant différentes méthodes :

#### Liste toutes les instances du modèle MyModel

Un `GET` sur l'url racine du modèle MyModel will récupèrera toutes les instances de ce modèle.

- **Méthode**: `GET`

- **Point d'entrée**: `https://<webapp>/api/v1.1/my-model/`

- **Exemple**:

**Requête** 

```shell
curl \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/my-model/"
```

**Réponse** : avec le code de statut HTTP `200 (OK)`, le corps de la réponse est un JSON contenant les détails de toutes les instances disponibles. La réponse est paginée.

```json
{
  "objects_count": 125,
  "next": "https://<webapp>/api/v1.1/my-model/?page=2",
  "previous": null,
  "results": [
    ...
  ],
  "objects_count_per_page": 125,
  "num_total_pages": 22,
  "num_current_page": 1,
  "max_allowed_objects_per_page": 250,
  "model_name": "MyModel",
  "model_verbose_name": "MyModel",
  "list_display": [],
  "list_filter": {},
  "total_objects_count": 2663,
  "create_url": "https://<webapp>/api/v1.1/my-model/"
}
```

- **Format de la réponse**:

La réponse JSON contient les clés suivantes :

- `objects_count`: nombre d'objet trouvés sur la page actuelle
- `next`: l'URL de la prochaine page (null si la page actuelle est la dernière/seule page)
- `previous`: l'URL de la page précédente (null si la page actuelle est la première/seule page)
- `results`: liste de toutes les instances
- `objects_count_per_page`: pagination de la réponse
- `num_total_pages`: nombre total de pages
- `num_current_page`: index de la page actuelle
- `max_allowed_objects_per_page`: maximum d'instances affiché en un seule réponse
- `model_name`: nom du modèle
- `model_verbose_name`: nom verbeux du modèle
- `list_display`: liste des champs affichés (défini dans la modélisation)
- `list_filter`: mapping des champs filtrables avec `{field_name: field_type}`. Les champs filtrables sont définient dans le modélisation
- `total_objects_count`: le total des intances du modèle actuel
- `create_url`: l'url pour la création d'une instance (avec une requête `POST`)

**IMPORTANT:** Pour le modèle **User**, la liste contiendra aucun utilisateur avec le niveau `blocked`. Pour accéder à ces utilisateurs, voir [utilisateurs bloqués](#Blockedusers)

#### Créer une nouvelle instance du modèle MyModel

Un `POST` sur l'url racine du modèle MyModel créera une nouvelle instance de ce modèle.

- **Méthode**: `POST`

- **Point d'entrée**: `https://<webapp>/api/v1.1/my-model/`

- **Exemple**:

**Requête**

```shell
curl \
    -X POST \
    -H "Authorization: Token <auth_token>" \
    -d "<JSON data for the new instance to create>" \
    "https://<webapp>/api/v1.1/my-model/"
```

**Réponse**: avec le code de statut HTTP `201 (CREATED)`, le corps de la réponse est un JSON contenant les champs de la nouvelle instance créée.

**IMPORTANT:** pour le modèle **User**, la requête `POST` précédente n'est pas autorisée. Pour créer un nouvel utilisateur, vous devez effectuer un [register](#Register)

#### Récupérer une instance spécifique du modèle MyModel grâce à son UID

Un `GET` sur l'url d'une instance donnée du modèle MyModel récupèrera les champs de l'instance donnée.

- **Méthod**: `GET`

- **Point d'entrée**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```


**Réponse**: avec le code de statut HTTP `200 (OK)`, le corps de la réponse est un JSON contenant les champs de l'instance requise.


#### Modifier une instance spécifique du model MyModel grâce à son UID

#### Modifier certains champs avec `PATCH`

Un `PATCH` sur l'url de l'instance donnée du model MyModel modifiera les champs de l'instance donnée.

- **Méthode**: `PATCH`

- **Point d'entrée**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -X PATCH \
  -H "Authorization: Token <auth_token>" \
  -d "<JSON data to update>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```

**Réponse**: avec le code de statut HTTP `200 (OK)`, le corps de la réponse est un JSON contenant tout les champs modifiés de l'instance donnée.


#### Modifier tous les champs avec`PUT`

Un `PUT` sur l'url de l'instance donnée du model MyModel modifiera tous les champs de l'instance donnée.

- **Méthode**: `PUT`

- **Point d'entrée**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -X PUT \
  -H "Authorization: Token <auth_token>" \
  -d "<JSON data to update>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```

**Réponse**: avec le code de statut HTTP `200 (OK)`, le corps de la réponse est un JSON contenant tout les champs modifiés de l'instance donnée.

#### Supprimer une instance spécifique du model MyModel grâce à son UID

Un `DELETE` sur l'url d'une instance donnée du modèle MyModel supprimera l'instance donnée.

- **Méthode**: `DELETE`

- **Point d'entrée**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -X DELETE \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```

**Réponse**: avec le code statut HTTP `204 (NO CONTENT)`, le corps de la réponse est vide.

Cette opération peut échouer. Si l'instance est liée à une instance protégée, elle ne peut pas être supprimée. Dans ce cas, le code statut HTTP est `412 (PRECONDITION FAILED)` avec le code d'erreur `"PROTECTED_RELATION"` dans la réponse.


### Points d'entrée spécifique de l'API

#### <a name="Register"></a>Inscription

- **Url**: `auth/register/` 
- **Méthode**: `POST`
- **Description**: permet à un utilisateur de s'inscrire sur l'API (voir la section [authentification](authentication.md)).

#### Se connecter

- **Url**: `auth/login/`
- **Méthode**: `POST`
- **Description**: permet à un utilisateur de se connecter à l'API (voir la section [authentification](authentication.md)).

#### Mon Compte

- **Url**: `account/me/` 
- **Méthode**: `GET`
- **Description**: permet à l'utilisateur de récupérer ses propres information sur l'API

**Requête**:

```shell
curl \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/account/me/"
```

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur.

#### Modifier les informations de l'utilisateur sur mon compte

- **Url**: `account/me/` 
- **Méthode**: `PATCH`
- **Description**: permet à l'utilisateur de modifier ses informations sur l'API

**Requête**:

```shell
curl \
  -X PATCH \
  -H "Authorization: Token <auth_token>" \
  -d "<JSON data to update>" \
  "https://<webapp>/api/v1.1/account/me/"
```

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur.


#### Modifier le mot de passe

- **Url**: `auth/change-password/`
- **Méthode**: `POST`
- **Description**: permet à un utilisateur de changer son mot de passe.

**Requête**: Deux cas de figure existent pour ce point d'entrée:

_Avec un `password_change_token`_: Un utilisateur dont le mot de passe a expiré, ou qui a oublié son mot de passe peut utiliser un `password_change_token` pour modifier son mot de passe. (Ce jeton est soit renvoyé dans la réponse après une connexion avec un mot de passe expiré, soit envoyé par e-mail après une tentative de réinitialisation du mot de passe)

```shell
curl \
  -X POST \
  -d '{"email": "<user_email>","password1":"<new_password>","password2":"<new_password>","password_change_token":"<token_returned_by_login>"}' \
  "https://<webapp>/api/v1.1/auth/change-password/"
```

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur.

_Sans un `password_change_token`_: Un utilisateur peut modifier son mot de passe quand il le souhaite s'il est connecté à l'API.

En outre, un utilisateur ayant le niveau manager ou supérieur (celui qui effectue la requête) peut modifier le mot de passe d'un autre utilisateur (cible). Ainsi, il (celui qui effectue la requête) doit remplir une de ces conditions :

-  est un superuser
-  est un admin et le mot de passe qu'il tente de modifier et à un manager ou à un simple-user
-  est un manager, la cible est un simple user et il à le même scope que l'utilisateur ciblé (si le model de données n'est pas scopé, aucun manager ne peut changer le mot de passe d'un autre utilisateur)

```shell
curl \
  -X POST \
  -H "Authorization: Token <auth_token>" \
  -d '{"email": "<target_user_email>","password1":"<new_password>","password2":"<new_password>"}' \
  "https://<webapp>/api/v1.1/auth/change-password/"
```

**Réponse**: `200 (OK)` avec le JSON suivant :

```json
{
    "email": "<user_email>",
    "message": "Password updated!"
}
```

#### Réinitialiser le mot de passe

- **Url**: `auth/reset-password/` 
- **Méthode**: `POST`
- **Description**: permet à un utilisateur de réinitialiser son mot de passe s'il l'a oublié (voir la section [authentification](authentication.md)).

#### Connexion à deux facteurs

- **Url**: `auth/two-factor/login/` (seulement pour l'API v1.1) 
- **Méthode**: `POST`
- **Description**: Si MultiFactorAuthentication est activé (USE_TWO_FACTOR_AUTH est True dans les paramètres), permet à un utilisateur de confirmer son authentification à l'API.

**Requête**:

```shell
curl \
  -X POST \
  -d '{"email": "<user_email>", "token": "<mfa_temp_token>", "verification_code": "<mfa_verification_code>"}' \
  "https://<webapp>/api/v1.1/secure-connect/retrieve-token/"
```

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur.

**Réponse**: `401 (UNAUTHORIZED)` si l'email n'existe pas avec le JSON suivant :

```json
{
    "message": "Wrong auth credentials",
    "_errors": ["WRONG_AUTH_CREDENTIALS"],
}
```

**Réponse**: `401 (UNAUTHORIZED)` si l'email n'existe pas avec le JSON suivant :

```json
{
    "message": "MFA temp token invalid",
    "_errors": ["MFA_TEMP_TOKEN_INVALID"],
}
```

**Réponse**: `401 (UNAUTHORIZED)` si le token a expiré avec le JSON suivant :

```json
{
    "message": "MFA temp token expired",
    "_errors": ["MFA_TEMP_TOKEN_EXPIRED"],
}
```

**Réponse**: `401 (UNAUTHORIZED)` si le code MFA est incorrect avec le JSON suivant :

```json
{
    "message": "Wrong verification code",
    "_errors": ["WRONG_VERIFICATION_CODE"],
}
```

#### LDAP Login

- **Url**: `auth/ldap/login/`
- **Méthode**: `POST`
- **Description**: permet à un utilisateur de s'authentifier en utilisant l'authentification LDAP.

**Requête**:

```shell
curl \
  -X POST \
  -d '{"username": "<user_username>", "password": "<user_password>"}' \
  "https://<webapp>/api/v1.1/secure-connect/retrieve-token/"
```

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur.

**Réponse**: `401 (UNAUTHORIZED)` si l'utilisateur n'existe pas avec le JSON suivant :

```json
{
    "message": "Wrong auth credentials",
    "_errors": ["WRONG_AUTH_CREDENTIALS"],
}
```

```json
{
    "message": "Token created and email sent"
}
```

#### Connexion sécurisée

##### Récupérer le Token

- **Url**: `secure-connect/retrieve-token/`
- **Méthode**: `POST`
- **Description**: permet à l'utilisateur de générer un token qui sera utilisé pour une connexion sécurisée. Un email contenant l'url de connexion sera envoyé à l'adresse mail.

**Requête**:

```shell
curl \
  -X POST \
  -d '{"email": "<user_email>"}' \
  "https://<webapp>/api/v1.1/secure-connect/retrieve-token/"
```

**Réponse**: `201 CREATED` avec le JSON suivant.

```json
{
    "message": "Token created and email sent"
}
```

**Réponse**: `400 (BAD REQUEST)` si l'utilisateur n'existe pas avec le JSON suivant :

```json
{
    "message": "Wrong email address",
    "_errors": ["WRONG_EMAIL_ADDRESS"],
}
```

##### Générer un Token

- **Url**: `secure-connect/generate-token/`
- **Méthod**: `POST`
- **Description**: permet à un **superuser** de récupérer le token d'un utilisateur.

**Requête**:

```shell
curl \
  -X POST \
  -H "Authorization: Token <auth_token>" \
  -d '{"email": "<target_user_email>"}' \
  "https://<webapp>/api/v1.1/secure-connect/generate-token/"
```

**Réponse**: `201 CREATED` avec le JSON suivant.

```json
{
    "secure-token":"1023ccc9-d8e8-4eef-86ae-889c37ec96b7"
}
```

**Réponse**: `400 (BAD REQUEST)` si l'utilisateur n'existe pas avec le JSON suivant :

```json
{
    "message": "Wrong email address",
    "_errors": ["WRONG_EMAIL_ADDRESS"],
}
```

##### Se connecter

- **Url**: `secure-connect/login/`
- **Méthode**: `POST`
- **Description**: permet à l'utilisateur de s'authentifier grâce à son token sécurisé.

**Requête**:

```shell
curl \
  -X POST \
  -d '{"token": "<secure_connect_token>"}' \
  "https://<webapp>/api/v1.1/secure-connect/login/"
```

**Réponse**: `401 (UNAUTHORIZED)` si le token est incorrect avec le JSON suivant:

```json
{
    "message": "Invalid token",
    "_errors": ["INVALID_TOKEN"],
}
```

**Réponse**: `403 (FORBIDDEN)` si le token a expiré avec le JSON suivant:

```json
{
    "message": "Token has expired",
    "_errors": ["TOKEN_HAS_EXPIRED"],
}
```

**Response**: `200 (OK)` with the JSON containing the user's information.

#### <a name="Blockedusers"></a>Accéder aux utilisateurs bloqués

- **Url**: `blocked-users`
- **Méthode**: `GET`
- **Description**: permet à un utilisateur (doit être du niveau `admin` ou `superuser`) de récupérer la liste de tous les utilisateurs `blocked`.

**Requête**:

```shell
curl \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/blocked-users/"
```

**Réponse**: `200 (OK)`:

```json
{
  "objects_count": 4,
  "next": null,
  "previous": null,
  "results": [
    ...
  ],
  "objects_count_per_page": 125,
  "num_total_pages": 1,
  "num_current_page": 1,
  "max_allowed_objects_per_page": 250,
  "model_name": "User",
  "model_verbose_name": "User",
  "list_display": [
    "email",
    "first_name",
    "last_name"
  ],
  "list_filter": {
    "email": "char"
  },
  "total_objects_count": 4,
  "create_url": "https://<webapp>/api/v1.1/auth/register/"
}
```

#### Accéder à un utilisateur bloqué spécifique

- **Url**: `blocked-users/<user_uid>`
- **Méthode**: `GET`
- **Description**: permet à un utilisateur (doit être du niveau `admin` ou `superuser`) de récupérer un utilisateur `blocked` spécifique.

**Requête**:

```shell
curl \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/blocked-users/<user_uid>/"
```

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur bloqué.

#### Débloquer un (ou des) utilisateur(s) bloqué(s)

- **Url**: `unblock-users/`
- **Méthode**: `POST`
- **Description**: permet à un utilisateur (doit être du niveau `admin` ou `superuser`) de débloquer un ou plusieurs utilisateur(s). Les utilisateurs débloqués auront le niveau `simpleuser`.

**Requête**:

```shell
curl \
  -X POST \
  -H "Authorization: Token <auth_token>" \
  -d '{"user_uids": ["<uid1>", "<uid2>"]}' \
  "https://<webapp>/api/v1.1/unblock-users/"
```

**Réponse**: `200 (OK)` avec le JSON suivant :

```json
{
    "<uid1>": "User successfully unblocked",
    "<uid2>": "User successfully unblocked"
}
```

<!-- ## API routes, methods and responses -->
## Routes, méthodes et réponses de l'API

<!-- For all the routes listed below, the API response has the following **failure status codes**: -->

Pour toutes les routes listées plus bas, la réponse de l'API à les **codes de statut d'erreur** suivant :

<!-- - `401 UNAUTHORIZED`: if the user does not have the rights to access the data.
- `403 FORBIDDEN`: if the user is not authenticated to the API.
- `404 NOT FOUND`: if the url is not found. -->

- `401 UNAUTHORIZED`: si l'utilisateur n'a pas les droits d'accès à la donnée.
- `403 FORBIDDEN`: si l'utilisateur n'est pas authentifié à l'API
- `404 NOT FOUND`: si l'url n'est pas trouvée.

<!-- ### Model related API endpoints -->

### API endpoints liés aux modèles

<!-- As explained in the introduction, Concrete Datastore consumes a datamodel definition in order to generate an API giving acess to the instances of the datastore. For each model, Concrete Datastore generates andpoints that allow a user to perform **CRUD** methods. This endpoint is a `kebab-case` (lower case with hyphens) of the model's name. For example if you have a model named `MyModel`, the API endpoint will be `my-model`. -->

Comme expliqué dans l'introduction, Concrete Datastore utilise une modélisation définie afin de générer une API donnant accès aux instances du datastore.
Pour chaque modèle, Concrete Datastore génère des endpoints qui permettent à l'utilisateur d'effectuer des méthodes **CRUD**. Ce endpoint sont des `kebab-case` (en minuscule avec des tirets) du nom du model. Par exemple si un model est nommé `MyModel`, l'endpoint de l'API sera `my-model`.

<!-- For each model, Concrete Datastore exposes two routes accepting different methods: -->

Pour chaque modèle, Concrete Datastore propose deux routes acceptant différentes méthodes :

<!-- #### List all instances of model MyModel -->

#### Liste toutes les instances du modèle MyModel

<!-- A `GET` on the root url of the model MyModel will retrieve all instances of this model. -->

Un `GET` sur l'url racine du modèle MyModel will récupèrera toutes les instances de ce modèle.

- **Méthode**: `GET`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/`

- **Exemple**:

**Requête** 

```shell
curl \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/my-model/"
```

<!-- **Response**: with status code HTTP `200 (OK)`, the response body is a JSON containing the details of all instances available. The response is paginated. -->

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

<!-- - **Response format**:

The JSON response contains the following keys:

- `objects_count`: number of objects found in this current page
- `next`: URL to the next page (null if the current page is the last/only page)
- `previous`: URL to the previous page (null if the current page is the first/only page)
- `results`: list of all the instances
- `objects_count_per_page`: pagination of the response
- `num_total_pages`: number of total pages
- `num_current_page`: index of the current page
- `max_allowed_objects_per_page`: max instances to be displayed in one response
- `model_name`: model name
- `model_verbose_name`: model verbose name
- `list_display`: list of displayed fields (defined in the datamodel)
- `list_filter`: mapping of the filter fields with `{field_name: field_type}`. Filter fields are defined in the datamodel
- `total_objects_count`: total instances of the current model
- `create_url`: url for instance creation (with a `POST` request)

**IMPORTANT:** For model **User**, the list won't contain any user with the level `blocked`. In order to access these users, see [blocked users](#Blockedusers) -->

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

<!-- #### Create a new instance of model MyModel

A `POST` to the root url of the model MyModel will create a new instance of this model.

- **Method**: `POST`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/`

- **Example**:

**Request** -->

#### Créer une nouvelle instance du modèle MyModel

Un `POST` sur l'url racine du modèle MyModel créera une nouvelle instance de ce modèle.

- **Méthode**: `POST`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/`

- **Exemple**:

**Requête**

```shell
curl \
    -X POST \
    -H "Authorization: Token <auth_token>" \
    -d "<JSON data for the new instance to create>" \
    "https://<webapp>/api/v1.1/my-model/"
```

<!-- **Response**: with status code HTTP  `201 (CREATED)`, the response body is a JSON containing the fields of the new instance created.

**IMPORTANT:** For the model **User**, the above `POST` request is not allowed. In order to create a new user, you should perform a [register](#Register) -->

**Réponse**: avec le code de statut HTTP `201 (CREATED)`, le corps de la réponse est un JSON contenant les champs de la nouvelle instance créée.

**IMPORTANT:** pour le modèle **User**, la requête `POST` précédente n'est pas autorisée. Pour créer un nouvel utilisateur, vous devez effectuer un [register](#Register)

<!--#### Retrieve a specific instance of model MyModel by its UID

 A `GET` on the url of a given instance of model MyModel will retrieve the fields of this given instance.

- **Method**: `GET`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Example**:

**Request** -->

#### Récupérer une instance spécifique du modèle MyModel grâce à son UID

Un `GET` sur l'url d'une instance donnée du modèle MyModel récupèrera les champs de l'instance donnée.

- **Méthod**: `GET`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```

<!-- **Response**: with status code HTTP `200 (OK)`, the response body is a JSON containing the fields of the instance requested. -->

**Réponse**: avec le code de statut HTTP `200 (OK)`, le corps de la réponse est un JSON contenant les champs de l'instance requise.

<!-- #### Update a specific instance of model MyModel by its UID

#### Update some of the fields with `PATCH`

A `PATCH` on the url of a given instance of model MyModel will update the fields of this given instance.

- **Method**: `PATCH`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Example**:

**Request** -->

#### Modifier une instance spécifique du model MyModel grâce à son UID

#### Modifier certains champs avec `PATCH`

Un `PATCH` sur l'url de l'instance donnée du model MyModel modifiera les champs de l'instance donnée.

- **Méthode**: `PATCH`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -X PATCH \
  -H "Authorization: Token <auth_token>" \
  -d "<JSON data to update>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```

<!-- **Response**: with status code HTTP `200 (OK)`, the response body is a JSON containing all the fields of the given instance, updated. -->

**Réponse**: avec le code de statut HTTP `200 (OK)`, le corps de la réponse est un JSON contenant tout les champs modifiés de l'instance donnée.

<!-- #### Update all the fields with `PUT`

A `PUT` on the url of a given instance of model MyModel will update the fields of this given instance.

- **Method**: `PUT`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Example**:

**Request** -->

#### Modifier tous les champs avec`PUT`

Un `PUT` sur l'url de l'instance donnée du model MyModel modifiera tous les champs de l'instance donnée.

- **Méthode**: `PUT`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -X PUT \
  -H "Authorization: Token <auth_token>" \
  -d "<JSON data to update>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```

<!-- **Response**: with status code HTTP `200 (OK)`, the response body is a JSON containing all the fields of the given instance, updated. -->

**Réponse**: avec le code de statut HTTP `200 (OK)`, le corps de la réponse est un JSON contenant tout les champs modifiés de l'instance donnée.

<!-- #### Delete a specific instance of model MyModel by its UID

A `DELETE` on the url of a given instance of model MyModel will retrieve the fields of this given instance.

- **Method**: `DELETE`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Example**:

**Request** -->

#### Supprimer une instance spécifique du model MyModel grâce à son UID

Un `DELETE` sur l'url d'une instance donnée du modèle MyModel supprimera l'instance donnée.

- **Méthode**: `DELETE`

- **Endpoint**: `https://<webapp>/api/v1.1/my-model/<uid>/`

- **Exemple**:

**Requête**

```shell
curl \
  -X DELETE \
  -H "Authorization: Token <auth_token>" \
  "https://<webapp>/api/v1.1/my-model/ef29364d-50f6-4e3e-a401-d30fecacf59b/"
```

<!-- **Réponse**: with status code HTTP `204 (NO CONTENT)`, the response body is empty.

This operation could fail. If the instance is related to a protected instance, it cannot be deleted. In this case, the HTTP status code is `412 (PRECONDITION FAILED)` with the error code `"PROTECTED_RELATION"` in the response. -->

**Réponse**: avec le code statut HTTP `204 (NO CONTENT)`, le corps de la réponse est vide.

Cette opération peut échouer. Si l'instance est liée à une instance protégée, elle ne peut pas être supprimée. Dans ce cas, le code statut HTTP est `412 (PRECONDITION FAILED)` avec le code d'erreur `"PROTECTED_RELATION"` dans la réponse.


<!-- ### Specific API endpoints

#### <a name="Register"></a>Register

- **Url**: `auth/register/` 
- **Method**: `POST`
- **Description**: allows a user to register to the API (see [authentication](authentication.md) section).

#### Login

- **Url**: `auth/login/`
- **Method**: `POST`
- **Description**: allows a user to log in the API (see [authentication](authentication.md) section).

#### Account Me

- **Url**: `account/me/` 
- **Method**: `GET`
- **Description**: allows a user to retrieve his own information on the API

**Request**: -->

### Endpoints spécifique de l'API

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

<!-- **Response**: `200 (OK)` with the JSON containing the user's information.

#### Change user information on Account Me

- **Url**: `account/me/` 
- **Method**: `PATCH`
- **Description**: allows a user to update his own information on the API

**Request**: -->

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

<!-- **Response**: `200 (OK)` with the JSON containing the user's information.


#### Change Password

- **Url**: `auth/change-password/`
- **Method**: `POST`
- **Description**: allows a user to change a password.

**Request**: Two use cases can be found for this endpoint:

_With a `password_change_token`_: A user which password expired or who forgot his password can use a `password_change_token` to change his password. (This token is either returned by login with an expired password, or sent in email after attempting a reset password) -->

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur.


#### Modifier le mot de passe

- **Url**: `auth/change-password/`
- **Méthode**: `POST`
- **Description**: permet à un utilisateur de changer son mot de passe.

**Requête**: Deux cas de figure existent pour cet endpoint:

_Avec un `password_change_token`_: Un utilisateur dont le mot de passe a expiré, ou qui a oublié son mot de passe peut utiliser un `password_change_token` pour modifier son mot de passe. (Ce jeton est soit renvoyé dans la réponse après une connexion avec un mot de passe expiré, soit envoyé par e-mail après une tentative de réinitialisation du mot de passe)

```shell
curl \
  -X POST \
  -d '{"email": "<user_email>","password1":"<new_password>","password2":"<new_password>","password_change_token":"<token_returned_by_login>"}' \
  "https://<webapp>/api/v1.1/auth/change-password/"
```

<!-- **Response**: `200 (OK)` with the JSON containing the user's information.

_Without a `password_change_token`_: A user can change his own password any time he wants if he is logger to the API.

Besides, a user of a level manager or above (requester) is able to change another user's password (target). Thus, he (the requester) should satisfy either one of these conditions:

-  is superuser
-  is admin and the password that he attempts to changes belongs to a manager or a simple user
-  is manager, the target is a simple user and he has the same scope as the targeted user (if the datamodel is not scoped, no manager can change any other user's password) -->

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

<!-- **Response**: `200 (OK)` with the following JSON: -->

**Réponse**: `200 (OK)` avec le JSON suivant :

```json
{
    "email": "<user_email>",
    "message": "Password updated!"
}
```

<!-- #### Reset Password

- **Url**: `auth/reset-password/` 
- **Method**: `POST`
- **Description**: allows a user to request a reset of his own password if he has forgotten it (see [authentication](authentication.md) section).

#### Two Factor Login

- **Url**: `auth/two-factor/login/` (only for API v1.1) 
- **Method**: `POST`
- **Description**: If MultiFactorAuthentication is enabled (USE_TWO_FACTOR_AUTH is True in settings), allows a user to confirm his authentication to the API.

**Request**: -->

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

<!-- **Response**: `200 (OK)` with the JSON containing the user's information.

**Response**: `401 (UNAUTHORIZED)` if the email does not exist with the following JSON: -->

**Réponse**: `200 (OK)` avec le JSON contenant les informations de l'utilisateur.

**Réponse**: `401 (UNAUTHORIZED)` si l'email n'existe pas avec le JSON suivant :

```json
{
    "message": "Wrong auth credentials",
    "_errors": ["WRONG_AUTH_CREDENTIALS"],
}
```

<!-- **Response**: `401 (UNAUTHORIZED)` if the the token in invalid with the following JSON: -->

**Réponse**: `401 (UNAUTHORIZED)` si l'email n'existe pas avec le JSON suivant :

```json
{
    "message": "MFA temp token invalid",
    "_errors": ["MFA_TEMP_TOKEN_INVALID"],
}
```

<!-- **Response**: `401 (UNAUTHORIZED)` if the the token has expired with the following JSON: -->

**Réponse**: `401 (UNAUTHORIZED)` si le token a expiré avec le JSON suivant :

```json
{
    "message": "MFA temp token expired",
    "_errors": ["MFA_TEMP_TOKEN_EXPIRED"],
}
```

<!-- **Response**: `401 (UNAUTHORIZED)` if the the MFA code is invalid with the following JSON: -->

**Réponse**: `401 (UNAUTHORIZED)` si le code MFA est incorrect avec le JSON suivant :

```json
{
    "message": "Wrong verification code",
    "_errors": ["WRONG_VERIFICATION_CODE"],
}
```

<!-- #### LDAP Login

- **Url**: `auth/ldap/login/`
- **Method**: `POST`
- **Description**: allows a user to authenticate using LDAP authentication.

**Request**: -->

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

<!-- **Response**: `200 (OK)` with the JSON containing the user's information.

**Response**: `401 (UNAUTHORIZED)` if the user does not exist with the following JSON: -->

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

<!-- #### Secure Connect

##### Retrieve Token

- **Url**: `secure-connect/retrieve-token/`
- **Method**: `POST`
- **Description**: allows a user to generate a token that will be used for secure login. An email will be sent to the email address containing the login url.
**Request**: -->

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

<!-- **Response**: `201 CREATED` with the following JSON: -->

**Réponse**: `201 CREATED` avec le JSON suivant.

```json
{
    "message": "Token created and email sent"
}
```

<!-- **Response**: `400 (BAD REQUEST)` if the user does not exist with the following JSON: -->

**Réponse**: `400 (BAD REQUEST)` si l'utilisateur n'existe pas avec le JSON suivant :

```json
{
    "message": "Wrong email address",
    "_errors": ["WRONG_EMAIL_ADDRESS"],
}
```

<!-- ##### Generate Token

- **Url**: `secure-connect/generate-token/`
- **Method**: `POST`
- **Description**: allows a **superuser** to retrieve a user's token.

**Request**: -->

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

<!-- **Response**: `201 CREATED` with the following JSON: -->

**Réponse**: `201 CREATED` avec le JSON suivant.

```json
{
    "secure-token":"1023ccc9-d8e8-4eef-86ae-889c37ec96b7"
}
```

<!-- **Response**: `400 (BAD REQUEST)` if the user does not exist with the following JSON: -->

**Réponse**: `400 (BAD REQUEST)` si l'utilisateur n'existe pas avec le JSON suivant :

```json
{
    "message": "Wrong email address",
    "_errors": ["WRONG_EMAIL_ADDRESS"],
}
```

<!-- ##### Login

- **Url**: `secure-connect/login/`
- **Method**: `POST`
- **Description**: allows a user to authenticate to the API using his secure token.

**Request**: -->

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

<!-- **Response**: `401 (UNAUTHORIZED)` if the token is invalid with the following JSON: -->

**Réponse**: `401 (UNAUTHORIZED)` si le token est incorrect avec le JSON suivant:

```json
{
    "message": "Invalid token",
    "_errors": ["INVALID_TOKEN"],
}
```

<!-- **Response**: `403 (FORBIDDEN)` if the token has expired with the following JSON: -->

**Réponse**: `403 (FORBIDDEN)` si le token a expiré avec le JSON suivant:

```json
{
    "message": "Token has expired",
    "_errors": ["TOKEN_HAS_EXPIRED"],
}
```

<!-- **Response**: `200 (OK)` with the JSON containing the user's information.

#### <a name="Blockedusers"></a>Access blocked users

- **Url**: `blocked-users`
- **Method**: `GET`
- **Description**: allows a user (must be of level `admin` or `superuser`) to retrieve the list of all `blocked` users.

**Request**: -->

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

<!-- #### Access a specific blocked user

- **Url**: `blocked-users/<user_uid>`
- **Method**: `GET`
- **Description**: allows a user (must be of level `admin` or `superuser`) to retrieve a specific `blocked` user. 

**Request**: -->

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

<!-- **Response**: `200 (OK)` with the JSON containing the blocked user's information.

#### Unblock blocked user(s)

- **Url**: `unblock-users/`
- **Method**: `POST`
- **Description**: allows a user (must be of level `admin` or `superuser`) to unblock one or more users. The unblocked users will have a level of `simpleuser`.

**Request**: -->

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

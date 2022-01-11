## Authentification

<!-- ## Authentication -->

### Utilisation avec les Headers HTTP

**Note** : HTTPS doit être utilisé pour garantir la confidentialité.

C'est un système d'authentification Token. Lorsque l'utilisateur se connecte, un token est généré et associé à l'utilisateur. En envoyant une requête HTTP avec le token, le serveur sait quel utilisateur a fait la requête.

**Headers:**

```json
{"Authorization": "Token [Token]"}
```

<!-- ### Usage with HTTP Headers

**Note** : HTTPS shall be used to ensure privacy

It's a Token authentication system. When the user logs in, a token is generated and associated to the user. By sending a HTTP request with the token, the server knows which user made the request.

**Headers:**

```json
{"Authorization": "Token [Token]"}
``` -->

### Utilisation avec un token dans l'URL

**Note** : HTTPS doit être utilisé pour garantir la confidentialité.

Utilisez les query params `c_auth_with_token=<token value>` dans n'importe quelle url.

Ex. `/api/v1.1/project/?c_auth_with_token=xxxxx`

<!-- ### Usage with token in URL

**Note** : HTTPS shall be used to ensure privacy

Use the query param `c_auth_with_token=<token value>` in any URL

Ex. `/api/v1.1/project/?c_auth_with_token=xxxxx` -->

<!-- ### Register -->
### S'inscrire

#### Requête

Utilisé pour s'inscrire à l'application.

**URL** : `/api/v1.1/auth/register/`

**Méthode** : `POST`

**Authentification requise** : NON

**Contraintes de données**

```json
{
    "email": "[valid email address]",
    "password1": "[password in plain text]",
    "password2": "[repeated password in plain text]",
    "url_format": "[valid url format]",
    "email_format": "[valid email format]"
}
```

-  `email` est obligatoire
-  `password1` est optionnel (si ALLOW_SEND_EMAIL_ON_REGISTER est activé dans les paramètres) et a une valeur par défaut générée dans la vue
-  `password2` est optionnel (si ALLOW_SEND_EMAIL_ON_REGISTER est activé dans les paramètres) et a une valeur par défaut générée dans la vue
-  `email_format` est optionnel est a une valeur par défaut qui est DEFAULT_REGISTER_EMAIL_FORMAT (dans les paramètres)
-  `url_format` est optionnel est a une valeur par défaut qui est `"/#/set-password/{token}/{email}/"`

**Cas d'utilisation** :

- Un utilisateur peut définir `password1` et `password2` afin de s'inscrire avec son propre mot de passe.
- Un  utilisateur peut s'inscrire sans définir de mot de passes (si ALLOW_SEND_EMAIL_ON_REGISTER est activé), et il recevra un lien dans un email. Ce lien contient l'adresse email utilisée pour s'inscrire et un token utilisé pour réinitialiser le mot de passe. Il peut alors réinitialiser son mot de passe en utilisant son token.

**Exemple de données**

```json
{
    "email": "johndoe@netsach.com",
    "password1": "abcd1234",
    "password2": "abcd1234"
}
```

<!-- #### Request

Used to register to the application.

**URL** : `/api/v1.1/auth/register/`

**Method** : `POST`

**Auth required** : NO

**Data constraints**

```json
{
    "email": "[valid email address]",
    "password1": "[password in plain text]",
    "password2": "[repeated password in plain text]",
    "url_format": "[valid url format]",
    "email_format": "[valid email format]"
}
```
-  `email` is mandatory
-  `password1` is optional (if ALLOW_SEND_EMAIL_ON_REGISTER is enabled in settings) and has a default value generated within the view
-  `password2` is optional (if ALLOW_SEND_EMAIL_ON_REGISTER is enabled in settings) and has a default value generated within the view
-  `email_format` is optional and has a default value of DEFAULT_REGISTER_EMAIL_FORMAT (in settings)
-  `url_format` is optional and has a default value of `"/#/set-password/{token}/{email}/"`

**Use cases** :

-  A user can set `password1` and `password2` in order to register whith his own password.
-  A user can register without setting passwords (if the setting ALLOW_SEND_EMAIL_ON_REGISTER is enabled), and he will recieve a link in an email. This link contains the email adress used to register with and a token used to reset the password. He can then reset his password using this token.

**Data example**

```json
{
    "email": "johndoe@netsach.com",
    "password1": "abcd1234",
    "password2": "abcd1234"
}
``` -->

#### Réponse de réussite

**Code** : `201 CREATED`

**Exemple de contenu**

```json
{
    "uid": "0f1cf8c2-5bda-4281-971b-7ed19ed7bc7a",
    "email": "johndoe@netsach.com",
    "url": "https://<webapp>/api/v1.1/account/me/",
    "token": "e0d15a8547e37ccced180ad590bac5e28cabc71a",
    "first_name": "",
    "last_name": "",
    "level": "simpleuser",
}
```

**IMPORTANT:**

Si AUTH_CONFIRM_EMAIL_ENABLE est true dans les settings, vous recevrez un email. Vous aurez besoin de confimer cette adresse email en cliquant sur le lien rattaché à cet email.

<!-- #### Success Response

**Code** : `201 CREATED`

**Content example**

```json
{
    "uid": "0f1cf8c2-5bda-4281-971b-7ed19ed7bc7a",
    "email": "johndoe@netsach.com",
    "url": "https://<webapp>/api/v1.1/account/me/",
    "token": "e0d15a8547e37ccced180ad590bac5e28cabc71a",
    "first_name": "",
    "last_name": "",
    "level": "simpleuser",
}
```

**IMPORTANT:** if AUTH_CONFIRM_EMAIL_ENABLE is True in settings, you will receive an email. You will need to confirm your email address by clicking on the link attached to this email. -->

#### Résponses d'erreur

**Condition** : Si l'e-mail n'est pas autorisé à s'inscrire.

**Code** : `400 BAD REQUEST`

**Contenu** :

```json
{
    "message": "This email is not allowed to register",
    "_errors": [
        "EMAIL_NOT_AUTHORIZED_TO_REGISTER"
    ]
}
```

**Condition** : Si `password1` est différent de `password2`.

**Code** : `400 BAD REQUEST`

**Contenu** :

```json
{
    "message": "Password confimation incorrect"
}
```

**Condition** : Si l'`url_format` n'a pas le bon format.

**Code** : `400 BAD REQUEST`

**Contenu** :

```json
{
    "errors": "url_format is not a valid format_string"
}
```

**Condition** : Si un utilisateur non authentifié sur la plate-forme (Utilisateur anonyme) tente d'envoyer un format e-mail.

**Code** : `400 BAD REQUEST`

**Contenu** :

```json
{
    "message": "Only registered users are allowed to set an email_format"
}
```

**Condition** : Si `password1` ne respecte pas les critères de complexité.

**Code** : `400 BAD REQUEST`

**Contenu** :

```json
{
    "message": "<message>",
    "_errors": ["<code>"]
}
```

`<message>` peut être :

- `"The password must contain at least X character(s)."`
- `"The password must contain at least X digit(s)."`
- `"The password must contain at least X lower character(s)."`
- `"The password must contain at least X upper character(s)."`
- `"The password must contain at least X special character(s) from these : (<list_of_special_characters>)"`

`<code>` peut être :

- `"NOT_ENOUGH_CHARS"`
- `"NOT_ENOUGH_DIGITS"`
- `"NOT_ENOUGH_LOWER"`
- `"NOT_ENOUGH_UPPER"`
- `"NOT_ENOUGH_SPECIAL"`

<!-- #### Error Responses

**Condition** : If the email is not allowed to register.

**Code** : `400 BAD REQUEST`

**Content** :

```json
{
    "message": "This email is not allowed to register",
    "_errors": [
        "EMAIL_NOT_AUTHORIZED_TO_REGISTER"
    ]
}
```
**Condition** : Si l'`email_format` n'a pas le bon format.

**Code** : `400 BAD REQUEST`

**Contenu** :

```json
{
    "errors": "email_format is not a valid format_string"
}
```
**Condition** : If `password1` is different from `password2`.

**Code** : `400 BAD REQUEST`

**Content** :

```json
{
    "message": "Password confimation incorrect"
}
```

**Condition** : If the `url_format` has not the right format.

**Code** : `400 BAD REQUEST`

**Content** :

```json
{
    "errors": "url_format is not a valid format_string"
}
```

**Condition** : If the `email_format` has not the right format.

**Code** : `400 BAD REQUEST`

**Content** :

```json
{
    "errors": "email_format is not a valid format_string"
}
```

**Condition** : If a user not authenticated to the platform (AnonymousUser) attempts to post an email format.

**Code** : `400 BAD REQUEST`

**Content** :

```json
{
    "message": "Only registered users are allowed to set an email_format"
}
```

**Condition** : If `password` does not comply with complexity criteria.

**Code** : `400 BAD REQUEST`

**Content** :

```json
{
    "message": "<message>",
    "_errors": ["<code>"]
}
```

`<message>` can be:

- `"The password must contain at least X character(s)."`
- `"The password must contain at least X digit(s)."`
- `"The password must contain at least X lower character(s)."`
- `"The password must contain at least X upper character(s)."`
- `"The password must contain at least X special character(s) from these : (<list_of_special_characters>)"`

`<code>` can be:

- `"NOT_ENOUGH_CHARS"`
- `"NOT_ENOUGH_DIGITS"`
- `"NOT_ENOUGH_LOWER"`
- `"NOT_ENOUGH_UPPER"`
- `"NOT_ENOUGH_SPECIAL"` -->

### Login

#### Requête

Utilisé pour collecter un token pour un utilisateur enregistré.

**URL** : `/api/v1.1/auth/login/`

**Méthode** : `POST`

**Authentification requise** : NON

**Contraintes de données**

```json
{
    "email": "[valid email address]",
    "password": "[password in plain text]"
}
```

**Exemple de données**

```json
{
    "email": "johndoe@netsach.com",
    "password": "abcd1234"
}
```

<!-- ### Login

#### Request

Used to collect a Token for a registered User.

**URL** : `/api/v1.1/auth/login/`

**Method** : `POST`

**Auth required** : NO

**Data constraints**

```json
{
    "email": "[valid email address]",
    "password": "[password in plain text]"
}
```

**Data example**

```json
{
    "email": "johndoe@netsach.com",
    "password": "abcd1234"
}
``` -->

#### Réponse de succès

**Code** : `200 OK`

**Exemple de contenu**

```json
{
    "uid": "0f1cf8c2-5bda-4281-971b-7ed19ed7bc7a",
    "email": "johndoe@netsach.com",
    "url": "https://<webapp>/api/v1.1/account/me/",
    "token": "e0d15a8547e37ccced180ad590bac5e28cabc71a",
    "first_name": "",
    "last_name": "",
    "level": "simpleuser",
    "is_verified": true,
    "groups": [],
    "external_auth": false,
    "<scope_model_field>": []
}
```

**Format de réponse**:

-  `uid`: identifiant unique de l'utilisateur
-  `email`: email de l'utilisateur
-  `url`: URL pour obtenir les informations du compte de l'utilisateur
-  `token`: token d'authentification de l'utilisateur
-  `first_name`: prénom de l'utilisateur
-  `last_name`: nom de famille de l'utilisateur
-  `level`: niveau de l'utilisateur
-  `is_verified`: ce champ est `false` si l'authentification multifacteur est activée **ET** l'utilisateur n'a pas validé le MFA. Sinon c'est "vrai"
-  `groups`: liste des groupes auxquels appartient l'utilisateur
-  `external_auth`: si l'utilisateur a été authentifié par une source d'authentification externe (LDAP, External Backend, ...)
-  `<scope_model_field>`: liste des scopes de l'utilisateur


<!-- #### Success Response

**Code** : `200 OK`

**Content example**

```json
{
    "uid": "0f1cf8c2-5bda-4281-971b-7ed19ed7bc7a",
    "email": "johndoe@netsach.com",
    "url": "https://<webapp>/api/v1.1/account/me/",
    "token": "e0d15a8547e37ccced180ad590bac5e28cabc71a",
    "first_name": "",
    "last_name": "",
    "level": "simpleuser",
    "is_verified": true,
    "groups": [],
    "external_auth": false,
    "<scope_model_field>": []
}
```

**Response format**:

-  `uid`: unique id of the user
-  `email`: user's email
-  `url`: url to get user's account information
-  `token`: user's authentication token
-  `first_name`: user's first name
-  `last_name`: user's last name
-  `level`: user's level
-  `is_verified`: this field is `false` if the Multi Factor Authentication is activated **AND** the user did not validate the MFA. Otherwise it is `true`
-  `groups`: list of groups to which the user belongs
-  `external_auth`: whether the user was authenticated by an external authentication source (LDAP, External Backend, ...)
-  `<scope_model_field>`: list of user's scopes -->


<!-- #### Error Responses

**Condition** : If 'username' and 'password' combination is wrong.

**Code** : `401 UNAUTHORIZED`

**Content** :

```json
{
    "message": "Wrong auth credentials",
    "_errors": [
        "WRONG_AUTH_CREDENTIALS"
    ]
}
```

**Condition** : If the email address has not been validated.

**Code** : `401 UNAUTHORIZED`

**Content** :

```json
{
    "message": "Email has not been validated",
    "_errors": [
        "EMAIL_NOT_VALIDATED"
    ]
}
``` -->
#### Réponses d'erreur

**Condition** : Si la combinaison 'nom d'utilisateur' et 'mot de passe' est incorrecte.

**Code** : `401 UNAUTHORIZED`

**Contenu** :

```json
{
    "message": "Wrong auth credentials",
    "_errors": [
        "WRONG_AUTH_CREDENTIALS"
    ]
}
```

**Condition** : Si l'adresse e-mail n'a pas été validée.

**Code** : `401 UNAUTHORIZED`

**Contenu** :

```json
{
    "message": "Email has not been validated",
    "_errors": [
        "EMAIL_NOT_VALIDATED"
    ]
}
```


<!-- ### Account information

#### Request

Used to get information about the user.

**URL** : `/api/v1.1/account/me/`

**Method** : `GET`

**Auth required** : YES

#### Success Response

**Code** : `200 OK`

**Content example** -->

### Information du compte

#### Requête

Sert à recupérer les informations d'un utilisateur.

**URL** : `/api/v1.1/account/me/`

**Méthode** : `GET`

**Auth requise** : OUI

#### Réponse de réussite

**Code** : `200 OK`

**Exemple de contenu**

```json
{
    "uid": "49297d80-e924-479a-8fcc-27e15f7a12e7",
    "email": "johndoe@netsach.com",
    "first_name": "Doe",
    "last_name": "John",
    "modification_date": "2018-11-26T15:54:34Z",
    "creation_date": "2018-11-26T15:54:34Z",
    "public": true,
    "url": "https://<webapp>/api/v1.1/user/49297d80-e924-479a-8fcc-27e15f7a12e7/",
    "verbose_name": "johndoe@netsach.com",
    "<scope_model_field>": [],
    "level": "simpleuser",
    "unsubscribe_notification_url": "http://<webapp>/c/unsubscribe-notifications/707ca2da-0ea0-4a90-9fba-d5337fa72555",
    "unsubscribe_all": false,
    "unsubscribe_to": [],
    "external_auth": false
}
```

<!-- **Response format**:

-  `uid`: unique id of the user
-  `email`: user's email
-  `first_name`: user's first name
-  `last_name`: user's last name
-  `modification_date`: datetime of last modification
-  `creation_date`: datetime of creation
-  `public`: whether the user is public or not
-  `url`: url to get user's account information
-  `verbose_name`: user's email
-  `<scope_model_field>`: list of user's scopes
-  `level`: user's level
-  `unsubscribe_notification_url`: url to unsubscribe user from notifications
-  `unsubscribe_all`: user is unsubscribed to all notifications
-  `unsubscribe_to`: list of scope notifications that the user is unsubscribed to
-  `external_auth`: whether the user was authenticated by an external authentication source (LDAP, External Backend, ...)

#### Error Responses

**Condition** : If the authentication token is not given.

**Code** : `403 FORBIDDEN` -->

**Format de la réponse**:

-  `uid`: id unique de l'utilisateur
-  `email`: email de l'utilisateur
-  `first_name`: prénom de l'utilisateur
-  `last_name`: nom de famille de l'utilisateur
-  `modification_date`: date et heure de la dernière modification
-  `creation_date`: date et heure de la création
-  `public`: Si l'utilisateur est publique ou non
-  `url`: l'url pour récupérer les informations de l'utilisateur
-  `verbose_name`: email de l'utilisateur
-  `<scope_model_field>`: liste des scopes de l'utilisateur
-  `level`: niveau de l'utilisateur
-  `unsubscribe_notification_url`: url to unsubscribe user form notifications
-  `unsubscribe_all`: l'utilisateur est désabonné de toutes les notifications
-  `unsubscribe_to`: liste des notifications scopés auquelles l'utiliateur est désabonné. 
-  `external_auth`: Si l'utilisateur s'est authentifié par une source externe d'authentification (LDAP, External Backend, ...)

#### Réponses d'erreur

**Condition** : Si le token d'authentification n'est pas fourni.

**Code** : `403 FORBIDDEN`

<!-- ### Update Account information

#### Request

Used to get information about the user.

**URL** : `/api/v1.1/account/me/`

**Method** : `PATCH`

**Auth required** : YES

#### Success Response

**Code** : `200 OK`

**Content example** -->

### Modifier les informations du compte

#### Requête

Sert à modifier les informations d'un utilisateur.

**URL** : `/api/v1.1/account/me/`

**Méthode** : `PATCH`

**Auth requise** : OUI

#### Réponse de réussite

**Code** : `200 OK`

**Exemple de contenu**

```json
{
    "public": true,
    "uid": "49297d80-e924-479a-8fcc-27e15f7a12e7",
    "modification_date": "2018-11-26T15:54:34Z",
    "creation_date": "2018-11-26T15:54:34Z",
    "url": "https://<webapp>/api/v1.1/user/49297d80-e924-479a-8fcc-27e15f7a12e7/",
    "verbose_name": "johndoe@netsach.com",
    "last_name": "John",
    "first_name": "Doe",
    "level": "simpleuser",
    "email": "johndoe@netsach.com"
}
```

<!-- #### Error Responses

**Condition** : If the serializer is invalid.

**Code** : `400 BAD REQUEST`

**Condition** : If the authentication token is not given.

**Code** : `403 FORBIDDEN` -->

#### Réponses d'erreur

**Condition** : Si le serialiseur est incorrect.

**Code** : `400 BAD REQUEST`

**Condition** : Si le token d'autentification n'est pas fourni.

**Code** : `403 FORBIDDEN`


<!-- ### Reset Password Request

#### Request

Used to reset your own password.

**URL** : `/api/v1.1/auth/reset-password/`

**Method** : `POST`

**Auth required** : NO

**Data constraints** -->

### Requête de déinitialisation d'un mot de passe

#### Requête

Sert à réinitialiser son propre mot de passe.

**URL** : `/api/v1.1/auth/reset-password/`

**Méthode** : `POST`

**Auth requise** : NON

**Contraintes**

```json
{
    "email": "[valid email]",
    "url_format": "[valid url_format]"
}
```
<!-- The url_format will be used to send the reset password email with a link to allow the user to reset his own password. It should be a string containing `"{email}"` and `"{token}"`. Example: `"/redirection-url/{email}/{token}"`.

Default value: `"/#/reset-password/{token}/{email}/"`.

**Data example** -->

L' `url_format` sera utiliser pour envoyer l'email de réinitialisation de mot de passe avec un lien pour permettre à l'utilisateur de réinitialiser son mot de passe. Ce lien devrait être une chaîne de caractère contenant `"{email}"` et `"{token}"`. Exemple: `"/redirection-url/{email}/{token}"`.

Valeur par défaut: `"/#/reset-password/{token}/{email}/"`.

**Exemple**

```json
{
    "email": "johndoe@netsach.com",
    "url_format": "/redirection-url/{token}/{email}/"
}
```

<!-- #### Success Response

**Code** : `200 OK`

**Content example** -->

#### Réponse de réussite

**Code** : `200 OK`

**Exemple de contenu**

```json
{
    "email": "johndoe@netsach.com"
}
```

<!-- #### Error Responses

**Condition** : If the `url_format` has not the right format.

**Code** : `400 BAD REQUEST`

**Content example** -->

#### Réponses d'erreur

**Condition** : Si l' `url_format` n'a pas le bon format.

**Code** : `400 BAD REQUEST`

**Exemple de contenu**

```json
{
    "errors": "url_format is not a valid format_string"
}
```

<!-- ### Change Password

#### Request

Used to change the password of a user.

**URL** : `/api/v1.1/auth/change-password/`

**Method** : `POST`

**Auth required** : YES.

**Data constraints** -->

### Modifier le mot de passe

#### Requête

Sert à modifier le mot de passe d'un utilisateur.

**URL** : `/api/v1.1/auth/change-password/`

**Méthode** : `POST`

**Auth requise** : OUI.

**Contraintes**

```json
{
    "email": "[valid user email]",
    "password1": "[valid password]",
    "password2": "[valid password]",
    "password_change_token": "[valid token]"
}
```

<!-- -  `email` is mandatory
-  `password1` is mandatory
-  `password2` is mandatory
-  `password_change_token` is optional. This token is created if the user forgot his password and requests a reset password, or if the current password has expired. If given in data, that means that the user is attempting to change his own password (the request is not authenticated). Otherwise, if the `password_change_token` is not given, the request must be authenticated and the authenticated user must have the permissions to alter another user's password.

**Data example** -->

-  `email` est requis
-  `password1` est requis
-  `password2` est requis
-  `password_change_token` est optionnel. Ce tiken est créé si l'utilisateur a oublié son mot de passe et en demande un nouveau, ou si le mot de passe actuel a expiré. Si ce token est présent, celà signifie que l'utilisateur tente de modifier son propre mot de passe (la requête n'est pas authentifiée). sinon, si le `password_change_token` n'ets pas préssent, la requête doit être authentifiée and et l'utilisateur connecté doit avoir les permission pour changer le mot de passe d'un autre utilisateur.

**Exemple**

```json
{
    "email": "johndoe@netsach.com",
    "password1": "test",
    "password2": "test",
    "password_change_token": "49297d80-e924-479a-8fcc-27e15f7a12e7"
}
```

<!-- #### Success Response

**Code** : `200 OK`

**Content example**

If a user attempts to change his own password -->

#### Réponse de réussite

**Code** : `200 OK`

**Exemple**

Si l'utilisateur tente de changer son propre mot de passe

```json
{
    "public": true,
    "uid": "49297d80-e924-479a-8fcc-27e15f7a12e7",
    "modification_date": "2018-11-26T15:54:34Z",
    "creation_date": "2018-11-26T15:54:34Z",
    "url": "https://<webapp>/api/v1.1/user/49297d80-e924-479a-8fcc-27e15f7a12e7/",
    "verbose_name": "johndoe@netsach.com",
    "last_name": "John",
    "first_name": "Doe",
    "level": "simpleuser",
    "email": "johndoe@netsach.com"
}
```

<!-- If a user (with the sufficient permissions) attempts to change another user's password: -->

Si un utilisateur (avec les permission suffisantes) tente de changer le motd e passe d'un autre utilisateur

```json
{
    "email": "johndoe@netsach.com",
    "message": "Passwod updated !"
}
```

<!-- #### Error Responses

**Condition** : If the given `password_change_token` is invalid.

**Code** : `400 BAD REQUEST`

**Condition** : If the given `password_change_token` does not match with the user's password.

**Code** : `400 BAD REQUEST`

**Condition** : If the given `password_change_token` has expired.

**Code** : `400 BAD REQUEST`

**Condition** : If the new password is identical to the old one.

**Code** : `400 BAD REQUEST`

**Condition** : If the user making the request does not have the permission to change another user's password.

**Code** : `403 FORBIDDEN` -->

#### Réponses d'erreur

**Condition** : Si le `password_change_token` donné est incorrect.

**Code** : `400 BAD REQUEST`

**Condition** : Si le `password_change_token` donné ne correspond pas au `password_change_token` de l'utilisateur.

**Code** : `400 BAD REQUEST`

**Condition** : Si le `password_change_token` donné a expiré.

**Code** : `400 BAD REQUEST`

**Condition** : Si le nouveau mot de passe est identique à l'ancien.

**Code** : `400 BAD REQUEST`

**Condition** : Si l'utilisateur faisant la reqête n'a pas la permission de changer le mot de passe d'un autre utilisateur.

**Code** : `403 FORBIDDEN`

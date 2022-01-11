## Authentification


### Utilisation avec les entêtes HTTP

**Note** : HTTPS doit être utilisé pour garantir la confidentialité.

C'est un système d'authentification Token. Lorsque l'utilisateur se connecte, un token est généré et associé à l'utilisateur. En envoyant une requête HTTP avec le token, le serveur sait quel utilisateur a fait la requête.

**Entêtes:**

```json
{"Authorization": "Token [Token]"}
```


### Utilisation avec un token dans l'URL

**Note** : HTTPS doit être utilisé pour garantir la confidentialité.

Utilisez les paramètres de requêtes `c_auth_with_token=<token value>` dans n'importe quelle url.

Ex. `/api/v1.1/project/?c_auth_with_token=xxxxx`


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


### Se connecter

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
-  `unsubscribe_to`: liste des notifications scopés auxquelles l'utiliateur est désabonné.
-  `external_auth`: Si l'utilisateur s'est authentifié par une source externe d'authentification (LDAP, External Backend, ...)

#### Réponses d'erreur

**Condition** : Si le token d'authentification n'est pas fourni.

**Code** : `403 FORBIDDEN`


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


#### Réponses d'erreur

**Condition** : Si le sérialiseur est incorrect.

**Code** : `400 BAD REQUEST`

**Condition** : Si le token d'autentification n'est pas fourni.

**Code** : `403 FORBIDDEN`


### Requête de réinitialisation d'un mot de passe

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


L' `url_format` sera utilisé pour envoyer l'email de réinitialisation de mot de passe avec un lien pour permettre à l'utilisateur de réinitialiser son mot de passe. Ce lien devra être une chaîne de caractère contenant `"{email}"` et `"{token}"`. Exemple: `"/redirection-url/{email}/{token}"`.

Valeur par défaut: `"/#/reset-password/{token}/{email}/"`.

**Exemple**

```json
{
    "email": "johndoe@netsach.com",
    "url_format": "/redirection-url/{token}/{email}/"
}
```


#### Réponse de réussite

**Code** : `200 OK`

**Exemple de contenu**

```json
{
    "email": "johndoe@netsach.com"
}
```


#### Réponses d'erreur

**Condition** : Si l' `url_format` n'a pas le bon format.

**Code** : `400 BAD REQUEST`

**Exemple de contenu**

```json
{
    "errors": "url_format is not a valid format_string"
}
```


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


-  `email` est requis
-  `password1` est requis
-  `password2` est requis
-  `password_change_token` est optionnel. Ce token est créé si l'utilisateur a oublié son mot de passe et en demande un nouveau, ou si le mot de passe actuel a expiré. Si ce token est présent, celà signifie que l'utilisateur tente de modifier son propre mot de passe (la requête n'est pas authentifiée). Sinon, si le `password_change_token` n'est pas présent, la requête doit être authentifiée et l'utilisateur connecté doit avoir les permissions pour changer le mot de passe d'un autre utilisateur.

**Exemple**

```json
{
    "email": "johndoe@netsach.com",
    "password1": "test",
    "password2": "test",
    "password_change_token": "49297d80-e924-479a-8fcc-27e15f7a12e7"
}
```


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


Si un utilisateur (avec les permission suffisantes) tente de changer le mot de passe d'un autre utilisateur

```json
{
    "email": "johndoe@netsach.com",
    "message": "Passwod updated !"
}
```


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

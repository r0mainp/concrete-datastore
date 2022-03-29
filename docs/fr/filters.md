## Filtres et Tri

### Filtres
API requests support different types of filters on the fields of the `filter_fields` declared in the datamodel, and other specific query parameters:

Les requêtes à l'API supportent plusieurs type de filtres sur les champs déclarés dans les `filter_fields` de la modélisation, ainsi que d'autre paramètres de requête spécifiques 

#### Filtrer sur les champs d'un modèle

- **Filtrer la valeur exacte:** (Appliqué sur tout les champs dans les `filter_fields` sauf les JsonFields et PointFields) En utilisant une égalité stricte (`field_name=field_value`) et retourner toutes les instances ayant `field_value` comme valeur pour le champ `field_name`. On peut aussi utiliser un filtre d'exclusion en ajoutant une négation (`!`) après le paramètre de la requête, afin d'exclure les instances qui correspondent au filtre (`field_name!=field_value`)
- **Filtre supportant l'opération OU (or):** (Appliqué sur tout les champs dans les `filter_fields`) En ajoutant le suffixe `__in`:
exemple:`?name__in=project1,project2,project3` retourne toutes les instances dont le champ `name` à pour valeur "project1" **OU** "project2" **OU** "project3". On peut aussi utiliser un filtre d'exclusion en ajoutant une négation (`!`) après le paramètre de la requête, afin d'exclure les instances qui correspondent au filtre. Exemple: `?name__in!=project1,project2,project3` retourne toutes les instances dont la valeur du champ `name`n'est **NI(nor)** "project1" **NI** "project2" **NI** "project3".
- **Filtre supportant la clé `Contains`:** (Appliqué sur les CharFields et TextFields) En ajoutant le suffixe `__contains`:
exemple: `?name__contains=pro` retourne toutes les instances dont le champ `name` contient la sous-chaîne `"pro"`. On peut aussi utiliser un filtre d'exclusion en ajoutant une négation (`!`) après le paramètre de la requête, afin d'exclure les instances qui correspondent au filtre. Exemple: `?name__contains!=pro` retourne toutes les instances dont le champ `name` ne contient pas la sous-chaîne `"pro"`. Veillez notez que le filtre `__contains` est sensible à la casse. Pour un filtre insensible à la casse, vous pouvez utiliser le tag `__icontains`.
- **Filtre supportant les valeur vide (Empty):** (Appliqué sur les CharFields et TextFields) En ajoutant `__isempty=true` ou `is__empty=false`:
exemple: `?name__isempty=true` retourne toutes les instances qui n'ont pas de `name` et `?name__isempty=false` retourne toutes les instances qui on un `name` non vide.
- **Filtre supprotant une relation Null** (Appliqué sur les ForeignKeys et ManyToManyField) En ajoutant `__isnull=true`:
exemple: `?project__isnull=true` retourne toutes les instance qui n'ont pas de `project`
- **Filtre supportant les opérations de Comparaisons:** (Appliqué sur les DateTimeFields, DateFields, DecimalFields, IntegerFields et FloatFields) En ajoutant le tag `__gte`, `__gt`, `__lte` or `__lt`.
example:
    - `?price__gte=10` (price >= 10)
    - `?price__gt=10` (price > 10)
    - `?price__lte=10` (price <= 10)
    - `?price__lt=10` (price < 10)

- **Filtre sur les champs JSON:** (Appliqué sur les champs JSON) Ce backend permet de filtrer sur une clé de champ JSON avec `?field__key=value`. Si la valeur est sensé être une chaîne, elle devrait être entre guillemets doubles: `"value"`, sinon, le serveur réponds avec une `400 BAD REQUEST` (Veuillez noter que l'encodage des guillemets doubles est `%22`, donc `"value"` devient `%22value%22`). On peut aussi utiliser un filtre d'exclusion en ajoutant une négation (`!`) après le paramètre de la requête, afin d'exclure les instances qui correspondent au filtre.

Exemple: Soit un modèle `MyModel` avec un champ JSON `data`, Et 3 instance de ce modèle:

```python
# instance_1
data = {
    "name": "test1",
    "item": {
        "name": "toto",
        "available": False,
        "price": 3.99e3,
        "size": 0
    },
    "items_list": [1, 2, 3],
    "reference": None,
}

# instance_2
data = {
    "name": "tEsT2",
    "item": {
        "name": "tata",
        "available": False,
        "price": 0.4,
        "size": 2
    },
    "custom_field": "tata",
    "items_list": [4, 2, 5],
    "reference": "12345",
}

# instance_3
data = {
    "name": "name",
    "item": {
        "name": "TOTO",
        "available": True,
        "price": 25,
        "size": 3
    },
    "custom_field": "toto",
    "items_list": ['1', '2', '3'],
    "reference": None,
}
```

Filtres possibles:

```python
# String filters
"?data__name__icontains=%22test%22"  # 200 OK (instance_1, instance_2)
"?data__name__icontains!=%22test%22"  # 200 OK (instance_3)
"?data__item__name=%22toto%22"  # 200 OK (instance_1)
"?data__item__name__icontains=%22to%22"  # 200 OK (instance_1, instance_3)
"?data__custom_field=%22toto%22"  # 200 OK (instance_3)
"?data__items_list__2=%223%22"  # 200 OK (instance_3)
"?data__name=test"  # 400 BAD REQUEST

# Boolean filters
"?data__item__available=False"  # 200 OK (instance_1, instance_2)
"?data__item__available=faLSe"  # 200 OK (instance_1, instance_2)

# Null filters
"?data__reference=null"  # 200 OK (instance_1, instance_3)
"?data__reference=nUlL"  # 200 OK (instance_1, instance_3)
"?data__reference=none"  # 200 OK (instance_1, instance_3)

# Integer filters
"?data__item__size__gt=0"  # 200 OK (instance_2, instance_3)
"?data__items_list__1=2"  # 200 OK (instance_1, instance_2)

# Float filters
"?data__item__price__lt=300.0"  # 200 OK (instance_2, instance_3)

# Invalid filters
"?data__wrong_field=%22test%22"  # 200 OK (No results)
"?data__items_list__10=1"  # 200 OK (No results)
"?data__a__b__3__c=%22test%22"  # 200 OK (No results)
```


- **Filtre supportant les distances::** (Appliqué seulement sur les PointFields) En ajoutant les tags `__distance_gte`, `__distance_gt`, `__distance_lte`, `__distance_lt`, `__distance_range` or `__distance_range!`.
    - `?coords__distance_gte=30,1.34,48.543` la distance du point ayant pour longitude 1.34 et pour latitude 48.543 est supérieure ou égale à 30 mètres
    - `?coords__distance_gt=30,1.34,48.543` la distance du point ayant pour longitude  1.34 et pour latitude 48.543 est strictement supérieure à 30 mètres
    - `?coords__distance_lte=30,1.34,48.543` la distance du point ayant pour longitude  1.34 et pour latitude 48.543 est inéférieure ou égale à 30 mètres
    - `?coords__distance_lt=30,1.34,48.543` la distance du point ayant pour longitude  1.34 et pour latitude 48.543 est strictement inéferieure à 30 mètres
    - `?coords__distance_range=30,40,1.34,48.543` la distance du point ayant pour longitude  longitude 1.34 et pour latitude 48.543 est entre 30 mètres et 40 mètres (intervalle inclusif)
    - `?coords__distance_range!=30,1.34,48.543` la distance du point ayant pour longitude  1.34 et pour latitude 48.543 est soit srtrictement supérieure à 40 mètres ou strictement inférieure à  30 mètres.
- **Filtre supportant un intervalle:** (Appliqué sur les DateTimeFields, DateFields, DecimalFields, IntegerFields et FloatFields) En ajoutant le suffixe `__range`: exemple: `?creation_date__range=2018-01-01,2018-12-31` retourne toutes les instances avec une `creation_date` comprise entre le 1er Jan 2018 et le 31 Dec 2018 (intervalle inclusif). On peut aussi utiliser un filtre d'exclusion en ajoutant une négation (`!`) après le paramètre de la requête, afin d'exclure les instances qui correspondent au filtre. Exemple: `?creation_date__range!=2018-01-01,2018-12-31` retourne toutes les instances qui ont été crées **SOIT** avant le 1ser Jan 2018 **OU** après le 31 Dec 2018

**Exemples**:

```
https://<webapp>/api/v1.1/mymodel/?name=test
https://<webapp>/api/v1.1/mymodel/?name!=test
https://<webapp>/api/v1.1/mymodel/?name__isempty=true
https://<webapp>/api/v1.1/mymodel/?name__isempty=false
https://<webapp>/api/v1.1/mymodel/?name__in=project1,project2,project3
https://<webapp>/api/v1.1/mymodel/?name__in!=project1,project2,project3
https://<webapp>/api/v1.1/mymodel/?price__gte=price
https://<webapp>/api/v1.1/mymodel/?creation_date__range=date1,date2
https://<webapp>/api/v1.1/mymodel/?creation_date__range!=date1,date2
https://<webapp>/api/v1.1/mymodel/?coords__lte=Distance,Longitude,latitude
https://<webapp>/api/v1.1/mymodel/?coords__range=Distance1,Distance2,Longitude,latitude
```


#### Filtre utilisant des paramètres de requête spécifiques

- `c_resp_page_size`: L'API propose également une pagination grâce à l'utilisation du paramètre de requête `c_resp_page_size` qui prend un entier représentant le nombre de résultats par page qui doivent être renvoyés.
- `c_resp_nested`: IS'il existe une relation entre les objets, par défaut, l'API affiche la relation complètement, elle est imbriquée.

Exemple:

```json
{
      "global_status": "PENDING",
      "name": "Test Name",
      "other_object": { //Imbriqué ici
        "status":"other_object_status",
        "name": "other_object_name"
      }
}
```

Vous pouvez empêcher ce comportement en filtrant avec le paramètre de requête `?c_resp_nested=False`

This will only give you the object uid:


```json
{
      "global_status": "PENDING",
      "name": "Test Name",
      "other_object": "b1d30fb2-4d11-4bef-a777-721df8dfe984"
}
```

* **Filtre dans l'intervalle d'horodatage:** Vous pouvez filtrer les résultats dans l'intervalle d'horodatage en ajoutant les paramètres de requête `timestamp_start` et `timestamp_end`
exemples:
    - `?timestamp_start=100000&timestamp_end=20000`: Filtre les objets entre les horodatages [10000, 20000] en fonction de la **modification_date**.
    - `?timestamp_start=10000`: Filtre les objets entre les horodatages [10000, now] en fonction de la **modification_date**.
    - `?timestamp_end=20000`: Filtre les objets entre les horodatages [0, 20000] en fonction de la **modification_date**.
    
    
    
Si `timestamp_start` est spécifié et `> 0`, la réponse del'API contiendra les éléments supplémentaires suivants :
    - `"timestamp_start"`: l'horodatage de début donné
    - `"timestamp_end"`: l'horodatage de fin si il est donné dans les paramètres de requête, sinon l'horodatage actuel
    - `"deleted_uids"`: une liste des uid des objets qui ne sont plus dans la réponse. Veuillez vous référer à [l'exemple sur la façon d'utiliser correctement timestamp_start et timestamp_end](#TimestampStartEnd)

<a name="TimestampStartEnd"></a>**Exemple d'utilisation de timestamp_start et timestamp_end**:

Soit un modèle `Article` avec un FloatField `price`.
Soit deux instance du modèle:

-  article1 (price=53.99)
-  article2 (price=52.99)


Pour récupérer tous les articles dont le prix est supérieur ou égal à 50.00 :
```
https://<webapp>/api/v1.1/article/?price__gte=50.0&timestamp_start=0
```

La réponse de l'API est:

```json
{
  "objects_count": 2,
  "next": null,
  "previous": null,
  "results": [ // Les deux acrticles sont dans la réponse
    // article1,
    // article2
  ],
  "objects_count_per_page": 250,
  "num_total_pages": 1,
  "num_current_page": 1,
  "max_allowed_objects_per_page": 250,
  "model_name": "Article",
  "model_verbose_name": "Article",
  "list_display": [],
  "list_filter": {},
  "total_objects_count": 2,
  "create_url": "https://<webapp>/api/v1.1/article/",
  "timestamp_start": 0.0,
  "timestamp_end": 1603716926.382462,
  "deleted_uids": []
}
```


Si vous utilisez le `timestamp_end` renvoyé pour effectuer la requête GET :
```
https://<webapp>/api/v1.1/article/?price__gte=50.0&timestamp_start=1603716926.382462
```

alors le résultat sera une liste vide, car entre `1603716926.382462` et l'horodatage actuel, rien n'a changé dans la base de données
```json
{
  "objects_count": 0,
  "next": null,
  "previous": null,
  "results": [], // pas d'article dans la réponse
  "objects_count_per_page": 250,
  "num_total_pages": 1,
  "num_current_page": 1,
  "max_allowed_objects_per_page": 250,
  "model_name": "Article",
  "model_verbose_name": "Article",
  "list_display": [],
  "list_filter": {},
  "total_objects_count": 0,
  "create_url": "https://<webapp>/api/v1.1/article/",
  "timestamp_start": 1603716926.382462,
  "timestamp_end": 1603716951.567238,
  "deleted_uids": []
}
```


Maintenant, si vous ajoutez une nouvelle instance au modèle `Article` : article3 (price=55.00) et effectuez la requête get avec les mêmes filtres, en utilisant le dernier timestamp_end comme nouveau timestamp_start :
```
https://<webapp>/api/v1.1/article/?price__gte=50.0&timestamp_start=1603716951.567238
```

la réponse contiendra seulement `article3`:

```json
{
  "objects_count": 1,
  "next": null,
  "previous": null,
  "results": [ // only the 3rd article
    // article3
  ],
  "objects_count_per_page": 250,
  "num_total_pages": 1,
  "num_current_page": 1,
  "max_allowed_objects_per_page": 250,
  "model_name": "Article",
  "model_verbose_name": "Article",
  "list_display": [],
  "list_filter": {},
  "total_objects_count": 1,
  "create_url": "https://<webapp>/api/v1.1/article/",
  "timestamp_start": 1603716951.567238,
  "timestamp_end": 1603717103.926404,
  "deleted_uids": []
}
```

Si vous mettez à jour `article1` avec `price = 49.99` et effectuez la requête avec le filtre `?price__gte=50.0` et le timstamp_start à `1603717103.926404`, l'uid de `article1` apparaîtra dans les uid supprimés, car il ne satisfait plus les filtres :

```
https://<webapp>/api/v1.1/article/?price__gte=50.0&timestamp_start=1603717103.926404
```

```json
{
  "objects_count": 0,
  "next": null,
  "previous": null,
  "results": [], // no articles in the reponse
  "objects_count_per_page": 250,
  "num_total_pages": 1,
  "num_current_page": 1,
  "max_allowed_objects_per_page": 250,
  "model_name": "Article",
  "model_verbose_name": "Article",
  "list_display": [],
  "list_filter": {},
  "total_objects_count": 0,
  "create_url": "https://<webapp>/api/v1.1/article/",
  "timestamp_start": 1603717103.926404,
  "timestamp_end": 1603717216.513412,
  "deleted_uids": [
    "1d80d208-1748-4784-a9e6-f0f70a2ecc64" // uid d'article1
  ]
}
```

### Filtres aditionnels pour le modèle Users

**Filtre des utilisateurs par rang:** Vous pouvez filtrer les utilisateurs par niveau avec deux filtres: `level` et `atleast`
    - `/user/?level=<user_level>`: Utilisateurs avec un rang égal à `user_level`. Le `user_level` doit être un des rangs parmis [**superuser**, **admin**, **manager**, **simpleuser**]
    - `/user/?atleast=<user_level>`: Utilisateurs avec un rang au moins égal à `user_level`. Le `user_level` doit être un des rangs parmis [**superuser**, **admin**, **manager**, **simpleuser**]

Exemple: `/user/?atleast=manager` retourne les utilisateurs avec le rang manager, admin et superuser


### Tri

Les requêtes `GET` à l'API supportent le tri sur les champs présent dans les `ordering_fields` du modèle.  
Afin d'obtenir un résultat trié à partir de l'API, vous pouvez utiliser le paramètre de requête `?ordering=<field_name>` (pour les résultats croissants) ou `?ordering=-<field_name>` (pour les résultats décroissants).

**Exemples**:
```
https://<webapp>/api/v1.1/mymodel/?ordering=name
https://<webapp>/api/v1.1/mymodel/?ordering=-name
```

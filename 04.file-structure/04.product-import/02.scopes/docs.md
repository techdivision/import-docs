---
title: Scopes
media_order: scope.png
published: true
visible: true
---

The import functionality provides a possiblity to import values on different scopes. Therefore, all scope specific values has to be added on a separate row for each scope, which represents a store view, like

| sku     | store_view_code | attribute_set_code | product_type | name          | description                          | url_key       | ...     |
|:--------|:----------------|:-------------------|:-------------|:--------------|:-------------------------------------|:--------------|:--------|
| MB-2401 |                 | Default            | simple       | Duffle Bag    | This the default description.        | duffle-bag    |         |
| MB-2401 | de_DE           |                    |              | Reisetasche   | Das ist die deutsche Beschreibung.   | reisetasche   |         |
| MB-2401 | fr_FR           |                    |              | Sac Marin     | C'est la description par défaut.     | sac-marin     |         |
| MB-2401 | es_ES           |                    |              | Bolsa de Lona | Esta es la descripción por defecto.  | bolsa-de-lona |         |

The column `store_view_code` **MUST** contain the appropriate Magento store view code.

### Translateable Values

For sure, not all attributes can have different values per scope. In general all product attributes that has been created with scope *Store View* can be translated. To find out which attributes have the scope *Store View* open the Backend and go to the overview with the product attributes by clicking on the navigation path *Stores > Attributes > Products*. Select the filter *Store View* and press enter, the result should look like

![](scope.png)

Most of the default attributes have a dedicated column in the CSV file and can simply be translated by adding the appropriate value to row with the approriate store view scope. But **ALL** attributes that has been user defined, either in the backend or by a developer doesn't. To find out which columns are user defined, you can open your preferred SQL editor and enter a SQL like

![](user_defined_attributes.png)

which should give you a list of all user defined attributes. To make the import as flexible as possible, all user defined attributes can be imported by adding the appropriate key value pair to the `additional_attributes` column.

The example below shows a part of a CSV file with scope specific values for the columns *name*, *description* and *test*

| sku     | store_view_code | name          | description                          | additional_attributes                    | ...     |
|:--------|:----------------|:--------------|:-------------------------------------|:-----------------------------------------|:--------|
| MB-2401 |                 | Duffle Bag    | This the default description.        | "test=english,activity=Gym&#124;Hiking"  |         |
| MB-2401 | de_DE           | Reisetasche   | Das ist die deutsche Beschreibung.   | "test=german"                            |         |
| MB-2401 | fr_FR           | Sac Marin     | C'est la description par défaut.     | "test=french"                            |         |
| MB-2401 | es_ES           | Bolsa de Lona | Esta es la descripción por defecto.  | "test=spain"                             |         |

For more details about Additional Attributes read the dedicated [section](/file-structure/product-import/additional-attributes).
---
title: Internationalisation
published: false
visible: true
---

The import functionality provides a possiblity to import values on store view level. The store view specific values has to be added on a separate row like

| sku     | store_view_code | attribute_set_code | product_type | name          | description                          | url_key       | ...     |
|:--------|:----------------|:-------------------|:-------------|:--------------|:-------------------------------------|:--------------|:--------|
| MB-2401 |                 | Default            | simple       | Duffle Bag    | This the default description.        | duffle-bag    |         |
| MB-2401 | de_DE           |                    |              | Reisetasche   | Das ist die deutsche Beschreibung.   | reisetasche   |         |
| MB-2401 | fr_FR           |                    |              | Sac Marin     | C'est la description par défaut.     | sac-marin     |         |
| MB-2401 | es_ES           |                    |              | Bolsa de Lona | Esta es la descripción por defecto.  | bolsa-de-lona |         |
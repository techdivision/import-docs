---
title: Scopes
visible: true
---

As well as for products, the import functionality for categories provides the possiblity to import values on different scopes. Therefore, all scope specific values has to be added on a separate row for each scope, which represents a store view, like

| store_view_code | attribute_set_code | path                              | name             | url_key          | ...     |
|:----------------|:-------------------|:----------------------------------|:-----------------|:-----------------|:--------|
|                 | Default            | Default Category/What's New       | What's New       | whats-new        |         |
| de_DE           | Default            | Default Category/What's New       | Was ist neu      | was-ist-neu      |         |
| fr_FR           | Default            | Default Category/What's New       | Quoi de neuf     | quoi-de-neuf     |         |
| es_ES           | Default            | Default Category/What's New       | Qué hay de nuevo | que-hay-de-nuevo |         |
|                 | Default            | Default Category/Women            | Women            | women            |         |
| de_DE           | Default            | Default Category/Women            | Frauen           | frauen           |         |
| fr_FR           | Default            | Default Category/Women            | Dames            | dames            |         |
| es_ES           | Default            | Default Category/Women            | Mujeres          | mujeres          |         |
|                 | Default            | Default Category/Women/Tops       | Tops             | tops-women       |         |
| de_DE           | Default            | Default Category/Women/Tops       | Oberteile        | oberteile-frauen |         |
| fr_FR           | Default            | Default Category/Women/Tops       | Toupie           | toupie-dames     |         |
| es_ES           | Default            | Default Category/Women/Tops       | Cima             | cima-mujeres     |         |
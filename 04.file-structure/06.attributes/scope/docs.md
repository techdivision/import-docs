---
title: Scope
visible: true
---

As well as for products, the import functionality for attributes provides the possiblity to import values on different scopes. Therefore, all scope specific values has to be added on a separate row for each scope, which represents a store view, like

| store_view_code | attribute_code | frontend_label   | attribute_option_values                            | ...     |
|:----------------|:---------------|:-----------------|:---------------------------------------------------|:--------|
|                 | activity       | Activity         | Gym,Hiking,Trail,Urban                             |         |
| de_DE           | activity       | Aktivität        | Fitness,Wandern,Trail,Städtisch                    |         |
| fr_FR           | activity       | Activité         | Gymnastique,Randonnée Pédestre,Sentier,D'urbanisme |         |
| es_ES           | activity       | Actuación        | Gimnasio,Senderismo,Rezagarse,Urbano               |         |

The example below shows a part of a CSV file with scope specific values for the columns *frontend_label* and *attribute_option_values*

The column `store_view_code` **MUST** contain the appropriate Magento store view code.

### Which Values that can be Translated

Basicalla for every attribute the label can be translated. 

Depending on the attribute's frontend input type, the attribute option/swatch values can also be translated. In the case the attribute frontend type is one of

* Multiple Select
* Dropdown
* Visual Swatch
* Text Swatch






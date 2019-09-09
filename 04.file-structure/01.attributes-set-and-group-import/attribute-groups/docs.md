---
title: 'Attribute Groups'
visible: true
---

When creating new Attribute Sets, it'll be necessary to create it's Attribute Groups too.

Beside the possiblity to copy the Attribute Groups with the column `based_on` e. g. from the *Default* Attribute Set, it'll also will be possible to create new ones on-the-fly. Whereas the default Attribute Group can be defined in the same row as the Attribute Set has been defined, for any additional Attribute Group a new row has to be added.

A CSV file that creates the Attribute Set *Bag* as well as 10 Attribute Groups can look like

| attribute_set_name | based_on | entity_type_code | sort_order | attribute_group_name       | attribute_group_code       | attribute_group_tab_group_code | attribute_group_sort_order | default_id |
|:-------------------|:---------|:-----------------|:-----------|:---------------------------|:---------------------------|:-------------------------------|:---------------------------|:-----------|
| Bag                |          | catalog_product  | 2          | Product Details            | product-details            | basic                          | 10                         | 1          |
|                    |          |                  |            | Content                    | content                    | basic                          | 15                         | 0          |
|                    |          |                  |            | Bundle Items               | bundle-items               |                                | 16                         | 0          | 
|                    |          |                  |            | Images                     | image-management           | basic                          | 20                         | 0          |
|                    |          |                  |            | Search Engine Optimization | search-engine-optimization | basic                          | 30                         | 0          |
|                    |          |                  |            | Advanced Pricing           | advanced-pricing           | advanced                       | 40                         | 0          |
|                    |          |                  |            | Design                     | design                     | advanced                       | 50                         | 0          |
|                    |          |                  |            | Schedule Design Update     | schedule-design-update     | advanced                       | 55                         | 0          |
|                    |          |                  |            | Autosettings               | autosettings               | advanced                       | 60                         | 0          |
|                    |          |                  |            | Gift Options               | gift-options               |                                | 61                         | 0          |
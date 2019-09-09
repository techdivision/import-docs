---
title: 'Attribute Groups'
visible: true
---

When creating new attribute sets, it'll be necessary to create attribute groups it's attribute groups too.

Beside the possiblity to copy the attribute groups with the column `based_on` e. g. from the *Default* attribute set, it'll also will be possible to create new ones on-the-fly. Whereas the default attribute group can be defined in the same row as the attribute set has been defined, for any additional attribute group a new row has to be added, 

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
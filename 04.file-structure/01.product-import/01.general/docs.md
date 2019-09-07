---
title: General
published: false
visible: true
---

Especially for the product import, Magento extends the default CSV format for some special cases called complex data, which is some kind of data serialization within the default CSV format, with a dedicated [page](https://docs.magento.com/m2/ce/user_guide/system/data-complex.html) and their website. Beside the topics that has been described on that page by Magento itself, there are additional columns that also uses a complex data format.

### Do's and Don'ts

General things that are **NOT** allowed

* Attribute code **MUST NOT** contain any special chars (only a-z, A-Z, 0-9, and _ are allowed)
* Attribute option values for the admin store **MUST NOT** contain a comma (,)
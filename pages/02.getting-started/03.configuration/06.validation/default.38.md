---
title: Validation
published: false
---

Up with version 3.8.0, validation for all entity types will be activated by default. Especially in case of huge CSV files, lets say > 100 MB, validation can slow down the import process massively. Therefore, the validation is highly customizable and can, if necessary, completely be switched off.

### Custom Validations

For each entity type a snippet, that declares the available operations, is available in the corresponding repositories configuration folder `etc/configuration/operations.json`. This files contains the configuration for the validation operation. By overriding it with a custom snippet, e. g. `custom-configuration-dir>/operations.json`, you've full control what will be validated and how the validation works.

In general, the validation operation is based on a validator subject, an observer, some listeners and a bunch of callbacks. Finally, the validations are implemented as callbacks which allows you register one or more validation callbacks for each column. M2IF comes with special   
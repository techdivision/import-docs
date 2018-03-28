---
title: 'Extend M2IF'
visible: true
---

### Dependency Injection

M2IF uses the [Symonfy DI Container](http://symfony.com/doc/current/components/dependency_injection.html) to compose the application. Therefore each library provides the necessary DI configuration files in the directory `symfony/Resources/config/services.xml`. On application startup, the library files will be parsed and depending on the used Magento Edition, the apropriate classes will be injected.

> For [configuration](#configuration) the symfony service IDs will be used instead of the real class names.
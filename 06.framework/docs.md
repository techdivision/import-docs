---
title: Framework
visible: true
---

The main purpose of M2IF, if you will not use it indirectly with the M2IF - Simple Console Tool, is to support you, to build your own Magento 2 import service. Therefore, we choosed a Component-Based Architecture. If you want to implement your own custom component to import another Magento 2 entity, e. g. customers, you can and should follow these guidelines.

### Dependency Injection

The M2IF componentens does not care about DI, but they can be tied together by using DI. For the M2IF - Simple Console Tool, that we've implemented as a reference application, we used the [Symonfy DI Container](http://symfony.com/doc/current/components/dependency_injection.html) to compose the application. To make the start quite simply, we recommend to also use Symonfy and/or Symfony DI when start writing your own component or application. Therefore each core library provides the necessary Symfony DI configuration files in the directory `symfony/Resources/config/services.xml`. To get an impression on how Symfony DI can be used to composer your application have a look at the reference application. M2IF - Simple Console Tool, which parses the library files on start-up, depending on the used Magento Edition, the apropriate load, initialize and inject the necessary classes.

> For [configuration](#configuration) the symfony service IDs will be used instead of the real class names.

### Component Structure

<pre>
composer.json
├── src/
│   ├── Actions/
│   │	└── Processors/
│   ├── Assemblers
│   ├── Callbacks
│   ├── Observers
│   ├── Repositories
│   │	└── CacheWarmers/
│   ├── Services
│   ├── Subjects
│   └── Utils
├── tests/
│   ├── Actions/
│   │	└── Processors/
│   ├── Assemblers
│   ├── Callbacks
│   ├── Observers
│   ├── Repositories
│   │   └── CacheWarmers/
│   ├── Services
│   ├── Subjects
│   └── Utils
└── symfony/
	│   ├── DependencyInjection/
	│   ├── Resources/
	│   │	└── config/
    │   │   	└── service.xml
	│   └── Utils/
    │	    └── DependencyInjectionKeys.php
    └── ImportBundle.php
</pre>
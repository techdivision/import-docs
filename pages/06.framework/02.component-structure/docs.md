---
title: 'Component Structure'
visible: true
---

In general, there is no restriction for the folder structure of your component. As M2IF components usually will be installed by Composer, at least your Component Structure should be PSR-0 or PSR-4 compatible. As usually every component will be delivered with it's configuration (the necessary files for DI for example), the structure should also support the separation of source code and configuration files.

Assuming you'll start to implement your first component the recommended Componend Structure looks like this

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
└── symfony/
	│   ├── DependencyInjection/
	│   ├── Resources/
	│   │	└── config/
    │   │   	└── service.xml
	│   └── Utils/
    │	    └── DependencyInjectionKeys.php
    └── ImportBundle.php
</pre>

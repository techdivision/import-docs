---
title: 'Dependency Injection'
taxonomy:
    category:
        - docs
visible: true
---

The Pacemaker Community componentens does not care about DI, but they can be tied together by using DI. For the Pacemaker - Community Console Tool, that we've implemented as a reference application, we used the [Symonfy DI Container](http://symfony.com/doc/current/components/dependency_injection.html) to compose the application. To make the start as simple as possible, we recommend to also use Symonfy and/or Symfony DI when start writing your own component or application. Therefore each core library provides the necessary Symfony DI configuration files in the directory `symfony/Resources/config/services.xml`. To get an impression on how Symfony DI can be used to composer your application have a look at the reference application. Pacemaker - Community Console Tool, which parses the library files on start-up, depending on the used Magento Edition, the apropriate load, initialize and inject the necessary classes.

! For [configuration](/configuration) the symfony service IDs will be used instead of the real class names.
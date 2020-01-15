---
title: Plug-Ins
taxonomy:
    category:
        - docs
visible: true
---

Depending on the required functionality, it'll be necessary to implement a plug-in. A plug-in is the highest level when starting a component and in many cases it'll not be necessary to implement a plug-in, as subject or observer level can cover the required needs.

#### When do i need a plug-in?

You should think about implementing a plug-in in either one of these cases

* You want to deal with all artefacts of an import and you want to do something with the files before they'll be processed or after they have been processed, e. g. like the artefact plug-in that compresses all the import artefacts into a ZIP and moves it to a configurable folder 
* You want to load some data from the database or the filesystem, that'll be needed later on in your subjects or observers, during the files will be processed, e. g. like the global data plug-in that loads the available attributes and add's them to the registry
* You need to pre-initialize something, before the main import process starts, e. g. like the cache warmer plug-in that warms the registered repository
* You need to do something after the import has been finished, e. g. like the missing options plug-in that sends a list with missing option values a configurable receiver

!!!! Generally you probably need a plug-in, when you want to do something before or after the main import step, or you have to implement some business logic that need access to **ALL IMPORT ARTEFACTS** at the same time.

#### How to implement a plug-in?

The good example is the `TechDivision\Import\Plugins\CacheWarmerPlugin` that is part of the M2IF core. Usually you don't have to write the plug-in from scratch, instead extend the `TechDivision\Import\Plugins\AbstractPlugin` class, that implements the `TechDivision\Import\Plugins\PluginInterface` which **MUST** be implemented by every plug-in. The interface defines the `setPluginConfiguration()` method, which expects the plug-in configuration with the optional parameters

```json
{
  "id": "import.plugin.cache.warmer",
  "params": {
  	"cache-warmers": [
      "import.repository.cache.warmer.eav.attribute.option.value"
    ] 
  }
}
```

and the `process()` method that'll have to implement the plug-ins main functionality.

The `TechDivision\Import\Plugins\CacheWarmerPlugin` loads the repository with the ID `import.repository.cache.warmer.eav.attribute.option.value` (that implements the `TechDivision\Import\Repositories\CacheWarmer\CacheWarmerInterface`) from the DI container and invokes it's `warm()` method which pre-loads the repository data and add's it to the cache. For sure, this can be done before the main import process to minimize database queries and system load. 

```php
namespace TechDivision\Import\Plugins;

use TechDivision\Import\Utils\ConfigurationKeys;
use TechDivision\Import\Utils\DependencyInjectionKeys;

class CacheWarmerPlugin extends AbstractPlugin
{

    /**
     * Array with the default cache warmers.
     *
     * @var array
     */
    protected $cacheWarmers = array(
        DependencyInjectionKeys::IMPORT_CACHE_WARMER_EAV_ATTRIBUTE_OPTION_VALUE_REPOSITORY
    );

    /**
     * Process the plugin functionality.
     *
     * @return void
     * @throws \Exception Is thrown, if the plugin can not be processed
     */
    public function process()
    {

        // query whether or not additional cache warmers has been configured
        if ($this->getPluginConfiguration()->hasParam(ConfigurationKeys::CACHE_WARMERS)) {
            // try ot load the cache warmers and merge them with the default ones
            $this->cacheWarmers = array_merge(
                $this->cacheWarmers,
                $this->getPluginConfiguration()->getParam(ConfigurationKeys::CACHE_WARMERS)
            );
        }

        // create the instances and warm the repository caches
        foreach ($this->cacheWarmers as $id) {
            $this->getApplication()->getContainer()->get($id)->warm();
        }
    }
}
```
!!!! Whenever another class has the repository injected, the data has been pre-loaded and no additional database queries will be necessary.

#### Cache Warmer

Has been removed up with version 3.8.0

#### OOTB Plug-Ins

The standard plugins are part of the M2IF core and can be used OOTB. When it'll be necessary to implement your own components, you'll go fine in most cases, if you don't implement your own plug-in, but instead use the `SubjectPlugin` and provide your functionality in form of subjects, observers and services.

##### Global Data

Load's the global data, necessary for the import process from the database and add's it to the registry, so that every plugin can access it. This 

The configuration has to be like

```json
{
  "id": "import.plugin.global.data"
}
```

##### Subject

This is the plug-in that does the main work by invoking the registered subjects as well as their registered observers and callbacks. When using M2IF as a framework, this plug-in will be a really good entrypoint to add your custom functionality. In most cases, it'll be quite enough to write a subject and observers that provides the necessary functionality and will be invoke by this plug-in. 

The plug-in configuration is

```json
{
  "id": "import.plugin.subject",
  "subjects": [ ... ]
}
```

whereas you can register as many subjects and observers as necessary.

##### Missing Option Values

This plugin provides the extended functionality to track whether an attribute option value, referenced in a CSV import file, is available or not, depending on `debug mode` enabled or not. If the `debug mode` is **NOT** enabled, an exception will be thrown immediately, else each missing attribute option value will be written to the CSV file `missing-option-values.csv` that'll stored in the temporary import directory and optionally sent to the specified mail recipients.

The configuration of the plugin can look like

```json
{
  "id": "import.plugin.missing.option.values",
  "swift-mailer" : {
    "id" : "import.logger.factory.transport.swift.smtp",
    "params" : {
      "to" : "tw@techdivision.com",
      "from" : "m2if@techdivision.com",
      "subject": "Something Went Wrong",
      "content-type" : "text/plain"
    },
    "transport" : {
      "params" : {
        "smtp-host" : "mail.techdivision.com",
        "smtp-port" : 25
      }
    }
  }
}
```

whereas the `swift-mailer` configuration node is optionally. Only if the configuration for the Swift Mailer is available, the CSV file will be send to the specified recipients.
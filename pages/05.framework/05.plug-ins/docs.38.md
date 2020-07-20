---
title: Plug-Ins
taxonomy:
    category:
        - docs
visible: true
---

Depending on the required functionality, it'll be necessary to implement a plug-in. A plug-in is the highest level when starting a component and in many cases it'll not be necessary to implement a plug-in, as subject or observer level can cover the required needs.

### When do i need a plug-in?

You should think about implementing a plug-in in either one of these cases

* You want to deal with all artefacts of an import and you want to do something with the files before they'll be processed or after they have been processed, e. g. like the artefact plug-in that compresses all the import artefacts into a ZIP and moves it to a configurable folder 
* You want to load some data from the database or the filesystem, that'll be needed later on in your subjects or observers, during the files will be processed, e. g. like the global data plug-in that loads the available attributes and add's them to the registry
* You need to pre-initialize something, before the main import process starts, e. g. like the cache warmer plug-in that warms the registered repository
* You need to do something after the import has been finished, e. g. like the missing options plug-in that sends a list with missing option values a configurable receiver

!!!! In general, you need a plug-in, when you want to do something before or after the main import step, or you have to implement some business logic that need access to **ALL IMPORT ARTEFACTS** at the same time.

### How to implement a plug-in?

The good example is the `TechDivision\Import\Plugins\GlobalDataPlugin` that is part of the Pacemaker Community core. Usually you don't have to write the plug-in from scratch, instead extend the `TechDivision\Import\Plugins\AbstractPlugin` class, that implements the `TechDivision\Import\Plugins\PluginInterface` which **MUST** be implemented by every plug-in. The interface defines the `setPluginConfiguration()` method, which expects the plug-in configuration with the optional parameters

```json
{
  "id": "import.plugin.global.data"
}
```

and the `process()` method that'll have to implement the plug-ins main functionality.

The `TechDivision\Import\Plugins\GlobalDataPlugin` loads the global data, e. g. entity types from the import processor and injects it into the registry. This makes it available by all following plug-ins, subjects and observers and helps to avoid unnecessary database access. 

```php
namespace TechDivision\Import\Plugins;

use TechDivision\Import\Utils\RegistryKeys;

class GlobalDataPlugin extends AbstractPlugin
{
    /**
     * Process the plugin functionality.
     *
     * @return void
     * @throws \Exception Is thrown, if the plugin can not be processed
     */
    public function process()
    {

        // load the global data from the import processor
        $globalData = $this->getImportProcessor()->getGlobalData();

        // add the status with the global data
        $this->getRegistryProcessor()->mergeAttributesRecursive(
            RegistryKeys::STATUS,
            array(RegistryKeys::GLOBAL_DATA => $globalData)
        );
    }
}
```

### OOTB Plug-Ins

The standard plugins are part of the Pacemaker Community core and can be used OOTB. When it'll be necessary to implement your own components, you'll go fine in most cases, if you don't implement your own plug-in, but instead use the `SubjectPlugin` and provide your functionality in form of subjects, observers and services.

#### Cache Warmer

Has been removed up with version 3.8.0

#### Global Data

Load's the global data, necessary for the import process from the database and add's it to the registry, so that every plugin can access it. You can find a more detailed exaplanation of the plugin in the section above. The configuration simply has to look like

```json
{
  "id": "import.plugin.global.data"
}
```

#### Subject

This is the plug-in that does the main work by invoking the registered subjects as well as their registered observers and callbacks. When using Pacemaker Community as a framework, this plug-in will be a really good entrypoint to add your custom functionality. In most cases, it'll be quite enough to write a subject and observers that provides the necessary functionality and will be invoke by this plug-in. 

The plug-in configuration is

```json
{
  "id": "import.plugin.subject",
  "subjects": [ ... ]
}
```

whereas you can register as many subjects and observers as necessary.

#### Missing Option Values

This plugin provides the extended functionality to track whether an attribute option value, referenced in a CSV import file, is available or not, depending on `debug mode` enabled or not. If the `debug mode` is **NOT** enabled, an exception will be thrown immediately, else each missing attribute option value will be written to the CSV file `missing-option-values.csv` that'll stored in the temporary import directory and optionally sent to the specified mail recipients.

The configuration of the plugin can look like

```json
{
  "id": "import.plugin.missing.option.values",
  "swift-mailer" : {
    "id" : "import.logger.factory.transport.swift.smtp",
    "params" : {
      "to" : "tw@techdivision.com",
      "from" : "pacemaker@techdivision.com",
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
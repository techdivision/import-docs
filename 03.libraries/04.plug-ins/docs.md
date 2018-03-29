---
title: Plug-Ins
visible: true
---

The standard plugins are part of the M2IF core and can be used OOTB. When it'll be necessary to implement your own components, you'll go fine in most cases, if you don't implement your own plug-in, but instead use the `SubjectPlugin` and provide your functionality in form of subjects, observers and services.

### Cache Warmer

The Cache Warmer plug-in pre-loads data from the database on repository level. So, to avoid unnecessary database access, the data for the EAV attribute option values, that'll be loaded by the `EavAttributeOptionValueRepository` for **EVERY** row can be pre-loaded by registering the repository for the Cache Warmer plug-in. This is only an example and it'll be not necessary to add this to your configuration, as the repository will be registered by default.

To register another repository, add the repository ID to the `cache-warmers` parameter like 

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\CacheWarmerPlugin",
  "params": {
  	"cache-warmers": [
      "import.repository.cache.warmer.eav.attribute.option.value"
    ] 
  }
}
```

> Be aware, that only repositories that implement the `TechDivision\Import\Repositories\CacheWarmer\CacheWarmerInterface` can be registered here!

### Global Data

Load's the global data, necessary for the import process from the database and add's it to the registry, so that every plugin can access it. This 

The configuration has to be like

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\GlobalsPlugin"
}
```

### Subject

This is the plug-in that does the main work by invoking the registered subjects as well as their registered observers and callbacks. When using M2IF as a framework, this plug-in will be a really good entrypoint to add your custom functionality. In most cases, it'll be quite enough to write a subject and observers that provides the necessary functionality and will be invoke by this plug-in. 

The plug-in configuration is

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\SubjectPlugin",
  "subjects": [ ... ]
}
```

whereas you can register as many subjects and observers as necessary.

### Archive

The archive plug-in zip's the import artefacts and moves them to the configured archive folder.

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\ArchivePlugin"
}
```

To activate the archive functionality, you've to activate the `archive-artefacts` node in your configuration file like

```json
{
  "magento-edition": "EE",
  "magento-version": "2.1.7",
  "operation-name" : "add-update",
  "source-date-format": "n/d/y, g:i A",
  "archive-artefacts" : true,
  ...
}
```

### Missing Option Values

This plugin provides the extended functionality to track whether an attribute option value, referenced in a CSV import file, is available or not, depending on `debug mode` enabled or not. If the `debug mode` is **NOT** enabled, an exception will be thrown immediately, else each missing attribute option value will be written to the CSV file `missing-option-values.csv` that'll stored in the temporary import directory and optionally sent to the specified mail recipients.

The configuration of the plugin can look like

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\MissingOptionValuesPlugin",
  "swift-mailer" : {
    "factory" : "TechDivision\\Import\\Utils\\SwiftMailer\\SmtpTransportMailerFactory",
    "mailer-factory" : "\\Swift_Mailer",
    "params" : [
      {
        "to" : "info@my-domain.tld",
        "from" : "info@my-domain.tld",
        "subject": "Something Went Wrong",
        "content-type" : "text/plain"
      }
    ],
    "transport" : {
      "transport-factory" : "\\Swift_SmtpTransport",
      "params" : [
        {
          "smtp-host" : "my-domain.tld",
          "smtp-port" : 25,
          "smtp-security" : "tls",
          "smtp-auth-mode" : "LOGIN",
          "smtp-username" : "your-username",
          "smtp-password" : "your-password"
        }
      ]
    }
  }
}
```

whereas the `swift-mailer` configuration node is optionally. Only if the configuration for the Swift Mailer is available, the CSV file will be send to the specified recipients.
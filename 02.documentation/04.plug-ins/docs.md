---
title: Plug-Ins
visible: true
---

The standard plugins are part of the M2IF core and can be used OOTB. Most components will go fine, if they don't implement their own plug-in, instead using the `SubjectPlugin` and provide their functionality in form of subjects, observers  and services.

### Global Data

Load's the global data, necessary for the import process from the database and add's it to the registry, so that every plugin can access it. The configuration has to be like

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\GlobalsPlugin"
}
```

### Subject

This the plugin that does the main work by invoking the subjects as well as their registered observers and callbacks. The plugin configuration is

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\SubjectPlugin",
  "subjects": [ ... ]
}
```

### Archive

The archive plugin zip's the import artefacts and moves them to the configured archive folder.

```json
{
  "class-name": "TechDivision\\Import\\Plugins\\ArchivePlugin"
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
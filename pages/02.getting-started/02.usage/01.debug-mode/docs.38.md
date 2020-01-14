---
title: 'Debug Mode'
taxonomy:
    category:
        - docs
visible: true
---

The debug mode provides a more detailed logging output (including PHP version, a list with activated extensions and a check if XDebug is enabled), by automatically setting the Monolog log level to `LogLevel::DEBUG` if **NOT** overwritten with the commandline option `--log-level`. Additionally it ignores

* product category relations that not exists 
* product images that are **NOT** available
* product images that can **NOT** be cleaned-up, e.g. the files are **NOT** available anymore
* product links (related, upsell, crosssell, etc.) for SKUs which are **NOT** available anymore
* product tier prices that can **NOT** be deleted, e. g. they are **NOT** available anymore
* product URL rewrites that can **NOT** be created
* configurable products for SKUs which are **NOT** available or available more than one time
* archive plugin can not create the archive directory, e. g. invalid permissions
* missing option values
* mission option values plugin will send a CSV file with the missing option values to the configured mail address

but logs these issues as warnings to the console.

When the debug mode has been enabled, missing attribute option values will **NOT** throw an exception, instead they will logged and put on an internal stack. If the [MissingOptionValuesPlugin](/framework/plug-ins?#missing-option-values) has been enabled, a CSV file with the missing option values will be created in the temporary import folder. If a Swift Mailer has been enabled by the plugin configuration, the CSV file will be sent to the given mail addresses.

This will help developers to test imports with partially invalid CSV files which do **NOT** break data consistency.

! To improve performance, up from version 3.6.0, metadata that allows to retrace above artefacts where the field data has been originated in, is only available in debug mode. In production mode, exceptions will now only contain name and line number of the actual artefact.
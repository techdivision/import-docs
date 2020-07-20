---
title: 'Delta Import'
visible: true
---

Beside the full import functionality, Pacemaker Community also supports delta import functionality. The delta import supports SKUs for

* all link types (up-sell, cross-sell + related)
* variant products
* grouped products
* bundle products

that are **NOT** part of one of the actual CSV files.

In contrast to a full import, which requires the following invocation of the appropriate indexers, after a delta import the [delta indexing](https://devdocs.magento.com/guides/v2.3/extension-dev-guide/indexing.html#m2devgde-mview) functionality takes care for the necessary index updates. The delta indexer can not be started on the commandline, instead it'll be started by the default Magento CRON job.
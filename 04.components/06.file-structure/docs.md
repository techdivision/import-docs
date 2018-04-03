---
title: 'File Structure'
visible: true
---

Generally, M2IF supports any given file format to be imported. The main format, and actually the only provided one, is the CSV format, which in case for the product import, is 100 % compatible with the Magento 2 standard import. The file format for the available import entities product, category and attribute is described in the following chapters.

Additional formats can be implemented by either adding new adapters, e. g. for XML or a plug-in to transform XML to CSV. In case of writing an adapter, the default CSV adapter can simply be replaced by overriding the DI configuration. If you want to write a plug-in, that converts one or more XML files into the apropriate CSV format, the plug-in has to be integrated by adding it the configuration of the Workflow Engine.


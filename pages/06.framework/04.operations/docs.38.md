---
title: Operations
taxonomy:
    category:
        - docs
visible: true
---

An operation reflects a step on an import command like the `add-update` and combines the necessary functionality as something like a container. It allows you to add a custom configuration for an existing operation or to add a new one. 

#### When do i need an operation?

You'll need an operation, if you want to integrate your own custom implementation e. g. based on a plug-in, subjects or observers. An operation allows you to combine these classes based on your needs.

!!!! In general, you'll need an operation when you want to create a new functionality combined out of plug-ins, subjects and observers. 

#### How to implement an operation?

An operation can not be implemented as it is a set of plug-ins that will be executed in the configured order. Up since version 3.8.0, most operations only have **ONE** plugin, as the chaining of operations and the order in which they have to be executed can be defined in the shortcuts. In general, you can configure an operation that matches your requirements, like 

```json
{
  "operations" : {
    "general": {
      "catalog_product": {
        "my-operation": {
          "plugins" : [
            {
              "id": "import.plugin.subject",
              "subjects" : [
                {
                  "id": "my.subject.id",
                  "file-resolver": {
                    "prefix": "my-prefix"
                  }
                }
              ]    
            }
          ]
        }
      }
    }
  }
}
```

The operations has to be defined under the Magento Edition they should be available and the entity type they are dedicated to. If the Magento Edition doesn't matter, you can use `general`, otherwise `ce` (for community) or `ee` (for commerce) are supported values. 
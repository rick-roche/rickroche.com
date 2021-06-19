---
title: "Migrating Azure ARM templates to Bicep"
subtitle: "How to migrate from Azure ARM templates to Bicep templates that have no errors and don't contain any warnings or linting issues!"
date: 2021-06-18T09:00:00+02:00
lastmod: 2021-06-17T11:13:20+02:00
draft: false
description: "You may have heard of Bicep, and you may be wondering how much effort it is going to take to move all your ARM templates to this new way of deploying Azure resources. I gave migrating from ARM to Bicep a go. This post will cover going from JSON ARM templates to shiny new Bicep templates that have no errors and don't contain any warnings or linting issues!"

tags: ["azure", "arm templates", "bicep", "migrating to bicep", "azure cli", "infra"]
categories: ["software"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "migrating-to-bicep.png"
featuredImagePreview: "migrating-to-bicep.png"
images: ["migrating-to-bicep.png"]

toc:
  enable: true
math:
  enable: false
lightgallery: false
---

You may have heard of [Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview "What is Bicep?"), and you may be wondering how much effort it is going to take to move all your ARM templates to this new way of deploying Azure resources.

I gave migrating from ARM to Bicep a go. This post will cover going from JSON ARM templates to shiny new Bicep templates that have no errors and don't contain any warnings or linting issues!

## Why should you migrate?

If you have ever deployed infra to Azure you have most likely used ARM templates before. They do the job, the docs aren't bad and there are built in tasks for ADO which make deploying them super straightforward. However, working with JSON is always going to be verbose, ARM templates have a lot of boilerplate required, making reusable templates is clunky and deployments can get tricky.

Aiming to address these (and other) issues, Azure is working on a project called Bicep. From the [projects overview](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview "What is Bicep?"):

> Bicep is a domain-specific language (DSL) that uses declarative syntax to deploy Azure resources. It provides concise syntax, reliable type safety, and support for code reuse. We believe Bicep offers the best authoring experience for your Azure infrastructure as code solutions.

Essentially it is a DSL built on-top of ARM that makes the development of templates simpler and promotes reuse through modules. Judging by the release rate [on their GitHub](https://github.com/Azure/bicep/releases "Bicep Releases") the team is working hard to make it awesome very quickly and according to them, as of v0.3, Bicep is now supported by Microsoft Support Plans and Bicep has 100% parity with what can be accomplished with ARM Templates. So Bicep is definitely prod ready (at time of writing v0.4.63 was the latest release)!

## Getting started

Ensure you have the [latest version](https://github.com/Azure/bicep/releases "Bicep Releases") of Bicep installed (excellent installation guide [here](https://github.com/Azure/bicep/blob/main/docs/installing.md "Setup your Bicep development environment")). I went with the `az cli` install option and all my examples will be using that variant. I also use Visual Studio Code and installed the [Bicep VS Code Extension](https://github.com/Azure/bicep/blob/main/docs/installing.md#install-the-bicep-vs-code-extension "Install the Bicep VS Code extension") for enhanced editing.

## Decompiling ARM to Bicep

First things first, it is possible to [decompile](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/decompile?tabs=azure-cli "Decompiling ARM template JSON to Bicep") an ARM template into Bicep by running the below command (all our ARM templates are in separate folders with an `azuredeploy.json` file for the template)

```bash
az bicep decompile --file azuredeploy.json
```

This creates a `azuredeploy.bicep` file in the same directory. Depending on your template, you will most likely get a stream of yellow warnings in the console, or potentially some red errors: don't panic! Even a single error will result in all the console output being red (at least on macOS) and at the very least you will get this warning message:

> WARNING: Decompilation is a best-effort process, as there is no guaranteed mapping from ARM JSON to Bicep.
> You may need to fix warnings and errors in the generated bicep file(s), or decompilation may fail entirely if an accurate conversion is not possible.
> If you would like to report any issues or inaccurate conversions, please see https://github.com/Azure/bicep/issues.

**It is important to note that the migration from ARM to Bicep will highlight issues in your ARM templates. ARM was more forgiving in certain aspects, after the migration, your templates will be in a better state.**

Errors come in the form of `Error BCPXXX: Description` and the descriptions generally let you get to the root of the problem quickly. E.g. *Error BCP037: The property "location" is not allowed on objects of type "Microsoft.EventHub/namespaces/eventhubs". Permissible properties include "dependsOn".*

If the decompilation gives you any errors, my preferred approach is to fix the ARM template and then run the decompilation again until you get a "clean" decompilation (warnings are fine, just ensure you decompile with no errors).

Warnings come in two flavours, ones that have a code (*Warning BCPXXX*) and ones that don't have a code but have a link at the end. As of `v0.4` there is a [linter](https://github.com/Azure/bicep/blob/main/docs/linter.md "Bicep Linter") which helps you get your templates into great shape -- these are the warnings with the links at the end. Both kinds of warnings are generally quick to fix and are sensible updates to start getting the benefits from Bicep.

## Common errors and how to fix them

### User defined functions are not supported (BCP007, BCP057)

If you have created user defined functions in ARM, these will not be decompiled (follow the [issue](https://github.com/Azure/bicep/issues/2)) and you will get two cryptic errors
> Error BCP007: This declaration type is not recognized. Specify a parameter, variable, resource, or output declaration.

> Error BCP057: The name "`functionName`" does not exist in the current context.

**To fix, move the function logic into your template (this may require duplication, but can be neatened up in the Bicep template later)**

### Nested dependsOn (BCP034)

Sometimes the use of nested `dependsOn` properties in your ARM templates gets weird with Bicep (`dependsOn` can often be [removed entirely](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/compare-template-syntax#resource-dependencies "Bicep Resource dependencies") in Bicep). The ARM version would have validated and deployed perfectly fine however you will get the following error when decompiling to Bicep.

> Error BCP034: The enclosing array expected an item of type "module[] | (resource | module) | resource[]", but the provided item was of type "string".

Fortunately [Bicep handles dependencies](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/compare-template-syntax#resource-dependencies "Bicep Resource dependencies") in a much smarter way than ARM meaning you can safely remove your nested dependencies and run again.

> For Bicep, you can set an explicit dependency but this approach isn't recommended. Instead, rely on implicit dependencies. An implicit dependency is created when one resource declaration references the identifier of another resource.

**To fix, delete your nested dependencies and validate (consider removing your `dependsOn` references entirely)**

### Strict schema validation (BCP037, BCP073)
Bicep validates the schema of each resource much more diligently than ARM. As a result you will probably find a bunch of places where you have added properties like `location` or `tags` to a resource that doesn't support them and ARM didn't mind this. These will be expressed as `Error BCP037`.

E.g. I had `location` on [Microsoft.EventHub/namespaces/eventhubs](https://docs.microsoft.com/en-us/azure/templates/microsoft.eventhub/namespaces/eventhubs?tabs=json "Microsoft.EventHub/namespaces/eventhubs schema")

> Error BCP037: The property "location" is not allowed on objects of type "Microsoft.EventHub/namespaces/eventhubs". Permissible properties include "dependsOn".

The one error I did run into was for [Azure Logic Apps](https://azure.microsoft.com/en-us/services/logic-apps/ "Azure Logic Apps") where the schema for [Microsoft.Logic/workflows](https://docs.microsoft.com/en-us/azure/templates/microsoft.logic/workflows?tabs=json "Microsoft.Logic/workflows schema") is missing the `identity` property needed for using Managed Identity with your Logic App:
    
> Error BCP037: The property "identity" is not allowed on objects of type "Microsoft.Logic/workflows". Permissible properties include "dependsOn".

Another common error I encountered was having read-only parameters in my templates (these tend to come along if you have exported templates from the Azure Portal). E.g.

> Error BCP073: The property "kind" is read-only. Expressions cannot be assigned to read-only properties.

**To fix both types of errors, remove the properties that the error messages highlight and run the decompilation again.**

### Reserved words as parameters (BCP079)

In ARM templates you can have the same name for a parameter, variable, function etc as these are all addressed directly using `parameters('name')` or `variables('name')` etc. In Bicep the syntax is simplified and as such you will get errors if you have used a reserved word as a parameter. We had a couple of templates taking in a parameter called `description` resulting in a cryptic error message:

> Error BCP079: This expression is referencing its own declaration, which is not allowed.

**To fix, rename the parameter in your ARM template, update its usages and run the decompilation again.**

## Common warnings and how to fix them

Hopefully by now you are out of the error zone, for warnings you can now start editing the Bicep file itself. The [Bicep VS Code Extension](https://github.com/Azure/bicep/blob/main/docs/installing.md#install-the-bicep-vs-code-extension "Install the Bicep VS Code extension") gives you great intellisense and highlights issues in the Bicep file.

To test an update on your Bicep file, run

```bash
az bicep build --file azuredeploy.bicep
```

### Schema warnings, enums and types (BCP035, BCP036, BCP037, BCP073, BCP081, BCP174)

[Data types](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/data-types "Bicep Data Types") in Bicep are stricter than in ARM. If you have used `True` or `False` for booleans these need to be updated to be `true` and `false`. When using enums, they need to match the enums in the schema exactly (case-sensitive). E.g. if you used `ascending` and the template schema defines `Ascending` this adjustment needs to be made. Examples of these warnings are shown below.

> Warning BCP036: The property "kafkaEnabled" expected a value of type "bool | null" but the provided value is of type "'False' | 'True'".

> Warning BCP036: The property "order" expected a value of type "'Ascending' | 'Descending' | null" but the provided value is of type "'ascending'".

Similar to the errors thrown by BCP037 and BCP073 you will get warnings on schema mismatches using codes BCP035, BCP037 and BCP073. These highlight schema mismatches, missing properties and read-only properties used. Examples below.

> Warning BCP035: The specified "object" declaration is missing the following required properties: "options".

> Warning BCP037: The property "apiVersion" is not allowed on objects of type "Output". No other properties are allowed.

> Warning BCP073: The property "status" is read-only. Expressions cannot be assigned to read-only properties.

I did find an instance where the Bicep template matches the schema perfectly however warnings are still thrown. I think this is due to [this issue](https://github.com/Azure/bicep/issues/3215 "Property values originating from resource properties are not validated correctly") and fortunately doesn't break anything.

**To fix, update the offending property to match the schema or data type**

### String interpolation

`concat` gets to disappear in Bicep thanks to the lovely string interpolation option. I experienced two variants:

> Warning prefer-interpolation: Use string interpolation instead of the concat function. [https://aka.ms/bicep/linter/prefer-interpolation]

**To fix, search for all usages of `concat` and replace with the new syntax: `'string-${var}'`.**

> Warning simplify-interpolation: Remove unnecessary string interpolation. [https://aka.ms/bicep/linter/simplify-interpolation]

**To fix, remove the `${}` and reference the variable directly. E.g. `'${variable}'` becomes `variable`.**

### Environment URLs

We had a lot of hardcoded URL's in our templates for storage suffixes, front door etc. There is a much better way to do this by using the `environment()` function. Caveat here, if you had `environment` as a parameter to your ARM template, update this to be something else like `env` for example otherwise you won't be able to use the function.

> Warning no-hardcoded-env-urls: Environment URLs should not be hardcoded. Use the environment() function to ensure compatibility across clouds. Found this disallowed host: "core.windows.net" [https://aka.ms/bicep/linter/no-hardcoded-env-urls]

**To fix, have a look at all the [URLs that the environment function provides](https://docs.microsoft.com/en-za/azure/azure-resource-manager/templates/template-functions-deployment?tabs=json#environment "Environment Template Function") and replace your hard-coded version with `environment().<property>`. **

E.g. for Azure Storage where `core.windows.net` had been hard coded, replacing with `environment().suffixes.storage` gives the desired result.

### Scopes (BCP174)

Bicep introduces the concept of target scopes which dictates the scope that resources within that deployment are created in. This gives you a new way to define resources where you previously would have used the `/providers/` syntax (role assignments, diagnostic settings etc). The warning you get looks as follows.

> Warning BCP174: Type validation is not available for resource types declared containing a "/providers/" segment. Please instead use the "scope" property. [https://aka.ms/BicepScopes]

These are the most time-consuming to fix, essentially you need to
- use the schema reference of the actual resource (so for diagnostic settings us `microsoft.insights/diagnosticSettings@2017-05-01-preview`) instead of the parent resource `/providers`/
- add a `scope` referencing the parent resource
- rename so as not to reference the parent resource
- remove unnecessary properties and `dependsOn`

**Before**

```bicep
resource functionAppLogAnalytics 'Microsoft.Web/sites/providers/diagnosticSettings@2017-05-01-preview' = {
  name: '${functionAppName}/Microsoft.Insights/LogAnalytics'
  tags: tagsVar
  properties: {
    name: 'LogAnalytics'
    workspaceId: resourceId(logAnalyticsResourceGroup, 'Microsoft.OperationalInsights/workspaces', logAnalyticsWsName)
    logs: [
      {
        category: 'FunctionAppLogs'
        enabled: true
      }
    ]
  }
  dependsOn: [
    functionApp
  ]
}
```

**After**

```bicep
resource functionAppLogAnalytics 'microsoft.insights/diagnosticSettings@2017-05-01-preview' = {
  name: LogAnalytics
  scope: functionApp
  properties: {
    workspaceId: logAnalyticsResourceId
    logs: [
      {
        category: 'FunctionAppLogs'
        enabled: true
      }
    ]
  }
}
```

## Should you migrate?

A resounding yes from me! The process above goes quite quickly, and I was on shiny new Bicep templates in less than an hour. Bicep is a much friendlier syntax to work with and the IDE support is great. What I also enjoyed is cleaning up all the errors that were present in my ARM templates that would have remained if not for the migration.

There is a neat comparison of ARM syntax vs Bicep syntax here which highlights a lot of the constructs that become simpler as well: https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/compare-template-syntax

Enjoy the migration! I will be playing around with modules and reuse next and will share what I find.

Featured image background by [Nick Fewings](https://unsplash.com/@jannerboy62?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/bird-migrating?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText).

---
title: "Bicep Modules: Refactor, Compose, Reuse"
subtitle: "Lessons learnt by refactoring Bicep templates into reusable modules"
date: 2021-07-07T20:58:17+02:00
lastmod: 2021-07-07T20:58:17+02:00
draft: true
description: "Bicep introduces the concept of modules to enable template reuse. I took some time out of my day to refactor an application that had been using ARM templates (converted to Bicep templates) to use Bicep modules. This post will cover the things that I learnt by working through that process."

tags: ["azure", "bicep", "modules", "devops", "infra"]
categories: ["software"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "bicep-modules-cover.png"
featuredImagePreview: "bicep-modules-cover.png"

toc:
  enable: true
math:
  enable: false
lightgallery: false
---

In my previous post I touched on the things I learnt while [migrating ARM templates to Bicep](https://www.rickroche.com/2021/06/migrating-azure-arm-templates-to-bicep/ "Migrating Azure ARM Templates to Bicep"). [Bicep](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/overview "What is Bicep?") also introduces the concept of modules to enable template reuse. I took some time to refactor a composite application that had already been converted from using ARM to Bicep templates, to use Bicep modules. This post will cover the things that I learnt by working through that process.

## Why modules?

From the [Bicep documentation](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/modules):

> Bicep enables you to break down a complex solution into modules. A Bicep module is a set of one or more resources to be deployed together. Modules abstract away complex details of the raw resource declaration, which can increase readability. You can reuse these modules, and share them with other people. Bicep modules are transpiled into a single ARM template with [nested templates](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/linked-templates#nested-template "Nested Templates") for deployment.

One of the traps we fell into with ARM templates was duplicating templates to make composing and deploying the resources we need easier. Any opportunity to make the deployment of infrastructure more readable, more reusable, and more composable are excellent reasons for me to give it a go. My goal when doing this refactor was to
- get rid of any duplication by creating fine-grained modules, designed to be reused
- ensure that all main templates are super easy to use by composing modules together in a way that makes sense for the application
- enable reusability of the fine-grained modules in other projects going forward.

In short: let's make the infra readable, composable and reusable.

## Notable learnings

### Reusable Modules

I went with the approach of trying to make a reusable module for each Azure resource type and putting the modules into a folder called `modules` and making sub folders for template groupings. For example,

```bash
modules/
    appInsights.bicep
    appServicePlan.bicep
    functionApp.bicep
    logAnalytics.bicep
    storageAccount/
        storageAccount.bicep
        tables.bicep
```

Personally I prefer to have one module to create a storage account and another to add tables to that storage account etc. This level of granularity felt like a good place to start.

### Referencing existing resources inside a module
By making fine-grained modules, there were a number of use cases where I would need to reference an existing resource. For example, creating tables in a storage account requires an existing storage account. [Referencing an existing resource](https://github.com/Azure/bicep/blob/main/docs/spec/resources.md#referencing-existing-resources "Bicep Existing Resources") is really easy -- you only need to know its name and can reference it as follows,

```bicep
// Lookup an existing resource
resource storageAccount 'Microsoft.Storage/storageAccounts@2021-02-01' existing = {
  name: storageAccountName
  // Optional if the existing resource is in a different resource group or subscription
  scope: resourceGroup(subscriptionId, resourceGroupName)
}

// You can now use the resource as if you had created it. e.g.
outputs storageAccountResourceId = storageAccount.id
```

### Loops!

I really like the [loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md "Bicep loops") feature. This allows you to iterate over an array setting multiple properties or creating multiple resources etc. This came in super handy for a storage account tables module that can create multiple tables in one go. E.g.

```bicep
param storageAccountName string
param tables array = [
  {
    container: 'default'
    name: 'replace'
  }
]

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-02-01' existing = {
  name: storageAccountName
}

resource storageAccountTables 'Microsoft.Storage/storageAccounts/tableServices/tables@2021-02-01' = [for table in tables: {
  name: '${storageAccount.name}/${table.container}/${table.name}'
  dependsOn: [
    storageAccount
  ]
}]

output storageAccountTableNames array = [for (table, i) in tables: {
  name: storageAccountTables[i].name
}]
```

Note the use of the different styles of the loops in the `storageAccountTables` resource and the outputs.

### Functions and Expressions

There are loads of [functions](https://docs.microsoft.com/en-us/azure/azure-resource-manager/bicep/bicep-functions "Bicep Functions") and [expressions](https://github.com/Azure/bicep/blob/main/docs/spec/expressions.md "Bicep expressions") that you can use in your Bicep files and I won't go into all of them. There were a few that I used regularly, and it's hopefully useful that I call them out.

#### Union

> `union(arg1, arg2, arg3, ...)`
> Returns a single array or object with all elements from the parameters. Duplicate values or keys are only included once.

I used union everywhere I wanted to have fixed (opinionated) defaults in the module, but allow additional parameters to be merged in. For example, with function apps I defined base settings I want all function apps to have and still allow for additional app settings to be passed in and merged with the base.

```bicep
@description('Additional app settings for your function app')
param additionalAppSettings object = {}

var appSettingsBase = {
  FUNCTIONS_EXTENSION_VERSION: '~3'
  FUNCTIONS_WORKER_RUNTIME: 'dotnet'
  WEBSITE_RUN_FROM_PACKAGE: '1'
  AzureWebJobsStorage: 'DefaultEndpointsProtocol=https;AccountName=${storageAccount.name};EndpointSuffix=${environment().suffixes.storage};AccountKey=${listKeys(storageAccount.id, storageAccount.apiVersion).keys[0].value}'
}

var appSettings = union(appSettingsBase, additionalAppSettings)
```

We can also get rid of all the hardcoded schema API versions when looking up keys against a resource by using `<resource>.apiVersion` as well as hardcoded suffixes by using the [environment function](https://www.rickroche.com/2021/06/migrating-azure-arm-templates-to-bicep/#environment-urls "Environment URLs"), in this case using the storage suffix: `environment().suffixes.storage`.

Another super useful call out would be to the `listKeys` function. It allows you to get connection strings or keys from your resources and is super handy (see the `AzureWebJobsStorage` example above). [John Reilly](https://blog.johnnyreilly.com/about "About John Reilly") went into [detail on this over here](https://blog.johnnyreilly.com/2021/07/07/output-connection-strings-and-keys-from-azure-bicep "John Reilly - Output connection strings and keys from Azure Bicep"), please have a read!

#### Ternary

I found that with Bicep I use the [ternary operator](https://github.com/Azure/bicep/blob/main/docs/spec/expressions.md#ternary-operator "Bicep Ternary Operator") a lot in my main templates when composing the modules. For example, only enabling a secondary region when the environment is `nonprod` or `prod` but not in `dev` can easily be described as,

```bicep
var secondaryRegionEnabled = (contains(env, 'prod')) ? true : false
```

Much cleaner and readable without all the JSON around it.

## Composing modules together

Every Bicep file can be consumed as a module which is an awesome feature. I chose to break my files up as follows:

```bash
infra/
  myApp/
    main.bicep - loads up the app.bicep and monitoring.bicep modules
    app.bicep - uses the fine grained modules - app service plans, functions, azure storage etc
    monitoring.bicep - uses the fine grained modules - app insights, log analytics etc
shared-infra/
  modules/
    <all the fine-grained modules>
```

In my `main.bicep` I reference the two local Bicep files as modules

```bicep
module monitoring 'monitoring.bicep' = {
  name: '${appName}-monitoring'
  params: {
    tags: tags
    location: location
    appInsightsName: appInsightsName
    logAnalyticsWsName: logAnalyticsWsName
  }
}

module app 'app.bicep' = {
  name: '${appName}-app'
  params: {
    tags: tags
    location: location
    appName: appName
    storageAccountName: storageAccountName
    appInsightsName: monitoring.outputs.appInsightsName
    logAnalyticsWsName: monitoring.outputs.logAnalyticsWsName
    monitoringResourceGroup: monitoring.outputs.ResourceGroupName
  }
}
```

Note that the `app` module relies on the outputs of the monitoring module -- this helps Bicep figure out the dependencies so that you don't need to define `dependsOn` any more.

Then in `monitoring.bicep` I can reference fine-grained reusable modules that I'd like to share with other projects in much the same way

```bicep
module log '../../shared-infra/modules/logAnalytics.bicep' = {
  name: '${appName}-log'
  params: {
    tags: tags
    location: location
    logAnalyticsWsName: logAnalyticsWsName
  }
}

module appi '../../shared-infra/modules/appInsights.bicep' = {
  name: '${appName}-appi'
  params: {
    tags: tags
    location: location
    appInsightsName: appInsightsName
    logAnalyticsWsName: log.outputs.logAnalyticsWsName
  }
}
```

Another thing to note is the `name` of your module is what is shown in the `Deployments` tab of the Azure Portal, so make these make sense to you for easy debugging if deployments are breaking.

## Sharing modules

At this stage I've got different two styles of modules -- app specific modules breaking up my `main.bicep`, making it easier to read and maintain and fine-grained templates in a `modules` folder that I can use across all the apps in my `infra` folder. Readable -- check! Composable -- check! Reusable -- only inside this repo, so half a check!

The current version of Bicep (`v0.4.63`), does not have a native mechanism to externally share modules across projects. The good news is that this is being looked at and will _hopefully_ be in the v0.5 release. The issues to watch are:
- [[Story] Bicep Registry](https://github.com/Azure/bicep/issues/2128 "[Story] Bicep Registry")
- [Ability to reference "external" modules](https://github.com/Azure/bicep/issues/660 "Ability to reference 'external' modules")
- [Modules need versions](https://github.com/Azure/bicep/issues/1242 "Modules need versions")

In the interim, I am using [Git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules "Git Submodules") to solve this, although this will not work with all CI/CD tooling when using private repos. To enable this, I moved the modules in `shared-infra` into its own repo and added it back to my project.

```bash
git submodule add <path-to-repo> shared-infra/
git submodule update --init
```

## Testing locally

To quickly validate the individual modules and main templates on my local machine, I wrote a [simple bash script](https://gist.github.com/rick-roche/57c90abae06630060afce1e76372b13b "infra.sh Gist") to either
- build the Bicep file, outputting to the terminal rather than writing to file or
- validate the Bicep template against a resource group

```bash
#!/bin/bash
RG=replace-with-your-rg
SUB=replace-with-your-sub

op=$1 # lint, validate, create
main_shared=$2
if [ "$main_shared" == 'main' ]; then path='./infra' && filename='main.bicep'; fi
if [ "$main_shared" == 'shared' ]; then path='./shared-infra' && filename='*.bicep'; fi

lint () {
    az bicep build --file "$f" --stdout
}

validate () {
    az deployment group validate --resource-group "$RG" --subscription "$SUB" -f "$1" -p env=dev
}

for f in $(find "$path" -name "$filename") ; do
    echo "$f"
    $op "$f"
done
```

The script allows one to test either `main` or `shared` templates, linting them or validating them:

```bash
./infra.sh lint main
./infra.sh lint shared
./infra.sh validate main
```

## Finishing up

After the refactor, my project is in a far cleaner state and the infrastructure is much easier to follow. A second plus is that we now have a separate repo of our shared modules that can become a shared asset across our various teams (using Git submodules for now and the Bicep registry in the future).

Featured image background by [Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText "Michael Dziedzic") on [Unsplash](https://unsplash.com/s/photos/modules?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

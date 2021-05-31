---
title: "Azure Pipelines and Dependabot"
subtitle: ""
date: 2021-05-31T11:21:36+02:00
lastmod: 2021-05-31T11:21:36+02:00
draft: true
description: "Keeping dependencies up to date using Dependabot and Azure Pipelines"

tags: ["dependabot", "dependencies", "azure pipelines", "azure devops"]
categories: ["software"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: "dependabot.png"
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: false
license: ""
---

Keeping your dependencies up to date in a project is a really easy way to try and keep the software secure. New releases of a dependency often include
- Patches for security vulnerabilities!
- Performance improvements!
- Awesome new features!
- Bug fixes!

It can also be quite a boring activity and can be time consuming for a team maintaining the project to run updates regularly into production. Fortunately tools like [Dependabot](https://dependabot.com/) exist!

<!--more-->

## Dependabot?

[Dependabot](https://dependabot.com/) creates pull requests on your repos with the dependencies you should update. Read about [How it works](https://dependabot.com/#how-it-works) but in a nutshell
- it checks for updates of your dependencies
- it opens up a pull request on your repo
- you review the PR and merge

This is an awesome tool to save you time and I find it makes keeping your dependencies up to date really easy. 

## Integration with Azure Pipelines

Dependabot is baked into the [Github ecosystem](https://docs.github.com/en/code-security/supply-chain-security/keeping-your-dependencies-updated-automatically/about-dependabot-version-updates) and really easy to use there. Recently I needed to solve this problem on a project in [Azure DevOps](https://azure.microsoft.com/en-us/services/devops/) using [Azure Pipelines](https://azure.microsoft.com/en-us/services/devops/pipelines/) and thought I would share my solution.

Search the [Azure DevOps Extension Marketplace](https://marketplace.visualstudio.com/) for Dependabot and you will find [this extension](https://marketplace.visualstudio.com/items?itemName=tingle-software.dependabot) made by [Tingle Software](https://tingle.software/). It is always great to find extensions to speed up integration and I gave this one a whirl.

The extension is feature rich and works really well with little configuration required. Simply adding the below to an Azure Pipeline, running it and you will end up with a host of PR's!

```yaml
stages:
  - stage: CheckDependencies
    displayName: 'Check Dependencies'
    jobs:
      - job: Dependabot
        displayName: 'Run Dependabot'
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - task: dependabot@1
            displayName: 'Run Dependabot'
            inputs:
              packageManager: 'nuget' # Examples: nuget, maven, gradle, npm, etc. Add multiple tasks if multiple package managers are used in your solution
              targetBranch: 'main'
              openPullRequestsLimit: 10 # Limits the number of PR's you get
              setAutoComplete: true # Saves us one click, once our PR policies pass, the update will merge
```

Have a look at all the [Task Parameters](https://github.com/tinglesoftware/dependabot-azure-devops/blob/main/src/extension/README.md#task-parameters) that the extension supports to fine tune your implementation.

### Work Item Linking

In the project I'm working in we have a policy set on all PR's to ensure they are linked to a work item. When I ran the above pipeline, I ended up with 10 PR's, none of which were linked to a work item so I couldn't quickly review and merge each one. The extension handles this by allowing you to pass in a `workItemId` parameter. 

{{< admonition type=note title="Changes in release 0.5" open=true >}}
In the upcoming 0.5 release of the extension, the `workItemId` parameter has been [renamed](https://github.com/tinglesoftware/dependabot-azure-devops/pull/130) to `milestone` so please be on the look out for this change!
{{< /admonition >}}

The Microsoft team have an [extension for creating work items](https://marketplace.visualstudio.com/items?itemName=mspremier.CreateWorkItem) which is really configurable. For my teams workflow, we wanted to have a User Story on our board with all the PR's linked to it. We also didn't want the pipeline creating duplicate User Stories every time it runs if we already had one open that we are working on. After a bit of trial and error, adding the below step to the pipeline gave us the desired result

```yaml
- task: CreateWorkItem@1
  displayName: 'Create User Story for Dependabot'
  inputs:
    workItemType: 'User Story' # We wanted a User Story created, but all work item types are available
    title: 'Update Dependencies' # The title of the work item
    # Adding some tags to the work item
    fieldMappings: |
      Tags=dependabot; dependencies 
    areaPath: 'your\area'
    iterationPath: 'your\iteration'
    # Adding duplicate detection, using the area path, iteration path and title to match
    preventDuplicates: true
    keyFields: |
      System.AreaPath
      System.IterationPath
      System.Title
    # Create output variables for the next task to be able use the work item ID
    createOutputs: true
    outputVariables: |
      workItemId=ID
```

### Docker Caching

The [Dependabot extension](https://marketplace.visualstudio.com/items?itemName=tingle-software.dependabot) depends on a Docker image hosted on [Docker Hub](https://hub.docker.com/r/tingle/dependabot-azure-devops/tags?page=1&ordering=last_updated). The image is around `4.4GB` in size and can take some time to download in the pipeline.

In the docs it mentions 
> Since this task makes use of a docker image, it may take time to install the docker image. The user can choose to speed this up by using [Caching for Docker](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/caching?view=azure-devops#docker-images) in Azure Pipelines.

The caching tasks can look a bit confusing, breaking it down you need 3 parts:

```yaml
- task: Cache@2
  inputs:
    key: docker | "${{ variables.imageToCache }}" # image to look for in the cache, e.g. tingle/dependabot-azure-devops:0.4
    path: $(Pipeline.Workspace)/docker # path of the cache
    cacheHitVar: DOCKER_CACHE_HIT # variable to set to true if a cache hit is found
  displayName: Cache Docker images
```

This checks if there is a cached version of the image to be used. If yes, `DOCKER_CACHE_HIT` is set to `true`. otherwise `false`.

```yaml
- script: |
    docker load -i $(Pipeline.Workspace)/docker/cache.tar
  displayName: Restore Docker image
  condition: and(not(canceled()), eq(variables.DOCKER_CACHE_HIT, 'true'))
```

If we have a cached version of the image, we need to pipeline to download it. This step only runs if `DOCKER_CACHE_HIT` is set to `true` and will download the cached archive and load it into the docker context.

```yaml
- script: |
    mkdir -p $(Pipeline.Workspace)/docker
    docker pull -q ${{ parameters.imageToCache }}
    docker save -o $(Pipeline.Workspace)/docker/cache.tar ${{ parameters.imageToCache }}
  displayName: Save Docker image
  condition: and(not(canceled()), or(failed(), ne(variables.DOCKER_CACHE_HIT, 'true')))
```

If we do not have a cached version of the image we need to pull it down from Docker Hub and save it as an archive to be cached.

## Finishing up

We have a pipeline that runs on a schedule, running all of the above together. A complete example can be found on Github [here](https://gist.github.com/rick-roche/c083ba72e9acd7635cff3c2757ee04bf).

```yaml
schedules:
  - cron: "0 4 * * 1"
    displayName: 'Weekly Run'
    always: true
    branches:
      include:
        - main

trigger: none

variables:
  - name: imageToCache
    value: tingle/dependabot-azure-devops:0.4

stages:
  - stage: CheckDependencies
    displayName: 'Check Dependencies'
    jobs:
      - job: Dependabot
        displayName: 'Run Dependabot'
        pool:
          vmImage: 'ubuntu-latest'
        steps:
          - task: CreateWorkItem@1
            displayName: 'Create User Story for Dependabot'
            inputs:
              workItemType: 'User Story'
              title: 'Update Dependencies'
              fieldMappings: |
                Tags=dependabot; dependencies
              areaPath: 'your\area'
              iterationPath: 'your\iteration'
              preventDuplicates: true
              keyFields: |
                System.AreaPath
                System.IterationPath
                System.Title
              createOutputs: true
              outputVariables: |
                workItemId=ID

          - task: Cache@2
            inputs:
              key: docker | "${{ variables.imageToCache }}"
              path: $(Pipeline.Workspace)/docker
              cacheHitVar: DOCKER_CACHE_HIT
            displayName: Cache Docker images
          - script: |
              docker load -i $(Pipeline.Workspace)/docker/cache.tar
            displayName: Restore Docker image
            condition: and(not(canceled()), eq(variables.DOCKER_CACHE_HIT, 'true'))
          - script: |
              mkdir -p $(Pipeline.Workspace)/docker
              docker pull -q ${{ variables.imageToCache }}
              docker save -o $(Pipeline.Workspace)/docker/cache.tar ${{ variables.imageToCache }}
            displayName: Save Docker image
            condition: and(not(canceled()), or(failed(), ne(variables.DOCKER_CACHE_HIT, 'true')))

          - task: dependabot@1
            displayName: 'Run Dependabot'
            inputs:
              packageManager: 'nuget'
              targetBranch: 'main'
              openPullRequestsLimit: 10
              workItemId: $(workItemId)
              setAutoComplete: true
```

In summary the pipeline does the following for you

- [x] Runs on a weekly schedule (update the cron to suite your needs)
- [x] Creates a new work item, with the tags we would like (avoiding duplicates)
- [x] Tries to use a cached version of the image for faster builds (creating a cached version if not found)
- [x] Run Dependabot, limiting the number of open PR's to 10, linking the PR's to the created work item and completing the PR once all the policies pass

Hopefully this will help you to get your projects running in Azure DevOps nice and up to date!

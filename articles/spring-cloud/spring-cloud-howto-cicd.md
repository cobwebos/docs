---
title: 适用于 Azure 春季云的 CI/CD
description: 适用于 Azure 春季云的 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 79d9b2a0d706dd2d9861d068de0e4671db1c5158
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089105"
---
# <a name="cicd-for-azure-spring-cloud"></a>适用于 Azure 春季云的 CI/CD

通过持续集成和持续交付工具，你可以快速地将更新部署到现有应用程序，并具有最小的工作量和风险。 Azure DevOps 可帮助你组织和控制这些关键作业。 目前，Azure 春季云不提供特定的 Azure DevOps 插件。  但是，可以使用 [Azure CLI 任务](/azure/devops/pipelines/tasks/deploy/azure-cli?preserve-view=true&view=azure-devops)将春季云应用程序与 DevOps 集成。

本文介绍如何将 Azure CLI 任务与 Azure 春季 Cloud 结合使用来与 Azure DevOps 集成。

## <a name="create-an-azure-resource-manager-service-connection"></a>创建 Azure 资源管理器服务连接

阅读 [本文](/azure/devops/pipelines/library/connect-to-azure?preserve-view=true&view=azure-devops) ，了解如何创建 azure 资源管理器服务连接到 azure DevOps 项目。 请确保选择用于 Azure 春季云服务实例的同一订阅。

## <a name="azure-cli-task-templates"></a>Azure CLI 任务模板
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>部署项目

你可以使用一系列的来生成和部署项目 `tasks` 。 此代码片段定义变量、用于生成应用程序的 .NET Core 任务，以及用于部署 *.zip* 文件的 Azure CLI 任务。

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --is-public true
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>部署项目

你可以使用一系列的来生成和部署项目 `tasks` 。 此代码段首先定义用于生成应用程序的 Maven 任务，接下来是使用 Azure 春季云 Azure CLI 扩展部署 JAR 文件的第二个任务。

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>从源部署

无需单独的生成步骤便可直接部署到 Azure。

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](spring-cloud-quickstart.md)
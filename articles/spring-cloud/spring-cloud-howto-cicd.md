---
title: Azure 春云的 CI/CD
description: Azure 春云的 CI/CD
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278514"
---
# <a name="cicd-for-azure-spring-cloud"></a>Azure 春云的 CI/CD

持续集成和持续交付工具使开发人员能够以最小的工作量和风险快速将更新部署到现有应用程序。 Azure DevOps 可帮助您组织和控制这些关键作业。 目前，Azure 春云不提供特定的 Azure DevOps 插件。  但是，您可以使用[Azure CLI 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)将春云应用程序与 DevOps 集成。 本文将介绍如何使用 Azure 春云的 Azure CLI 任务与 Azure DevOps 集成。

## <a name="create-an-azure-resource-manager-service-connection"></a>创建 Azure 资源管理器服务连接

阅读[本文](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)，了解如何创建 Azure 资源管理器服务连接到 Azure DevOps 项目。 请务必选择与 Azure Spring 云服务实例相同的订阅。

## <a name="azure-cli-task-templates"></a>Azure CLI 任务模板

### <a name="deploy-artifacts"></a>部署项目

您可以使用一系列`tasks`生成和部署项目。 此代码段首先定义 Maven 任务以生成应用程序，然后是使用 Azure SpringCloud Azure CLI 扩展名部署 JAR 文件的第二个任务。

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

无需单独的生成步骤即可直接部署到 Azure。

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

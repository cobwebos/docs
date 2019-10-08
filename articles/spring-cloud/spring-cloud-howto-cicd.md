---
title: 适用于 Azure 春季云的 CI/CD
description: 适用于 Azure 春季云的 CI/CD
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 96795315fcb0dd9b90cd55e8baa46c6a394882ce
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038829"
---
# <a name="cicd-for-azure-spring-cloud"></a>适用于 Azure 春季云的 CI/CD

通过持续集成和持续交付工具，开发人员可快速将更新部署到现有应用程序，并将其工作量和风险降到最低。 Azure DevOps 可帮助你组织和控制这些关键作业。 目前，Azure 春季云不提供特定的 Azure DevOps 插件。  但是，可以使用[Azure CLI 任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)将春季云应用程序与 DevOps 集成。 本文介绍如何将 Azure CLI 任务与 Azure 春季 Cloud 结合使用来与 Azure DevOps 集成。

## <a name="create-an-azure-resource-manager-service-connection"></a>创建 Azure 资源管理器服务连接

阅读[本文](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops)，了解如何创建 azure 资源管理器服务连接到 azure DevOps 项目。 请确保选择用于 Azure 春季云服务实例的同一订阅。

## <a name="azure-cli-task-templates"></a>Azure CLI 任务模板

### <a name="deploy-artifacts"></a>部署项目

可以使用一系列 @no__t 来生成和部署项目。 此代码段首先定义用于生成应用程序的 Maven 任务，接下来是使用 Azure 春季云 Azure CLI 扩展部署 JAR 文件的第二个任务。

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
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
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
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```

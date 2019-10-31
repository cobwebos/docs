---
title: Deployment best practices - Azure App Service | Microsoft Docs
description: Learn about the key components of deploying to Azure App Service.
keywords: azure app service, web app, deploy, deployment, pipelines, build
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: 121ea4b7e29510ef86b61350ed97ffca5d133d56
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199485"
---
# <a name="deployment-best-practices"></a>Deployment Best Practices

Every development team has unique requirements that can make implementing an efficient deployment pipeline difficult on any cloud service. This article introduces the three main components of deploying to App Service: deployment sources, build pipelines, and deployment mechanisms. This article also covers some best practices and tips for specific language stacks.

## <a name="deployment-components"></a>Deployment Components

### <a name="deployment-source"></a>Deployment Source

A deployment source is the location of your application code. For production apps, the deployment source is usually a repository hosted by version control software such as [GitHub, BitBucket, or Azure Repos](deploy-continuous-deployment.md). For development and test scenarios, the deployment source may be [a project on your local machine](deploy-local-git.md). App Service also supports [OneDrive and Dropbox folders](deploy-content-sync.md) as deployment sources. While cloud folders can make it easy to get started with App Service, it is not typically recommended to use this source for enterprise-level production applications. 

### <a name="build-pipeline"></a>Build Pipeline

Once you decide on a deployment source, your next step is to choose a build pipeline. A build pipeline reads your source code from the deployment source and executes a series of steps (such as compiling code, minifying HTML and JavaScript, running tests, and packaging components) to get the application in a runnable state. The specific commands executed by the build pipeline depend on your language stack. These operations can be executed on a build server such as Azure Pipelines, or executed locally.

### <a name="deployment-mechanism"></a>Deployment Mechanism

The deployment mechanism is the action used to put your built application into the */home/site/wwwroot* directory of your web app. The */wwwroot* directory is a mounted storage location shared by all instances of your web app. When the deployment mechanism puts your application in this directory, your instances receive a notification to sync the new files. App Service supports the following deployment mechanisms:

- Kudu 终结点： [Kudu](https://github.com/projectkudu/kudu/wiki)是一种开源开发人员生产力工具，它作为 Windows 应用服务中的单独进程运行，并作为 Linux 应用服务中的第二个容器运行。 Kudu 处理连续部署并提供用于部署的 HTTP 终结点，如 zipdeploy。
- FTP 和 WebDeploy：使用您的[站点或用户凭据](deploy-configure-credentials.md)，可以[通过 FTP](deploy-ftp.md)或 WebDeploy 上传文件。 这些机制不会经过 Kudu。  

部署工具（例如 Azure Pipelines、Jenkins 和编辑器插件）使用这些部署机制之一。

## <a name="language-specific-considerations"></a>特定于语言的注意事项

### <a name="java"></a>Java

使用 Kudu [zipdeploy/](deploy-zip.md) API 部署 JAR 应用程序，并使用[WARDEPLOY/](deploy-zip.md#deploy-war-file) for WAR 应用。 如果你使用的是 Jenkins，则可以在部署阶段直接使用这些 Api。 有关详细信息，请参阅[此文章](../jenkins/execute-cli-jenkins-pipeline.md)。

### <a name="node"></a>节点

默认情况下，Kudu 执行节点应用程序的生成步骤（`npm install`）。 如果使用的是生成服务（例如 Azure DevOps），则不需要 Kudu 内部版本。 若要禁用 Kudu 生成，请创建一个应用设置 `SCM_DO_BUILD_DURING_DEPLOYMENT`，并将值设置 `false`。

### <a name="net"></a>.NET 

默认情况下，Kudu 执行 .Net 应用程序的生成步骤（`dotnet build`）。 如果使用的是生成服务（例如 Azure DevOps），则不需要 Kudu 内部版本。 若要禁用 Kudu 生成，请创建一个应用设置 `SCM_DO_BUILD_DURING_DEPLOYMENT`，并将值设置 `false`。

## <a name="other-deployment-considerations"></a>其他部署注意事项

### <a name="use-deployment-slots"></a>使用部署槽位

请尽可能在部署新的生产版本时使用[部署槽位](deploy-staging-slots.md)。 使用标准应用服务计划层或更好的应用程序层时，可将应用部署到过渡环境，验证更改，以及执行冒烟测试。 准备就绪后，可以交换过渡和生产槽。 交换操作得到预热必要的辅助角色实例，以匹配生产规模，从而消除停机。 

### <a name="local-cache"></a>本地缓存

Azure 应用服务内容存储在 Azure 存储中，作为内容共享持续提供。 但是，某些应用程序只需要高性能的只读内容存储，其可在高可用性下运行。 这些应用程序可以受益于使用[本地缓存](overview-local-cache.md)。 对于内容管理站点（例如 WordPress），不建议使用本地缓存。

始终将本地缓存与[部署槽位](deploy-staging-slots.md)一起使用，以防止停机。 有关将这些功能一起使用的信息，请参阅[此部分](overview-local-cache.md#best-practices-for-using-app-service-local-cache)。

### <a name="high-cpu-or-memory"></a>高 CPU 或内存

如果你的应用服务计划使用超过90% 的可用 CPU 或内存，则基础虚拟机在处理你的部署时可能会遇到问题。 发生这种情况时，请暂时向上缩放实例计数以执行部署。 完成部署后，可以将实例计数返回到它以前的值。

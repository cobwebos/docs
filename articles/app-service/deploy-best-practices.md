---
title: 部署最佳做法-Azure App Service |Microsoft Docs
description: 了解部署到 Azure App Service 的关键组件。
keywords: azure 应用服务, web 应用, 部署, 部署, 管道, 生成
services: app-service
documentationcenter: ''
author: jasonfreeberg
manager: ''
editor: ''
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.custom: ''
ms.openlocfilehash: 2beb35002cb98119db90f4cd278decc185ea35d7
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536490"
---
# <a name="deployment-best-practices"></a>部署最佳实践

每个开发团队都有独特的要求, 这使得在任何云服务上都难以实现有效的部署管道。 本文介绍了部署到应用服务的三个主要组件: 部署源、生成管道和部署机制。 本文还介绍了特定语言堆栈的一些最佳实践和提示。

## <a name="deployment-components"></a>部署组件

### <a name="deployment-source"></a>部署源

部署源是应用程序代码的位置。 对于生产应用, 部署源通常是由版本控制软件 (例如[GitHub、BitBucket 或 Azure Repos](deploy-continuous-deployment.md)) 托管的存储库。 对于开发和测试方案, 部署源可以是[本地计算机上的项目](deploy-local-git.md)。 应用服务还支持[OneDrive 和 Dropbox 文件夹](deploy-content-sync.md)作为部署源。 尽管云文件夹可以轻松地开始使用应用服务, 但通常不建议将此源用于企业级的生产应用程序。 

### <a name="build-pipeline"></a>生成管道

确定部署源后, 下一步是选择生成管道。 生成管道从部署源读取源代码, 并执行一系列步骤 (如编译代码、缩小 HTML 和 JavaScript、运行测试和打包组件) 以使应用程序处于可运行状态。 生成管道执行的特定命令取决于你的语言堆栈。 这些操作可在 Azure Pipelines 的生成服务器上执行, 或在本地执行。

### <a name="deployment-mechanism"></a>部署机制

部署机制是用于将生成的应用程序放入 web 应用程序的 */home/site/wwwroot 中*目录的操作。 */Wwwroot*目录是由 web 应用的所有实例共享的已装载存储位置。 当部署机制将应用程序放在此目录中时, 实例会收到同步新文件的通知。 应用服务支持以下部署机制:

- Kudu 终结点:[Kudu](https://github.com/projectkudu/kudu/wiki)是一种开源开发人员生产力工具, 它作为 Windows 应用服务中的单独进程运行, 并作为 Linux 应用服务中的第二个容器运行。 Kudu 处理连续部署并提供用于部署的 HTTP 终结点, 如 zipdeploy。
- FTP 和 WebDeploy:使用[站点或用户凭据](deploy-configure-credentials.md), 可以[通过 FTP](deploy-ftp.md)或 WebDeploy 上传文件。 这些机制不会经过 Kudu。  

部署工具 (例如 Azure Pipelines、Jenkins 和编辑器插件) 使用这些部署机制之一。

## <a name="language-specific-considerations"></a>特定于语言的注意事项

### <a name="java"></a>Java

使用 Kudu [zipdeploy/](deploy-zip.md) API 部署 JAR 应用程序, 并使用[WARDEPLOY/](deploy-zip.md#deploy-war-file) for WAR 应用。 如果你使用的是 Jenkins, 则可以在部署阶段直接使用这些 Api。 有关详细信息，请参阅[此文章](../jenkins/execute-cli-jenkins-pipeline.md)。

### <a name="node"></a>节点

默认情况下, Kudu 执行节点应用程序 (`npm install`) 的生成步骤。 如果使用的是生成服务 (例如 Azure DevOps), 则不需要 Kudu 内部版本。 若要禁用 Kudu 生成, 请创建一个应用设置`SCM_DO_BUILD_DURING_DEPLOYMENT`, 并将`false`值设置为。

### <a name="net"></a>.NET 

默认情况下, Kudu 执行 .Net 应用程序 (`dotnet build`) 的生成步骤。 如果使用的是生成服务 (例如 Azure DevOps), 则不需要 Kudu 内部版本。 若要禁用 Kudu 生成, 请创建一个应用设置`SCM_DO_BUILD_DURING_DEPLOYMENT`, 并将`false`值设置为。

## <a name="other-deployment-considerations"></a>其他部署注意事项

### <a name="use-deployment-slots"></a>使用部署槽位

请尽可能在部署新的生产版本时使用[部署槽位](deploy-staging-slots.md)。 使用标准应用服务计划层或更好的应用程序层时, 可将应用部署到过渡环境, 验证更改, 以及执行冒烟测试。 准备就绪后, 可以交换过渡和生产槽。 交换操作得到预热必要的辅助角色实例, 以匹配生产规模, 从而消除停机。 

### <a name="local-cache"></a>本地缓存

Azure 应用服务内容存储在 Azure 存储中，作为内容共享持续提供。 但是, 某些应用程序只需要高性能的只读内容存储, 其可在高可用性下运行。 这些应用程序可以受益于使用[本地缓存](overview-local-cache.md)。 对于内容管理站点 (例如 WordPress), 不建议使用本地缓存。

始终将本地缓存与 [部署槽] (部署过渡槽 md) 结合使用, 以防止停机。 有关将这些功能一起使用的信息, 请参阅[此部分](overview-local-cache.md#best-practices-for-using-app-service-local-cache)。

### <a name="high-cpu-or-memory"></a>高 CPU 或内存

如果你的应用服务计划使用超过 90% 的可用 CPU 或内存, 则基础虚拟机在处理你的部署时可能会遇到问题。 发生这种情况时, 请暂时向上缩放实例计数以执行部署。 完成部署后, 可以将实例计数返回到它以前的值。

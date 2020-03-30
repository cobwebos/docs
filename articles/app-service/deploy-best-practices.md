---
title: 部署最佳做法
description: 了解部署到 Azure 应用服务的关键机制。 查找特定于语言的建议和其他注意事项。
keywords: Azure 应用服务、Web 应用、部署、部署、管道、生成
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750471"
---
# <a name="deployment-best-practices"></a>部署最佳实践

每个开发团队都有独特的要求，使在任何云服务上实现高效的部署管道都很困难。 本文介绍了部署到应用服务的三个主要组件：部署源、生成管道和部署机制。 本文还介绍了特定语言堆栈的一些最佳实践和提示。

## <a name="deployment-components"></a>部署组件

### <a name="deployment-source"></a>部署源

部署源是应用程序代码的位置。 对于生产应用，部署源通常是由版本控制软件（如[GitHub、BitBucket 或 Azure 存储库](deploy-continuous-deployment.md)）托管的存储库。 对于开发和测试方案，部署源可能是[本地计算机上的项目](deploy-local-git.md)。 应用服务还支持[OneDrive 和 Dropbox 文件夹](deploy-content-sync.md)作为部署源。 虽然云文件夹可以方便地开始使用应用服务，但通常不建议将此源用于企业级生产应用程序。 

### <a name="build-pipeline"></a>生成管道

确定部署源后，下一步是选择生成管道。 生成管道从部署源读取源代码，并执行一系列步骤（如编译代码、对 HTML 和 JavaScript 进行简单化、运行测试和打包组件），以使应用程序处于可运行状态。 生成管道执行的特定命令取决于您的语言堆栈。 这些操作可以在生成服务器上执行（如 Azure 管道），也可以在本地执行。

### <a name="deployment-mechanism"></a>部署机制

部署机制是用于将内置应用程序放入 Web 应用的 */home/site/wwwroot*目录中的操作。 */wwwroot*目录是 Web 应用的所有实例共享的装载存储位置。 当部署机制将应用程序放入此目录中时，实例会收到同步新文件的通知。 应用服务支持以下部署机制：

- Kudu 终结点[：Kudu](https://github.com/projectkudu/kudu/wiki)是开源开发人员生产力工具，在 Windows 应用服务中作为单独的进程运行，并作为 Linux 应用服务中的第二个容器运行。 库杜处理连续部署并提供用于部署的 HTTP 终结点，如 zipdeploy。
- FTP 和 WebDeploy：使用[您的网站或用户凭据](deploy-configure-credentials.md)，您可以通过[FTP](deploy-ftp.md)或 WebDeploy 上传文件。 这些机制不通过库杜。  

部署工具（如 Azure 管道、Jenkins 和编辑器插件）使用这些部署机制之一。

## <a name="language-specific-considerations"></a>特定于语言的注意事项

### <a name="java"></a>Java

使用 Kudu [zipdeploy/API](deploy-zip.md)部署 JAR 应用程序，以及 WAR 应用程序[的战争部署/](deploy-zip.md#deploy-war-file) 如果使用 Jenkins，则可以在部署阶段直接使用这些 API。 有关详细信息，请参阅[此文章](../jenkins/execute-cli-jenkins-pipeline.md)。

### <a name="node"></a>节点

默认情况下，Kudu 执行 Node 应用程序 （）`npm install`的生成步骤。 如果使用生成服务（如 Azure DevOps），则不需要 Kudu 生成。 要禁用 Kudu 生成，请创建一个`SCM_DO_BUILD_DURING_DEPLOYMENT`应用设置，其值`false`为 。

### <a name="net"></a>.NET 

默认情况下，Kudu 执行 .Net 应用程序的生成步骤 （`dotnet build`。 如果使用生成服务（如 Azure DevOps），则不需要 Kudu 生成。 要禁用 Kudu 生成，请创建一个`SCM_DO_BUILD_DURING_DEPLOYMENT`应用设置，其值`false`为 。

## <a name="other-deployment-considerations"></a>其他部署注意事项

### <a name="use-deployment-slots"></a>使用部署槽

尽可能在部署新的生产生成时使用[部署槽](deploy-staging-slots.md)。 使用标准应用服务计划层或更高级别时，可以将应用部署到暂存环境、验证更改并执行烟雾测试。 准备就绪后，可以交换暂存和生产槽。 交换操作预热必要的辅助角色实例，以匹配您的生产规模，从而消除停机时间。 

### <a name="local-cache"></a>本地缓存

Azure 应用服务内容存储在 Azure 存储中，作为内容共享持续提供。 但是，某些应用只需要一个高性能的、只读的内容存储，它们就可以以高可用性运行。 这些应用程序可以从使用[本地缓存](overview-local-cache.md)中获益。 不建议对内容管理网站（如 WordPress）进行本地缓存。

始终将本地缓存与[部署插槽](deploy-staging-slots.md)结合使用，以防止停机。 有关将这些功能结合使用的信息，请参阅[本节](overview-local-cache.md#best-practices-for-using-app-service-local-cache)。

### <a name="high-cpu-or-memory"></a>高 CPU 或内存

如果应用服务计划使用了超过 90% 的可用 CPU 或内存，则基础虚拟机在处理部署时可能遇到问题。 发生这种情况时，请临时向上扩展实例计数以执行部署。 部署完成后，可以将实例计数返回到其上一个值。

有关最佳实践的详细信息，请访问[应用服务诊断](https://docs.microsoft.com/azure/app-service/overview-diagnostics)，了解特定于您的资源的可操作最佳做法。

- 在[Azure 门户](https://portal.azure.com)中导航到 Web 应用。
- 单击 **"诊断并解决**左侧导航中的问题"，该导航将打开应用服务诊断。
- 选择**最佳实践**主页磁贴。
- 单击 **"最佳**配置&可用性的最佳做法"或 **"最佳配置的最佳做法**"，查看应用对这些最佳实践的当前状态。

您还可以使用此链接直接打开资源的应用服务诊断： `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`。

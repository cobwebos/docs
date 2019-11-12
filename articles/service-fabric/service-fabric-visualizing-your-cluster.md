---
title: 使用 Azure Service Fabric Explorer 可视化群集 | Microsoft Docs
description: Service Fabric Explorer 是一个用于检验和管理 Microsoft Azure Service Fabric 群集中的云应用程序和节点的应用程序。
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 9b873b5a68979b8225c44c32e0b9494408e35ac1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927184"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>使用 Service Fabric Explorer 可视化群集

Service Fabric Explorer (SFX) 是一种用于检验和管理 Azure Service Fabric 群集的开源工具。 Service Fabric Explorer 是适用于 Windows、macOS 和 Linux 的桌面应用程序。

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer 下载

使用以下链接将 Service Fabric Explorer 下载为桌面应用程序：

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> 桌面版的 Service Fabric Explorer 可能比群集支持包含更多或更少的功能。 可回退到部署到群集的 Service Fabric Explorer 版本，以确保完全的功能兼容性。
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>从群集运行 Service Fabric Explorer

Service Fabric Explorer 同时在 Service Fabric 群集的 HTTP 管理终结点中进行托管。 若要在 Web 浏览器中启动 SFX，请从任意浏览器浏览到群集的 HTTP 管理终结点（例如 https:\//clusterFQDN:19080）。

对于开发人员工作站设置，可以通过导航到 https://localhost:19080/Explorer 在本地群集上启动 Service Fabric Explorer。 阅读本文，了解如何[准备开发环境](service-fabric-get-started.md)。

> [!NOTE]
> 如果群集受自签名证书保护，你将从 Web 浏览器收到错误消息“此站点不安全”。 你只需重写该警告即可在大多数新型 Web 浏览器中继续浏览。 在生产环境中，应使用公用名称和证书颁发机构颁发的证书来保护群集。 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>连接到 Service Fabric 群集
若要连接到 Service Fabric 群集，需要群集管理终结点 (FQDN/IP) 和 HTTP 管理终结点端口（默认情况下为 19080）。 例如，https\://mysfcluster.westus.cloudapp.azure.com:19080。 使用“连接到 localhost”复选框，连接到工作站上的本地群集。

### <a name="connect-to-a-secure-cluster"></a>连接到安全群集
可以使用证书或 Azure Active Directory (AAD) 控制客户端对 Service Fabric 群集的访问。

如果尝试连接到安全群集，则将需提供客户端证书或使用 AAD 登录，具体取决于群集的配置。

## <a name="understand-the-service-fabric-explorer-layout"></a>了解 SService Fabric Explorer 的布局
可以使用左侧的树来导航 Service Fabric Explorer。 在树根中，群集仪表板提供了群集的概述，包括应用程序和节点运行状况的摘要。

![Service Fabric Explorer 群集仪表板][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>查看群集的布局
Service Fabric 群集中的节点横跨容错域和升级域的二维网格放置。 这种放置可确保应用程序在发生硬件故障及应用程序升级时仍然可用。 可以使用群集图查看当前群集的布局方式。

![Service Fabric Explorer 群集图][sfx-cluster-map]

### <a name="view-applications-and-services"></a>查看应用程序和服务
群集包含两个子树：一个用于应用程序，另一个用于节点。

可以使用应用程序视图来导航 Service Fabric 的逻辑层次结构：应用程序、服务、分区和副本。

在以下示例中，应用程序 **MyApp** 由两个服务 **MyStatefulService** 与 **WebService** 组成。 由于 **MyStatefulService** 是有状态的，因此它包含一个分区，其中有一个主副本和两个辅助副本。 相反，WebSvcService 是无状态的，只包含单个实例。

![Service Fabric Explorer 应用程序视图][sfx-application-tree]

在树的每个级别，主窗格显示有关项目的信息。 例如，可以看到特定服务的运行状况和版本。

![Service Fabric Explorer 基本信息窗格][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>查看群集的节点
节点视图显示群集的物理布局。 对于给定的节点，可以检查已在该节点上部署代码的应用程序。 更具体地说，可以看到当前在那里运行的副本。

## <a name="actions"></a>操作
Service Fabric Explorer 提供用于对群集中的节点、应用程序和服务快速调用操作的方式。

例如，要删除某应用程序实例，只需从左侧树中选择该应用程序，并选择“操作” > “删除应用程序”。

![Service Fabric Explorer 中删除应用程序][sfx-delete-application]

> [!TIP]
> 可以通过单击每个元素旁边的省略号来执行相同的操作。
>
> 可以通过 Service Fabric Explorer 执行的每个操作也可以通过 PowerShell 或 REST API 执行，以实现自动化。
>
>

还可使用 Service Fabric Explorer 为给定应用程序类型和版本创建应用程序实例。 在树视图中选择应用程序类型，在右窗格中单击想要的版本旁边的“**创建应用实例**”链接。

![在 Service Fabric Explorer 中创建应用程序实例][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer 创建应用程序实例时不支持参数。 应用程序实例使用默认参数值。
>
>

## <a name="event-store"></a>事件存储
EventStore 是该平台提供的一项功能，它通过 REST API 提供可在 Service Fabric Explorer 中使用的 Service Fabric 平台事件。 可以查看群集中每个实体的动态快照视图，例如节点、服务、应用程序和基于事件时间的查询。 还可以从 [EventStore 概述](service-fabric-diagnostics-eventstore.md)了解有关 EventStore 的详细信息。   

![EventStore][sfx-eventstore]

>[!NOTE]
>从 Service Fabric 版本 6.4 开始。 EventStore 在默认情况下不启用，必须在资源管理器模板中启用

>[!NOTE]
>从 Service Fabric 版本 6.4 开始。 EventStore API 仅可用于在 Azure 上运行的 Windows 群集。 我们正在将此功能移植到 Linux 以及我们的独立群集。

## <a name="image-store-viewer"></a>映像存储查看器
映像存储查看器是使用本机映像存储时提供的一项功能，使用它可以查看映像存储的当前内容、获取文件和文件夹信息，以及删除文件/文件夹。

![Service Fabric Explorer 群集图][sfx-imagestore]

## <a name="backup-and-restore"></a>备份和还原
Service Fabric Explorer 提供与[备份和还原](./service-fabric-reliable-services-backup-restore.md)接口的功能。 若要查看 SFX 中的备份和还原功能，必须启用高级模式。

![启用高级模式][0]
 
可以执行以下操作：

* 创建、编辑和删除备份策略。
* 为应用程序、服务或分区启用和禁用备份。
* 挂起和恢复应用程序、服务或分区的备份。
* 触发和跟踪分区的备份。
* 为分区触发和跟踪还原。

有关备份和还原服务的详细信息，请参阅[REST API 引用](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)。
## <a name="next-steps"></a>后续步骤
* [在 Visual Studio 中管理 Service Fabric 应用程序](service-fabric-manage-application-in-visual-studio.md)
* [使用 PowerShell 部署 Service Fabric 应用程序](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png
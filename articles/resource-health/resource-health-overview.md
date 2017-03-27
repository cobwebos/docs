---
title: "Azure 资源运行状况概述 | Microsoft 文档"
description: "Azure 资源运行状况概述"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: resource-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 06/01/2016
ms.author: BernardoAMunoz
translationtype: Human Translation
ms.sourcegitcommit: d777bc6bd477c5b6645fc8bd7b6d57a5d2f89e22
ms.openlocfilehash: e465e2c1503add186a4b134e85bd9aab61d5c0ad


---
# <a name="azure-resource-health-overview"></a>Azure 资源运行状况概述
Azure 资源运行状况是一项服务，用于公开各个 Azure 资源的运行状况，并为排查问题提供可行的指南。 在无法直接访问服务器或基础结构元素的云环境中，资源运行状况的目标是减少客户用于排除故障的时间，尤其是减少为确定问题的根源是否在于应用程序或问题是否是由 Azure 平台内的事件引发而花费的时间。

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>什么是资源？资源运行状况如何决定资源是否正常运行？
资源是由服务提供的资源类型的用户创建的实例，例如：虚拟机、Web 应用或 SQL 数据库。 

资源运行状况依赖于由资源和/或服务发出的信号以确定资源是否正常运行。 请务必注意，资源运行状况仅考虑一种特定资源类型的运行状况，而不考虑可能有助于整体运行状况的其他元素。 例如，当报告虚拟机状态时，仅考虑基础结构的计算部分，即在资源运行状况中不会显示网络中的问题，除非存在已声明的服务中断，在此情况下，会在边栏选项卡顶部以横幅显示。 有关服务中断的详细信息，请参见本文的后面部分。 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>资源运行状况与服务运行状况仪表板有何不同？
资源运行状况提供的信息比服务运行状况仪表板提供的更详细。 SHD 与影响区域中服务可用性的事件进行通信，同时资源运行状况会显示与特定资源相关的信息，例如，它会显示影响虚拟机、Web 应用或 SQL 数据库可用性的事件。 例如，如果节点发生意外重新启动，则正在该节点上运行虚拟机的客户将会获得其 VM 在一段时间内不可用的原因。   

## <a name="how-to-access-resource-health"></a>如何访问资源运行状况
对于通过资源运行状况可用的服务，有两种用来访问资源运行状况方法。

### <a name="azure-portal"></a>Azure 门户
Azure 门户中的“资源运行状况”边栏选项卡提供有关资源健康状况的详细信息，以及根据资源不同的当前运行状况而变化的建议的操作。 此边栏选项卡在查询资源运行状况时提供最佳体验，因为它有助于访问门户内的其他资源。 如前文所述，“资源运行状况”边栏选项卡中的一组建议的操作会根据当前运行状况而变化：

* 正常运行的资源：由于未检测到可能影响资源运行状况的问题，因此这些操作重点帮助故障排除过程。 例如，它可以直接访问“故障排除”边栏选项卡，这提供了有关如何解决客户面临的最常见问题的指导。
* 非正常运行的资源：对于由 Azure 导致的问题，边栏选项卡将显示 Microsoft 正在采取（或已采取）恢复资源的操作。 对于由用户启动的操作导致的问题，边栏选项卡将显示客户可以采取的操作列表，以解决问题并恢复资源。  

登录到 Azure 门户后，可通过两种方式访问“资源运行状况”边栏选项卡： 

### <a name="open-the-resource-blade"></a>打开“资源”边栏选项卡
打开给定资源的“资源”边栏选项卡。 在“资源”边栏选项卡旁边打开的左侧边栏选项卡上，单击“支持 + 故障排除”下面的“资源运行状况”以打开“资源运行状况”边栏选项卡。 

![“资源运行状况”边栏选项卡](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>“帮助和支持”边栏选项卡
通过单击右上角的问号，然后选择“帮助和支持”，打开“帮助和支持”边栏选项卡。 

**从顶部导航栏**

![帮助 + 支持](./media/resource-health-overview/HelpAndSupport.png)

在“帮助 + 支持”边栏选项卡旁边打开的左边栏选项卡上单击“支持 + 故障排除”下面的“资源运行状况”。 单击此选项可打开“资源运行状况订阅”边栏选项卡，其中列出了你的订阅中的所有资源。 在每个资源旁边，都有一个图标指示其运行状况。 单击资源将打开“资源运行状况”边栏选项卡。

**资源运行状况磁贴**

![资源运行状况磁贴](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>我的资源运行状况是什么意思？
你可能会看到你的资源有 4 种不同的运行状况。

### <a name="available"></a>可用
服务未检测到平台中可能会影响资源可用性的任何问题。 这会由绿色复选标记图标指示。 

![资源可用](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>不可用
在此情况下，服务检测到平台中存在正在影响该资源可用性的正在运行的问题，例如，VM 发生意外重新启动的节点。 这会由红色警告图标指示。 有关该问题的其他信息，请参见边栏选项卡的中间部分，其中包括： 

1. Microsoft 正在采取哪些措施来恢复资源 
2. 问题的详细时间线，包括预期的解决时间
3. 用户的推荐操作列表 

![资源不可用](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>不可用 - 客户启动
由于客户请求（例如停止资源或请求重启）导致资源不可用。 这会由蓝色信息图标指示。 

![由于用户启动操作导致资源不可用](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Unknown
该服务已超过 5 分钟未收到有关此资源的信息。 这会由灰色问号图标指示。 

请务必注意，这并不是明确指示资源存在问题，因此客户应该遵循以下建议：

* 如果资源按预期运行，但其资源运行状况中的运行状况被设置为“未知”，则表示没有问题，你可以预期资源的状态在几分钟后会更新为正常。
* 如果访问资源时出现问题，并且其资源运行状况中的运行状况被设置为“未知”，则这可能是表明可能存在问题的早期迹象，应进行其他调查直到运行状况更新为正常或不正常为止

![资源运行状况未知](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>服务影响事件
如果资源可能受到正在运行的服务影响事件的影响，则会在“资源运行状况”边栏选项卡的顶部显示一个横幅。 单击横幅将打开“审核事件”边栏选项卡，其中显示有关中断的其他信息。

![资源运行状况可能会受 SIE 影响](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>有关资源运行状况，我还需要知道什么？
### <a name="signal-latency"></a>信号延迟
提供资源运行状况的信号可能最多延迟 15 分钟，这可能导致资源的当前运行状况与其实际可用性之间的差异。 请务必牢记这将有助于消除花在调查可能存在的问题上的不必要的时间。 

### <a name="special-case-for-sql"></a>SQL 的特殊情况
资源运行状况报告 SQL 数据库的状态，而不是 SQL 服务器的状态。 虽然这样提供了更真实的运行状况，但仍需考虑多个组件和服务才能确定数据库的运行状况。 当前信号依赖于登录到数据库，这意味着对于接收常规登录（其中包括接收查询执行请求）的数据库，将定期显示运行状况。 如果数据库在 10 分钟或更长时间内没有被访问，它将被移动到未知状态。 这并不意味着该数据库不可用，只是因为没有执行登录而没有发出信号。 连接到数据库并运行查询将发出确定和更新数据库的运行状况所需的信号。

## <a name="feedback"></a>反馈
我们始终乐于接受反馈和建议！ 请向我们发送你的[建议](https://feedback.azure.com/forums/266794-support-feedback)。 此外，你可以通过 [Twitter](https://twitter.com/azuresupport) 或 [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure)与我们联系。




<!--HONumber=Jan17_HO2-->



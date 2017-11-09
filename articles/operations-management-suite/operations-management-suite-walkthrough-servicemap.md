---
title: "服务映射解决方案自控进度型演示 | Microsoft Docs"
description: "服务映射是 Operations Management Suite (OMS) 中的解决方案，可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。  本文为自控进度型演示，介绍了如何使用服务映射来确定和诊断 Web 应用程序中的模拟问题。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: c3548d24c74f8ad865b22d6af3490d0b5cc77a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-oms-self-paced-demo---service-map"></a>Operations Management Suite (OMS) 自控进度型演示 - 服务映射
本文为自控进度型演示，介绍了如何在 Operations Management Suite (OMS) 中使用[服务映射解决方案](operations-management-suite-service-map.md)来确定和诊断 Web 应用程序中的模拟问题。  服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。  它还整合其他 OMS 服务收集的数据，协助你分析性能并确定问题。  还将使用 [Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-searches.md)深入分析收集的数据，以便确定根本问题。


## <a name="scenario-description"></a>方案描述
刚收到一则通知，了解到 ACME 客户门户应用程序出现了性能问题。  获得的唯一信息是，这些问题是在今天凌晨约 4:00 (PST) 时出现的。  除了一组 Web 服务器，并不完全确定该门户依赖的所有组件。  

## <a name="components-and-features-used"></a>所使用的组件和功能
- [服务映射解决方案](operations-management-suite-service-map.md)
- [Log Analytics 日志搜索](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>演练

### <a name="1-connect-to-the-oms-experience-center"></a>1.连接到 OMS 体验中心
本演示使用 [Operations Management Suite 体验中心](https://experience.mms.microsoft.com/)，该中心提供完整的 OMS 环境和示例数据。 开始时，请单击该链接，提供信息，然后选择“Insight and Analytics”方案。


### <a name="2-start-service-map"></a>2.启动服务映射
通过单击“服务映射”磁贴启动服务映射解决方案。

![“服务映射”磁贴](media/operations-management-suite-walkthrough-servicemap/tile.png)

此时会显示服务映射控制台。  左窗格中是你环境中安装了服务映射代理的计算机的列表。  请从该列表中选择要查看的计算机。

![计算机列表](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3.查看计算机
我们知道，Web 服务器的名称为 AcmeWFE001 和 AcmeWFE002，因此看来可以从这里开始。  单击“AcmeWFE001”。  此时会显示 AcmeWFE001 的映射及其所有依赖项。  可以看到哪些进程运行在所选计算机上，以及这些进程与哪些外部服务通信。

![Web 服务器](media/operations-management-suite-walkthrough-servicemap/web-server.png)

我们关注的是 Web 应用程序的性能，因此请单击“AcmeAppPool (IIS 应用池)”进程。  此时会显示该进程的详细信息，并突出显示其依赖项。  

![应用池](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4.更改时间窗口

我们已经知道，问题发生在凌晨 4:00，因此让我们看看该时间发生的事情。 单击“时间范围”，将时间更改为凌晨 4:00 PST（保留当前日期，针对本地时区进行调整），并将持续时间设置为 20 分钟。

![时间选取器](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5.查看警报

现在可以看到，**acmetomcat** 依赖项显示了一个警报，因此这可能是问题之所在。  单击 **acmetomcat** 中的警报图标，显示警报详细信息。  可以看到 CPU 使用率极度异常，将其展开即可获得更多详细信息。  这可能是导致性能下降的原因。 

![警报](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6.查看性能

让我们细看一下 **acmetomcat**。  单击 **acmetomcat** 右上角，选择“加载服务器映射”，以便显示此计算机的详细信息和依赖项。 然后，我们可以更详细地查看这些性能计数器，验证我们的怀疑。  选择“性能”选项卡，显示相应时间范围内 [Log Analytics 收集的性能计数器](../log-analytics/log-analytics-data-sources-performance-counters.md)。  可以看到，处理器和内存定期出现峰值。

![性能](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7.查看更改跟踪
让我们看看，能否找出导致使用率如此之高的原因。  单击“摘要”选项卡。该选项卡提供 OMS 从计算机收集的信息，例如失败的连接、严重警报、软件更改。  包含最新相关信息的部分应已展开，可以展开其他部分，检查其所包含的信息。


如果“更改跟踪”尚未打开，则请将其展开。  此时会显示[更改跟踪解决方案](../log-analytics/log-analytics-change-tracking.md)收集的信息。  看起来在此时间窗口进行了软件更改。  单击“软件”获取详细信息。  凌晨 4:00 刚过时，计算机上添加了一个备份进程，看起来这就是资源过度使用的原因。

![更改跟踪](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8.在日志搜索中查看详细信息
我们可以查看 Log Analytics 存储库中收集的详细性能信息，进一步对此进行验证。  再次单击“警报”选项卡，然后单击某个“CPU 过高”警报。  单击“在日志搜索中显示”。  此时会打开“日志搜索”窗口，可以在其中针对存储库中存储的任何数据执行[日志搜索](../log-analytics/log-analytics-log-searches.md)。  服务映射中已经填充了一个查询，供我们检索感兴趣的警报。  

![日志搜索](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9.打开保存的搜索
让我们看看，能否获取性能收集方面的更多详细信息（正是此方面的异常生成了该警报），并验证我们的怀疑，即问题是由该备份进程导致的。  将时间范围更改为“6 小时”。  然后单击“收藏夹”，向下滚动到针对“服务映射”保存的搜索。  这些是我们专为此分析创建的查询。  单击“acmetomcat 的前 5 个进程(按 CPU)”。

![保存的搜索](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


该查询返回一个列表，其中包含 **acmetomcat** 上处理器使用率最高的前 5 个进程。  可以查看该查询，简单了解日志搜索所使用的查询语言。  如果对其他计算机上的进程感兴趣，则可修改该查询以检索相应的信息。

在本示例中，我们可以看到，备份进程始终在消耗应用服务器约 60% 的 CPU。  很明显，这个新进程导致了性能问题。  显然，解决办法就是从应用程序服务器中删除这个新的备份软件。  实际上，我们可以利用 Azure 自动化托管的 Desired State Configuration (DSC) 来定义策略，确保该进程不会在这些关键系统上运行。


## <a name="summary-points"></a>要点
- [服务映射](operations-management-suite-service-map.md)提供整个应用程序的视图，即使你不知道其所有服务器和依赖项。
- 服务映射可呈现其他 OMS 解决方案收集的数据，帮助你确定应用程序及其底层基础结构存在的问题。
- [日志搜索](../log-analytics/log-analytics-log-searches.md)用于深入分析 Log Analytics 存储库中收集的具体数据。    

## <a name="next-steps"></a>后续步骤
- 详细了解[服务映射](operations-management-suite-service-map.md)。
- [部署和配置](operations-management-suite-service-map-configure.md)服务映射。
- 了解 [Log Analytics](../log-analytics/log-analytics-overview.md)，这是服务映射所需要的，用于存储代理所存储的操作数据。
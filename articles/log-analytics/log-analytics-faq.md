---
title: "Log Analytics 常见问题解答 | Microsoft Docs"
description: "有关 Azure Log Analytics 服务的常见问题解答。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 9ee8f4aafcc35e43c4fcba5a3a72b043dd9fc32c
ms.openlocfilehash: 4695669dc20b4b4b90ccdaf4db06df2cfcba2167
ms.lasthandoff: 02/21/2017


---
# <a name="log-analytics-faq"></a>Log Analytics 常见问题解答
此 Microsoft 常见问题解答是有关 Microsoft 操作管理套件 (OMS) 中 Log Analytics 的常见问题列表。 如果你有与 Log Analytics 有关的任何其他问题，请转到[论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)并发布你的问题。 来自我们社区的人员将帮助你回答问题。 如果某个问题是常见问题，我们会将该问题添加到本文中，以便可以轻松快捷地找到该问题。

## <a name="general"></a>常规
**问：AD 和 SQL 评估解决方案会执行哪些检查？**

A. 以下查询显示了当前执行的所有检查的说明：

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

结果可以导出到 Excel 供进一步查看。

**问：为什么我看到的 *OMS* 与 SCOM 管理中的不同？**

答：这样取决于你应用了哪些 SCOM 更新汇总，您可能会看到*系统中心顾问*、*操作见解*或 *Log Analytics* 节点。

对 *OMS* 进行的文本字符串更新包含在一个管理包中，需要你手动导入。 按照最新 SCOM 更新汇总知识库文章中的说明，刷新 OMS 控制台，以查看 *OMS* 节点中的最新更新。

**问：是否有*本地* OMS 版本？**

答：没有。 Log Analytics 处理并存储巨大量的数据。 作为云服务，Log Analytics 可根据需要向上扩展，以避免对你的环境造成任何性能影响。

此外，作为云服务意味着你不需要使 Log Analytics 基础结构保持运行，就可接收到频繁的功能更新和修补程序。

## <a name="configuration"></a>配置
**问：可以更改用于从 Azure 诊断 (WAD) 读取数据的表/blob 容器的名称吗？**  

A.    目前不可以，但计划在将来的版本中实现。

**问：OMS 服务使用哪些 IP 地址？如何确保我的防火墙仅允许与 OMS 服务通信？**  

A. Log Analytics 服务构建于 Azure 之上，终结点只接收 [Microsoft Azure 数据中心 IP 范围](http://www.microsoft.com/download/details.aspx?id=41653)内的 IP。

当进行服务部署时，OMS 服务的实际 IP 地址会发生变化。 [在 Log Analytics 中配置代理和防火墙设置](log-analytics-proxy-firewall.md)中记录了允许通过防火墙的 DNS 名称。

**问：我使用 ExpressRoute 连接到 Azure。我的 Log Analytics 通信流量会使用我的 ExpressRoute 连接吗？**  

A. 不同类型的 ExpressRoute 通信流量记录在 [ExpressRoute 文档](../expressroute/expressroute-faqs.md#supported-services)中。

Log Analytics 通信流量使用的是公共对等 ExpressRoute 线路。

**问：有没有简单易用的方法将现有的 Log Analytics 工作区移到另一个 Log Analytics 工作区/Azure 订阅？**  我们正在 Azure 订阅中测试和检验几个客户的 OMS 工作区。它们将要移到生产环境，因此我们想将它们移到其自身的 Azure/OMS 的订阅。  

A. `Move-AzureRmResource` Cmdlet 允许你移动 Log Analytics 工作区，也可以将自动化帐户从一个 Azure 订阅移到另一个订阅。 有关详细信息，请参阅[移动 - AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx)。

也可在 Azure 门户中进行此更改。

你不能将数据从一个 Log Analytics 工作区移到另一个工作区，或更改 Log Analytics 数据所存储的区域。

**问：如何将 OMS 添加到 SCOM？**

答：更新到最新的更新汇总并导入管理包后，你就可以将 SCOM 连接到 Log Analytics。

请注意，SCOM 与 Log Analytics 的连接仅适用于 SCOM 2012 SP1 和更高版本。

**问：如何确定代理能够与 Log Analytics 通信？**

答：要确保代理能够与 OMS 通信，请转到：控制面板、安全性和设置、**Microsoft 监视代理**。

在“**Azure Log Analytics (OMS)**”选项卡下，查找一个绿色复选标记。 绿色复选标记图标确认代理能够与 OMS 服务进行通信。

黄色警告图标意味着代理与 OMS 存在通信问题。 一个常见原因是 Microsoft 监视代理服务已停止，需要重新启动。

**问：如何停止代理与 Log Analytics 进行通信？**

答：在 SCOM 中，从 OMS 托管列表中删除该计算机。 这将停止通过 SCOM 为该代理执行的所有通信。 对于直接连接到 OMS 的代理，可以通过以下路径停止其与 OMS 进行通信：控制面板、安全性和设置、**Microsoft 监视代理**。
在“**Azure Log Analytics (OMS)**”选项卡下，删除列出的所有工作区。

**问：为什么我在在尝试将工作区从一个 Azure 订阅移动到另一个订阅时收到错误？**

答：添加解决方案时，Azure 会在工作区所处的 Azure 订阅中创建资源。

通常，添加订阅的人员是 Azure 订阅的管理员或参与者。 如果用户在 Azure 门户中不拥有用于 Azure 订阅的相同权限，则 OMS 门户中的管理员或参与者是不够的。


## <a name="agent-data"></a>代理数据
**问：可以通过代理向 Log Analytics 发送多少数据？每个客户的数据量是否有最大限制？**  
A. 免费套餐设置的上限是每个工作区每天 500 MB。 标准和高级套餐对上载的数据量没有限制。 作为云服务，OMS 中的 Log Analytics 设计为自动向上扩展以处理来自客户的数据 – 即使每天数据量达到数万亿字节 (TB)。

Log Analytics 代理设计为确保占用较小的数据空间并且会进行一些基本的数据压缩。 我们的一位客户实际上写了一篇博客描述对我们代理进行的测试，测试结果给人的印象非常深刻。 数据量取决于客户启用的解决方案。 在 OMS 概览页的“**使用情况**”磁贴下可以找到有关数据量的详细信息以及按解决方案列出的数据量划分。

有关更多信息，你可以阅读[客户博客](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html)以了解 OMS 代理的低数据占用空间。

**问：将数据发送到 Log Analytics 时，Microsoft 管理代理 (MMA) 使用多少网络带宽？**

A. 带宽是已发送数据量的一个函数。 数据通过网络发送时会被压缩。

**问：每个代理发送多少数据？**

A. 这很大程度上取决于：

* 你启用的解决方案
* 正在收集的日志和性能计数器的数量
* 日志中的数据量

一个好的方法就是利用免费定价层来搭载数台服务器并测量典型数据量。 总体使用情况显示在“**使用情况**”页。
对于能够运行 WireData 代理的计算机，你可以通过使用以下查询了解正在发送的数据量：

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>后续步骤
* [开始使用 Log Analytics](log-analytics-get-started.md)，以快速了解有关 Log Analytics 的更多信息并使 Log Analytics 开始运行。


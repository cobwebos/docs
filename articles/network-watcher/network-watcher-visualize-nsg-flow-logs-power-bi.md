---
title: "使用 Power BI 可视化 Azure 网络安全组流日志 | Microsoft 文档"
description: "本页介绍如何使用 Power BI 可视化 NSG 流日志。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f32c8cd6e32e99bc34d62435bcd1883ba957e716
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>使用 Power BI 可视化网络安全组流日志

使用网络安全组流日志可以查看有关网络安全组中入口和出口 IP 流量的信息。 这些流日志基于每个规则显示出站和入站流、流所适用的 NIC、有关流的 5 元组信息（源/目标 IP、源/目标端口、协议），以及是允许还是拒绝流量。

手动搜索日志文件可能难以洞察流日志数据。 本文提供一种用来可视化最新流日志和了解网络流量的解决方案。

## <a name="scenario"></a>方案

在以下方案中，我们会将 Power BI Desktop 连接到已配置为 NSG 流日志数据接收器的存储帐户。 连接到存储帐户后，Power BI 将下载并分析日志，提供网络安全组记录的流量的可视化形式。

使用模板中提供的视觉对象，可以检查：

* 最活跃的通信方
* 按方向和规则决策列出的时序流数据
* 通过网络接口 MAC 地址列出的流
* 按 NSG 和规则列出的流
* 按目标端口列出的流

提供的模板可编辑，因此可以对它进行修改以添加新数据或视觉对象，或者根据需要编辑查询。

## <a name="setup"></a>设置

在开始之前，必须在帐户中的一个或多个网络安全组上启用网络安全组流日志记录。 有关启用网络安全流日志的说明，请参阅以下文章：[Introduction to flow logging for Network Security Groups](network-watcher-nsg-flow-logging-overview.md)（网络安全组流日志记录简介）。

此外，必须在计算机上安装 Power BI Desktop 客户端，并在计算机上提供足够的可用空间用于下载和加载存储帐户中的日志数据。

![Visio 图示][1]

### <a name="steps"></a>步骤

1. 在 Power BI Desktop 应用程序中下载并打开以下 Power BI 模板：[网络观察程序 PowerBI 流日志模板](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. 输入所需的查询参数
    1. **StorageAccountName** – 指定包含所要加载和可视化的 NSG 流日志的存储帐户的名称。
    1. **NumberOfLogFiles** – 指定要在 Power BI 中下载和可视化的日志文件数。 例如，如果指定 50，则会下载 50 个最新的日志文件。 如果将 2 个 NSG 启用并配置为向此帐户发送 NSG 流日志，则可以查看过去 25 小时的日志。

    ![Power BI 主界面][2]

1. 输入存储帐户的访问密钥。 在 Azure 门户中导航到存储帐户，然后从“设置”菜单中选择“访问密钥”，即可找到有效的访问密钥。 单击“连接”，并应用更改。

    ![访问密钥][3]

    ![访问密钥 2][4]

4.  现已下载并分析日志，接下来可以利用预先创建的视觉对象。

## <a name="understanding-the-visuals"></a>了解视觉对象

模板中提供的视觉对象集有助于发挥 NSG 流日志数据的作用。 下图显示了已填充数据的仪表板的外观示例。 下面更详细介绍了每个视觉对象 

![powerbi][5]
 
“最活动的通信方”视觉对象显示在指定的时间段内发起连接最多的 IP。 框的大小对应于相对连接数。 

![toptalkers][6]

以下时序图显示该时间段内的流数。 图表的上半部分已根据流的方向分段，下半部分已根据所做的决策（允许或拒绝）分段。 使用此视觉对象可检查各个时间的流量趋势，以及探查流量或流量分段的任何异常高峰或下落。

![flowsoverperiod][7]

下图显示了每个网络接口的流，上半部分已根据流的方向分段，下半部分已根据所做的决策分段。 使用此信息可以洞察哪些 VM 相对于其他 VM 通信最活跃，以及发往特定 VM 的流量是受到允许还是遭到拒绝。

![flowspernic][8]

以下圆环图按目标端口显示了流的细分情况。 使用此信息可以查看在指定的时间段内最经常使用的目标端口。

![donut][9]

以下条形图按 NSG 和规则显示流。 使用此信息可以查看负责处理大多数流量的 NSG，以及 NSG 中按规则列出的流量细分。

![barchart][10]
 
以下参考图表显示有关日志中的 NSG 的信息、在该时间段内捕获的流数，以及捕获的最旧日志的日期。 使用此信息可以大致了解正在记录哪些 NSG，以及流的日期范围。

![infochart1][11]

![infochart2][12]

此模板包含以下筛选器，方便用户查看他们只想要了解的数据。 可以根据资源组、NSG 和规则筛选。 还可以根据 5 元组信息、决策以及日志写入时间筛选。

![slicers][13]

## <a name="conclusion"></a>结束语

本方案表明，我们可以使用网络观察程序和 Power BI 提供的网络安全组流日志来可视化和了解流量。 使用提供的模板，Power BI 可以直接从存储中下载日志并在本地对其进行处理。 加载模板所需的时间根据请求的文件数和下载文件的大小而异。

可以根据需要任意自定义此模板。 在 Power BI 中使用网络安全组流日志的方式多种多样。 

## <a name="notes"></a>说明

* 日志默认存储在 `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/` 中

    * 如果另一目录中存在其他数据，必须修改用于提取和处理这些数据的查询。

* 不建议使用提供的模板来处理 1 GB 以上的日志。

* 如果日志量很大，我们建议考虑使用其他数据存储解决方案，例如 Data Lake 或 SQL Server。

## <a name="next-steps"></a>后续步骤

访问 [Visualize network traffic patterns to and from your VMs using open source tools](network-watcher-using-open-source-tools.md)（使用开源工具可视化传入和传出 VM 的网络流量模式），了解如何使用 Elastick Stack 来可视化 NSG 流日志

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png

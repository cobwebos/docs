---
title: "使用 Azure 网络观察程序管理网络安全组流日志 | Microsoft Docs"
description: "此页说明如何在 Azure 网络观察程序中管理网络安全组流日志"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 41cb5ffab9bd3a3bed75ffdb6a7383ca1690f810
ms.contentlocale: zh-cn
ms.lasthandoff: 06/02/2017

---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>在 Azure 门户中管理网络安全组流日志

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

网络安全组流日志是网络观察程序的一项功能，可用于查看有关通过网络安全组的入口和出口 IP 流量的信息。 这些流日志采用 JSON 格式编写，提供以下重要信息： 

- 根据规则显示出站和入站流。
- 该流所适用的 NIC。
- 有关流（源/目标 IP、源/目标端口和协议）的 5 元组信息。
- 有关允许或拒绝流的信息。

## <a name="before-you-begin"></a>开始之前

此方案假定你已按照[创建网络观察程序实例](network-watcher-create.md)中的步骤进行操作。 此方案还假定包含有效虚拟机的资源组已存在。

## <a name="register-insights-provider"></a>注册 Insights 提供程序

要使流日志记录正常工作，必须注册 Microsoft.Insights 提供程序。 若要注册提供程序，请采用以下步骤： 

1. 转到“订阅”，然后选择要启用流日志的订阅。 
2. 在“订阅”边栏选项卡上，选择“资源提供程序”。 
3. 查看提供程序列表，确认是否已注册 microsoft.insights 提供程序。 若未注册，请选择“注册”。

![查看提供程序][providers]

## <a name="enable-flow-logs"></a>启用流日志

这些步骤演示了完成对网络安全组启用流日志的过程。

### <a name="step-1"></a>步骤 1

转到网络观察程序实例并选择“NSG 流日志”。

![流日志概述][1]

### <a name="step-2"></a>步骤 2

从列表中选择网络安全组。

![流日志概述][2]

### <a name="step-3"></a>步骤 3 

在“流日志设置”边栏选项卡上，将状态设置为“打开”，然后配置存储帐户。  完成后，请选择“确定”。 再选择“保存”。

![流日志概述][3]

## <a name="download-flow-logs"></a>下载流日志

将流日志保存在存储帐户中。 下载流日志进行查看。

### <a name="step-1"></a>步骤 1

若要下载流日志，请选择“可从配置的存储帐户下载流日志”。 此步骤会转到存储帐户视图，在该视图中可以选择要下载的日志。

![流日志设置][4]

### <a name="step-2"></a>步骤 2

转到正确的存储帐户。 然后选择“容器” > “insights-log-networksecuritygroupflowevent”。

![流日志设置][5]

### <a name="step-3"></a>步骤 3

转到流日志的位置，将其选中，然后选择“下载”。

![流日志设置][6]

有关日志结构的信息，请访问[网络安全组流日志概述](network-watcher-nsg-flow-logging-overview.md)。

## <a name="next-steps"></a>后续步骤

了解如何[使用 PowerBI 直观地显示 NSG 流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)。

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png


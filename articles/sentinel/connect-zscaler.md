---
title: 将 Zscaler 数据连接到 Azure 哨兵*微软文档
description: 了解如何将 Zscaler 数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587985"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>将 Zscaler 互联网接入连接到 Azure 哨兵

> [!IMPORTANT]
> Azure Sentinel 中的 Zscaler 数据连接器当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何将 Zscaler Internet Access 设备连接到 Azure Sentinel。 Zscaler 数据连接器允许您使用 Azure Sentinel 轻松连接 Zscaler Internet Access （ZIA） 日志，以查看仪表板、创建自定义警报并改进调查。 在 Azure Sentinel 上使用 Zscaler 将为您提供更多有关组织 Internet 使用情况的见解，并将增强其安全操作功能。 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>配置 Zscaler 以发送 CEF 消息

1. 在 Zscaler 设备上，您需要设置这些值，以便设备根据日志分析代理以必要的格式向 Azure Sentinel Syslog 代理发送必要的日志。 您可以在设备中修改这些参数，只要在 Azure Sentinel 代理上的 Syslog 守护进程中也修改它们。
    - 协议 = TCP
    - 端口 = 514
    - 格式 = CEF
    - IP 地址 - 请确保将 CEF 消息发送到专用于此目的的虚拟机的 IP 地址。
 有关详细信息，请参阅[Zscaler 和 Azure 哨兵部署指南](https://aka.ms/ZscalerCEFInstructions)。
 
   > [!NOTE]
   > 此解决方案支持 Syslog RFC 3164 或 RFC 5424。


1. 要在日志分析中为 CEF 事件使用相关架构，请`CommonSecurityLog`搜索 。
1. 继续[执行步骤 3：验证连接](connect-cef-verify.md)。


## <a name="next-steps"></a>后续步骤
在本文中，您学习了如何将 Zscaler Internet 访问连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



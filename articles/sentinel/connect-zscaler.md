---
title: 将 Zscaler 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Zscaler 数据连接到 Azure Sentinel。
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587985"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>连接 Zscaler Internet 访问 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Zscaler 数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍了如何将 Zscaler Internet 访问设备连接到 Azure Sentinel。 使用 Zscaler 数据连接器，可以轻松地将 Zscaler Internet Access （ZIA）日志与 Azure Sentinel 连接，查看仪表板，创建自定义警报，并改善调查。 使用 Azure 上的 Zscaler 可以更深入地了解组织的 Internet 使用情况，并增强其安全性操作功能。 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>配置 Zscaler 以发送 CEF 消息

1. 在 Zscaler 设备上，需要设置这些值，以便设备根据 Log Analytics 代理将必要的格式的必要日志发送到 Azure Sentinel Syslog 代理。 你可以在设备中修改这些参数，只要你还在 Azure Sentinel 代理上的 Syslog 后台程序中修改它们即可。
    - 协议 = TCP
    - 端口 = 514
    - Format = CEF
    - IP 地址-请确保将 CEF 消息发送到专用于此目的的虚拟机的 IP 地址。
 有关详细信息，请参阅[Zscaler 和 Azure Sentinel 部署指南](https://aka.ms/ZscalerCEFInstructions)。
 
   > [!NOTE]
   > 此解决方案支持 Syslog RFC 3164 或 RFC 5424。


1. 若要在 CEF 事件的 Log Analytics 中使用相关架构，请搜索 `CommonSecurityLog`。
1. 继续执行[步骤3：验证连接性](connect-cef-verify.md)。


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Zscaler Internet 访问连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



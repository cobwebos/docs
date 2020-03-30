---
title: 配置安全解决方案，将 CEF 数据连接到 Azure 哨兵预览*微软文档
description: 了解如何配置安全解决方案以将 CEF 数据连接到 Azure Sentinel。
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
ms.openlocfilehash: bdb76954b1db8135d8a36d6658bb7fff274ac126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588444"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>第 2 步：配置安全解决方案以发送 CEF 消息

在此步骤中，您将对安全解决方案本身执行必要的配置更改，以便将日志发送到 CEF 代理。

## <a name="configure-a-solution-with-a-connector"></a>使用连接器配置解决方案

如果安全解决方案已具有现有连接器，请使用特定于连接器的说明，如下所示：

- [检查点](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>配置任何其他解决方案
如果特定安全解决方案不存在连接器，请使用以下通用说明将日志转发到 CEF 代理。

1. 有关如何配置解决方案以发送 CEF 消息的步骤，请转到特定配置文章。 如果未列出解决方案，则需要在设备上设置这些值，以便设备根据日志分析代理以必要的格式向 Azure Sentinel Syslog 代理发送必要的日志。 您可以在设备中修改这些参数，只要在 Azure Sentinel 代理上的 Syslog 守护进程中也修改它们。
    - 协议 = TCP
    - 端口 = 514
    - 格式 = CEF
    - IP 地址 - 请确保将 CEF 消息发送到专用于此目的的虚拟机的 IP 地址。

   > [!NOTE]
   > 此解决方案支持 Syslog RFC 3164 或 RFC 5424。


1. 要在日志分析中为 CEF 事件使用相关架构，请`CommonSecurityLog`搜索 。

1. 继续执行步骤 3：[验证连接](connect-cef-verify.md)。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。


---
title: 配置安全解决方案以将 CEF 数据连接到 Azure Sentinel Preview |Microsoft Docs
description: 了解如何配置安全解决方案以将 CEF 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 2513638be6f895598f93758d8d8dbdf04c9561e9
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75615354"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>步骤2：配置安全解决方案以发送 CEF 消息

在此步骤中，你将对安全解决方案本身执行必要的配置更改，以将日志发送到 CEF 代理。

## <a name="configure-a-solution-with-a-connector"></a>使用连接器配置解决方案

如果你的安全解决方案已经具有现有连接器，请使用以下连接器特定的说明：

- [Check Point](connect-checkpoint.md)
- [Ios](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>配置任何其他解决方案
如果特定安全解决方案没有连接器，请使用以下一般说明将日志转发到 CEF 代理。

1. 请参阅特定配置一文，了解有关如何配置解决方案以发送 CEF 消息的步骤。 如果未列出你的解决方案，则在设备上需要设置这些值，以便设备根据 Log Analytics 代理将必要的格式的必要日志发送到 Azure Sentinel Syslog 代理。 你可以在设备中修改这些参数，只要你还在 Azure Sentinel 代理上的 Syslog 后台程序中修改它们即可。
    - 协议 = TCP
    - 端口 = 514
    - Format = CEF
    - IP 地址-请确保将 CEF 消息发送到专用于此目的的虚拟机的 IP 地址。

   > [!NOTE]
   > 此解决方案支持 Syslog RFC 3164 或 RFC 5424。


1. 若要在 CEF 事件的 Log Analytics 中使用相关架构，请搜索 `CommonSecurityLog`。

1. 继续执行步骤3：[验证连接性](connect-cef-verify.md)。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。


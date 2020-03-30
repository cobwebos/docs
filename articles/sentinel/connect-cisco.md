---
title: 将思科数据连接到 Azure 哨兵*微软文档
description: 了解如何将思科数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588393"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>将 Cisco ASA 连接到 Azure 哨兵



本文介绍如何将 Cisco ASA 设备连接到 Azure Sentinel。 Cisco ASA 数据连接器允许您轻松地将 Cisco ASA 日志与 Azure Sentinel 连接，查看仪表板、创建自定义警报并改进调查。 在 Azure Sentinel 上使用 Cisco ASA 将为您提供更多了解组织 Internet 使用情况的见解，并将增强其安全操作功能。 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>将 Cisco ASA 日志转发到系统代理

Cisco ASA 不支持 CEF，因此日志以 Syslog 方式发送，Azure Sentinel 代理知道如何解析它们，就像它们是 CEF 日志一样。 配置 Cisco ASA 可通过 Syslog 代理将 Syslog 消息转发到 Azure 工作区：

1. 转到[将 Syslog 消息发送到外部 Syslog 服务器](https://aka.ms/asi-syslog-cisco-forwarding)，然后按照说明设置连接。 当出现以下提示时，请使用以下参数：
    - 将**端口**设置为 514 或您在代理中设置的端口。
    - **syslog_ip**集到代理的 IP 地址。

1. 要在日志分析中为思科事件使用相关架构，请搜索`CommonSecurityLog`。

1. 继续[执行步骤 3：验证连接](connect-cef-verify.md)。




## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Cisco ASA 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



---
title: 将 Cisco 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Cisco 数据连接到 Azure Sentinel。
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588393"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>将 Cisco ASA 连接到 Azure Sentinel



本文介绍如何将 Cisco ASA 设备连接到 Azure Sentinel。 Cisco ASA 数据连接器可让你轻松地将 Cisco ASA 日志连接到 Azure Sentinel，查看仪表板、创建自定义警报和改进调查。 使用 Azure 上的 Cisco ASA 可以更深入地了解组织的 Internet 使用情况，并增强其安全性操作功能。 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>将 Cisco ASA 日志转发到 Syslog 代理

Cisco ASA 不支持 CEF，因此会以 Syslog 的形式发送日志，Azure Sentinel 代理知道如何分析它们，就好像它们是 CEF 日志一样。 将 Cisco ASA 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure 工作区：

1. 请参阅将[Syslog 消息发送到外部 Syslog 服务器](https://aka.ms/asi-syslog-cisco-forwarding)，并按照说明设置连接。 在出现提示时使用以下参数：
    - 将 "**端口**" 设置为 "514" 或在代理中设置的端口。
    - 将**syslog_ip**设置为代理的 ip 地址。

1. 若要在 Cisco 事件 Log Analytics 中使用相关架构，请搜索 `CommonSecurityLog`。

1. 继续执行[步骤3：验证连接性](connect-cef-verify.md)。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Cisco ASA 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



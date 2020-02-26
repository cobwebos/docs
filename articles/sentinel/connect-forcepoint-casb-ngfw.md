---
title: 将 Forcepoint 产品连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Forcepoint 产品连接到 Azure Sentinel。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588223"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>将 Forcepoint 产品连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Forcepoint products 数据连接器目前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


本文介绍了如何将 Forcepoint 产品连接到 Azure Sentinel。 

Forcepoint 数据连接器允许使用 Azure Sentinel 连接 Forcepoint Cloud Access Security Broker 和 Forcepoint 下一代防火墙日志。 通过这种方式，可以将用户定义的日志实时地自动导出到 Azure Sentinel。 连接器可让你深入了解 Forcepoint 产品记录的用户活动。 它还可让你进一步关联 Azure 工作负荷中的数据和其他源，并改进了 Azure Sentinel 内的工作簿的监视功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>将 Forcepoint 产品日志转发到 Syslog 代理 

配置 Forcepoint 产品以通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 按照以下安装指南中所述，将 Forcepoint 产品设置为 Azure Sentinel 集成：
 - [Forcepoint CASB 集成指南](https://frcpnt.com/casb-sentinel)
 - [Forcepoint NGFW 集成指南](https://frcpnt.com/ngfw-sentinel)

2. 搜索 "CommonSecurityLog" 以在 Log Analytics 中使用相关架构，其中 DeviceVendor 名称包含 "Forcepoint"。 

3. 继续执行[步骤3：验证连接性](connect-cef-verify.md)。



## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Forcepoint 产品连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。

- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
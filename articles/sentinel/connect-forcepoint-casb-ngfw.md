---
title: 将强制点产品连接到 Azure 哨兵*微软文档
description: 了解如何将 Forcepoint 产品连接到 Azure 哨兵。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588223"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>将强制点产品连接到 Azure 哨兵

> [!IMPORTANT]
> Azure Sentinel 中的 Forcepoint 产品数据连接器当前处于公共预览版中。 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


本文介绍如何将 Forcepoint 产品连接到 Azure Sentinel。 

Forcepoint 数据连接器允许您将 Forcepoint 云访问安全代理和强制点下一代防火墙日志与 Azure Sentinel 连接。 通过这种方式，您可以自动将用户定义的日志实时导出到 Azure Sentinel 中。 该连接器使您能够丰富对 Forcepoint 产品记录的用户活动的可见性。 它还支持与 Azure 工作负荷和其他源中的数据进一步关联，并改进 Azure Sentinel 内工作手册的监视功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>将强制点产品日志转发到 Syslog 代理 

将强制点产品配置为通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 按照以下安装指南中所述，将 Forcepoint 产品设置为 Azure Sentinel 集成：
 - [力点 CASB 集成指南](https://frcpnt.com/casb-sentinel)
 - [力点 NGFW 集成指南](https://frcpnt.com/ngfw-sentinel)

2. 搜索通用安全日志以使用与设备供应商名称的日志分析中的相关架构包含"强制点"。 

3. 继续[执行步骤 3：验证连接](connect-cef-verify.md)。



## <a name="next-steps"></a>后续步骤

在本文档中，您学习了如何将 Forcepoint 产品连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。

- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
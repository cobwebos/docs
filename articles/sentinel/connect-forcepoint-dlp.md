---
title: 将力点 DLP 连接到 Azure 哨兵*微软文档
description: 了解如何将力点 DLP 连接到 Azure 哨兵。
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
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588240"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>将强制点 DLP 连接到 Azure 哨兵

> [!IMPORTANT]
> Azure Sentinel 中的强制点数据丢失防护 （DLP） 数据连接器当前处于公共预览版中。 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Forcepoint DLP 连接器允许您自动将 DLP 事件数据导出到 Azure Sentinel 中。 您可以使用它来了解用户活动和数据丢失事件。 它还支持与 Azure 工作负荷和其他源中的数据关联，并改进 Azure Sentinel 内工作手册的监视功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-forcepoint-dlp"></a>配置和连接强制点 DLP

配置强制点 DLP 以 JSON 格式将事件数据通过 REST API 转发到 Azure 工作区，如[Forcepoint DLP 集成指南](https://frcpnt.com/dlp-sentinel)中所述。


## <a name="find-your-data"></a>查找您的数据

设置 Forcepoint DLP 连接器后，数据将显示在自定义日志**ForcepointDLPEvents_CL**下的日志分析中。


要在"强制点 DLP 的日志分析"中使用相关架构，请搜索**ForcepointDLPEvents_CL**。


## <a name="validate-connectivity"></a>验证连接

可能需要 20 分钟以上，直到日志开始出现在日志分析中。

## <a name="next-steps"></a>后续步骤

在本文档中，您学习了如何将力点 DLP 连接到 Azure 哨兵。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
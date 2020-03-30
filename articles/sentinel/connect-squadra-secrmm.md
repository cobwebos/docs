---
title: 将 Squadra 技术秒RMM 数据连接到 Azure 哨兵*微软文档
description: 了解如何将 Squadra 技术秒RMM 数据连接到 Azure 哨兵。
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
ms.openlocfilehash: d904e51321870fb4b61a237c23e425034b76dc0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588104"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>将小队技术秒RMM 数据连接到 Azure 哨兵 

> [!IMPORTANT]
> Azure Sentinel 中的 Squadra 技术安全可移动媒体管理器 （secRMM） 数据连接器当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


Squadra 技术 secRMM 连接器允许您轻松地将 Squadra 技术 secRMM 安全解决方案日志与 Azure Sentinel 连接。 它允许您查看仪表板、创建自定义警报和改进调查。 此连接器让您深入了解 USB 可移动存储事件。 Squadra 技术 secRMM 和 Azure 哨兵之间的集成利用了 REST API。


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>配置和连接 Squadra 技术 secRMM 

Squadra 技术 secRMM 可以直接将日志集成并导出到 Azure Sentinel。
1. 在 Azure Sentinel 门户中，单击"数据连接器"并选择"Squadra 技术秒RMM"，然后选择"打开连接器"页。

2. 按照[Azure 哨兵的"Squadra 技术入职指南](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)"中概述的步骤，在 Azure Sentinel 中获取 Squadra secRMM 数据。   


## <a name="find-your-data"></a>查找您的数据

成功建立连接后，数据将显示在自定义日志secRMM_CL下的日志分析中。
要在"小队技术"秒RMM的日志分析中使用相关架构，请搜索secRMM_CL。

## <a name="validate-connectivity"></a>验证连接
可能需要 20 分钟以上，直到日志开始出现在日志分析中。 


## <a name="next-steps"></a>后续步骤
在本文中，您学习了如何将 Squadra 技术 secRMM 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


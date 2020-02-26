---
title: 将 Squadra 技术 secRMM 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Squadra 技术 secRMM 数据连接到 Azure Sentinel。
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588104"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>将 Squadra 技术 secRMM 数据连接到 Azure Sentinel 

> [!IMPORTANT]
> Azure Sentinel 中的 Squadra 技术安全可移动媒体管理器（secRMM）数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


Squadra 技术 secRMM 连接器可让你轻松地将 Squadra 技术 secRMM 安全解决方案日志与 Azure Sentinel 连接起来。 它允许你查看仪表板、创建自定义警报和改进调查。 此连接器可让你深入了解 USB 可移动存储事件。 Squadra 技术 secRMM 和 Azure Sentinel 之间的集成利用 REST API。


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>配置并连接 Squadra 技术 secRMM 

Squadra 技术 secRMM 可以直接将日志集成到 Azure Sentinel 并将其导出。
1. 在 Azure Sentinel 门户中，单击 "数据连接器"，然后选择 "Squadra 技术 secRMM"，然后单击 "连接器" 页。

2. 按照[Azure sentinel 的 Squadra 技术载入指南](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf)中所述的步骤，在 azure sentinel 中获取 Squadra secRMM 数据。   


## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "CustomLogs" 下的 "Log Analytics" secRMM_CL 下。
若要在 Squadra 技术 secRMM 的 Log Analytics 中使用相关架构，请搜索 secRMM_CL。

## <a name="validate-connectivity"></a>验证连接
可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Squadra 技术 secRMM 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


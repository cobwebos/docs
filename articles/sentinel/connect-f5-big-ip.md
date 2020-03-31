---
title: 将 F5 大 IP 数据连接到 Azure 哨兵*微软文档
description: 了解如何将 F5 BIG-IP 数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: ae361c74b261bdd6a5673040f868392282b573ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588274"
---
# <a name="connect-your-f5-big-ip-appliance"></a>连接您的 F5 大 IP 设备 

> [!IMPORTANT]
> Azure Sentinel 中的 F5 BIG-IP 数据连接器当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

F5 BIG-IP 连接器允许您使用 Azure Sentinel 轻松连接所有 F5 BIG-IP 日志，查看工作簿、创建自定义警报并改进调查。 这使您可以更深入地了解组织的网络，并改进您的安全操作功能。 F5 BIG-IP 和 Azure 哨兵之间的集成利用了 REST API。


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-f5-big-ip"></a>配置和连接 F5 BIG-IP 

F5 BIG-IP 可以直接集成日志并将其导出到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 **"数据连接器**"并选择**F5 BIG-IP，** 然后**选择"打开连接器"页**。 
1. 要连接 F5 BIG-IP，您必须将 JSON 声明发布到系统的 API 终结点。 有关如何执行此操作的说明，请参阅[将 F5 BIG-IP 与 Azure Sentinel 集成](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)。
8. 从 F5 BIG-IP 连接器页面，复制工作区 ID 和主密钥，然后按照流数据指示将其粘贴[到 Azure 日志分析](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)。
1. 完成 F5 BIG-IP 说明后，在 Azure Sentinel 连接器页中，您将看到已连接的数据类型。
1. 要在日志分析中为 F5 BIG-IP 事件使用相关架构，请搜索**F5Telemetry_LTM_CL、F5Telemetry_system_CL**和**F5Telemetry_ASM_CL**。 **F5Telemetry_LTM_CL**


## <a name="validate-connectivity"></a>验证连接

可能需要 20 分钟以上，直到日志开始出现在日志分析中。 



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 F5 BIG-IP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



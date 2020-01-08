---
title: 将 F5 大 IP 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 F5 大 IP 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 273bea7fa8629dd907c228416d06dac087b0ec85
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610567"
---
# <a name="connect-your-f5-big-ip-appliance"></a>连接 F5 大 IP 设备 

> [!IMPORTANT]
> Azure Sentinel 中的 F5 大 IP 数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

F5 大 IP 连接器可让你轻松地将所有 F5 大 IP 日志连接到 Azure Sentinel，查看工作簿，创建自定义警报，以及改进调查。 这样，你就可以更深入地了解组织的网络并改善安全操作功能。 F5 大 IP 和 Azure Sentinel 之间的集成利用 REST API。


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-f5-big-ip"></a>配置并连接 F5 大 IP 

F5 大 IP 可以将日志直接集成到 Azure Sentinel 并将其导出。

1. 在 Azure Sentinel 门户中，单击 "**数据连接器**"，并依次选择 " **F5 大 IP** " 和 "**打开连接器**"。 
1. 若要连接 F5 大 IP，必须将 JSON 声明发送到系统的 API 终结点。 有关如何执行此操作的说明，请参阅[将 F5 大 IP 与 Azure Sentinel 集成](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)。
8. 在 "F5 大 IP 连接器" 页面上，复制 "工作区 ID" 和 "主密钥"，并按照 "[将数据流式传输到 Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)中的说明进行粘贴。
1. 完成 F5 大 IP 说明后，请在 Azure Sentinel 连接器页中看到连接的数据类型。
1. 若要在 F5 大 IP 事件 Log Analytics 中使用相关架构，请搜索**F5Telemetry_LTM_CL**、 **F5Telemetry_system_CL**和**F5Telemetry_ASM_CL**。


## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 F5 大 IP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



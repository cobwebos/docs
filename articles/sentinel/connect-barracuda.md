---
title: 将梭子鱼数据连接到 Azure 哨兵*微软文档
description: 了解如何将梭子鱼数据连接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 4e87bb57e6bdfea6307a166383da9dea187eea4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588478"
---
# <a name="connect-your-barracuda-appliance"></a>连接梭子鱼产品 



梭鱼 Web 应用程序防火墙 （WAF） 连接器允许您轻松地将梭子鱼日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 这使您可以更深入地了解组织的网络，并改进您的安全操作功能。 Azure Sentinel 利用**梭子鱼和**日志分析代理之间的本机集成来提供无缝集成。 


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-barracuda-waf"></a>配置和连接梭子鱼 WAF
梭子鱼 Web 应用程序防火墙可以通过日志分析代理将日志直接集成并导出到 Azure Sentinel。
1. 转到[梭子鱼 WAF 配置流](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，然后按照说明设置连接，使用这些参数：
    - **工作区 ID**：从 Azure 哨兵梭子鱼连接器页复制工作区 ID 的值。
    - **主键**：从 Azure Sentinel 梭子鱼连接器页复制主键的值。
1. 要在梭子鱼事件的日志分析中使用相关架构，请搜索**通用安全日志**和**barracuda_CL**。


## <a name="validate-connectivity"></a>验证连接

可能需要 20 分钟以上，直到日志开始出现在日志分析中。 



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将梭子鱼设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



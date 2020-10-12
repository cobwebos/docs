---
title: 将 Barracuda 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Barracuda Web 应用程序防火墙 (WAF) 连接器通过 Azure Sentinel 连接 Barracuda 日志。
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
ms.openlocfilehash: f28243be6b4f998e8bdea0ce02da3ceedde23e02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85559115"
---
# <a name="connect-your-barracuda-appliance"></a>连接 Barracuda 设备 



通过 Barracuda Web 应用程序防火墙 (WAF) 连接器，可以轻松地通过 Azure Sentinel 连接 Barracuda 日志、查看仪表板、创建自定义警报和改进调查。 这样，你就可以更深入地了解组织的网络并改善安全操作功能。 Azure Sentinel 充分利用 **Barracuda** 与 Log Analytics 代理之间的本机集成，提供无缝集成。 


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-barracuda-waf"></a>配置并连接 Barracuda WAF
Barracuda Web 应用程序防火墙可通过 Log Analytics 代理直接将日志集成到 Azure Sentinel 并将其导出。
1. 请参阅 [BARRACUDA WAF configuration flow](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，并按照说明使用以下参数设置连接：
    - **工作区 id**：从 Azure Sentinel Barracuda connector 页面复制工作区 id 的值。
    - **主键**：从 Azure Sentinel Barracuda connector 页面复制主密钥的值。
1. 若要在 Barracuda 事件的 Log Analytics 中使用相关架构，请搜索 **CommonSecurityLog** 和 **barracuda_CL**。


## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 



## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Barracuda 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。



---
title: 收集 Azure Sentinel 预览版中的 Barracuda 数据 |Microsoft Docs
description: 了解如何收集在 Azure Sentinel Barracuda 数据。
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 453af401f3b6120345cbe489fbfc323eca464ade
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58092270"
---
# <a name="connect-your-barracuda-appliance"></a>连接 Barracuda 设备 

> [!IMPORTANT]
> Azure Sentinel 目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Barracuda Web 应用程序防火墙 (WAF) 连接器，可轻松地连接到 Barracuda 日志在 Azure Sentinel，若要查看的仪表板、 创建自定义警报和改进的调查。 这为您提供了更详细地了解您组织的网络，并提高你的安全操作功能。 利用之间的本机集成 azure Sentinel **Barracuda**和 Microsoft Azure OMS 提供无缝集成。 


> [!NOTE]
> 
> - 数据将存储在其运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-barracuda-waf"></a>配置和连接 Barracuda WAF
Barracuda Web 应用程序防火墙可以集成并导出到 [ASI] 直接通过 Azure OMS 服务器的日志。
1. 转到[Barracuda WAF 配置流](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/)，然后按照说明进行设置的连接，使用以下参数：
    - **工作区 ID**： 从 Azure Sentinel Barracuda 连接器页中复制的工作区 ID 值。
    - **主键**： 从 Azure Sentinel Barracuda 连接器页面中复制的主要密钥值。
2. 在 Azure Sentinel 门户中，转到在其部署 Azure Sentinel 工作区并选择省略号 （...） 末尾的行并选择**高级设置**。 
1. 选择**数据**，然后**Syslog**。
1. 请确保在 Barracuda 中设置的功能存在并设置严重性，然后单击**保存**。


## <a name="validate-connectivity"></a>验证连接

它可能需要 1-2 20 分钟，直到你的日志开始在 Log Analytics 中显示。 



## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何连接到 Azure Sentinel Barracuda 设备。 若要了解有关 Azure Sentinel 的详细信息，请参阅以下文章：
- 了解如何[来了解一下你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[检测威胁 Azure Sentinel](tutorial-detect-threats.md)。


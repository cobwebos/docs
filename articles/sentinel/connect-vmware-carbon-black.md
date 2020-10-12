---
title: 将 VMware 碳黑色云终结点标准数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 VMware 碳黑色云终结点标准数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096304"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>通过 Azure Function 将 VMware 碳黑色云终结点标准连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 VMware 碳黑色云终结点标准数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

VMware 碳黑色云终结点标准连接器可让你轻松地将所有 [VMware 碳黑色端点标准](https://www.carbonblack.com/products/endpoint-standard/) 安全解决方案日志连接到 Azure Sentinel，以查看仪表板、创建自定义警报和改进调查。 VMware 碳黑色与 Azure Sentinel 之间的集成利用 Azure Functions 使用 REST API 请求日志数据。


> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>配置和连接 VMware 碳黑色云终结点标准版

Azure Functions 可以直接从 VMware 碳黑色云终结点标准集成并请求事件和日志，并将其转发到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，然后选择 " **VMware 碳黑色** 连接器"。
2. 选择 " **打开连接器" 页面**。
3. 按照 **VMware 碳黑色** 页面上的说明进行操作。


## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "Log Analytics" 下的 " **CarbonBlackAuditLogs_CL**"、" **CarbonBlackNotifications_CL** " 和 "CarbonBlackEvents_CL" 表中。

## <a name="validate-connectivity"></a>验证连接
可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 


## <a name="next-steps"></a>后续步骤
本文档介绍了如何使用 Azure Function App 将 VMware 碳黑色云终结点标准连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


---
title: 将 Orca 安全警报连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Orca 安全警报数据连接到 Azure Sentinel、查看仪表板、创建自定义警报和改进调查。
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
ms.openlocfilehash: 735f4b447d7258a9b444f3b75a6537dec20b1307
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076268"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>将 Orca 安全警报连接到 Azure Sentinel 

> [!IMPORTANT]
> Azure Sentinel 中的 Orca 安全警报连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Orca 安全警报连接器可让你轻松地将你的 [Orca 警报](https://orca.security/) 到 Azure Sentinel 中，以便你可以在工作簿中查看它们，并使用它们来创建自定义警报，并将它们结合起来以改进调查。 Orca 安全警报与 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-orca-security-alerts"></a>配置和连接 Orca 安全警报

Orca 安全警报可以将日志直接集成到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，然后选择 " **Orca 安全警报** "，然后单击 " **连接器" 页**。

2. 请参阅 https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration 以从 Orca 平台完成集成。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **OrcaAlerts_CL** " 表中 " **CustomLogs** " 下的 Log Analytics 中。
若要为 Orca 警报使用 Log Analytics 中的相关架构，请搜索 `OrcaAlerts_CL` 。

## <a name="validate-connectivity"></a>验证连接
可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Orca 安全警报连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


---
title: 将 Qualys 漏洞管理数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Qualys 漏洞管理数据连接到 Azure Sentinel。
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
ms.openlocfilehash: 44002a8f4ab3b644e3530ee2d2fc06a7af271fbe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531033"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>将 Qualys VM 与 azure Function 连接到 Azure Sentinel

Qualys 漏洞管理（VM）连接器可让你轻松地将所有[QUALYS VM](https://www.qualys.com/apps/vulnerability-management/)安全解决方案日志与 Azure Sentinel 连接起来，查看仪表板，创建自定义警报，并改善调查。 Qualys VM 与 Azure Sentinel 之间的集成利用 Azure Functions 来使用 REST API 请求日志数据。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-qualys-vm"></a>配置并连接 Qualys VM

Azure Functions 可以直接从 Qualys VM 集成并请求事件和日志，并将其转发到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 "**数据连接器**"，并选择 " **Qualys 漏洞管理**连接器"。

1. 选择 "**打开连接器" 页面**。

1. 按照 " **Qualys 漏洞管理**" 页上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **QualysHostDetection_CL** " 表下 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Azure Function App 将 Qualys VM 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

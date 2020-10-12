---
title: 连接 Proofpoint on demand 目标攻击防护 (将) 数据点击 Azure Sentinel |Microsoft Docs
description: 了解如何连接 Proofpoint on demand 目标攻击防护 (将) 数据连接到 Azure Sentinel。
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
ms.openlocfilehash: df4292a40335e58c64b8ebc50ffb6ac1ceee8836
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87059896"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>通过 Azure Function 将 Proofpoint on demand 点击连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Proofpoint on demand 点击数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Proofpoint on demand 目标攻击防护 (点击) 连接器可让你轻松地将 [proofpoint on demand](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) 安全解决方案日志与 Azure Sentinel 连接起来，查看仪表板、创建自定义警报，并改善调查。 Proofpoint on demand 分流和 Azure Sentinel 之间的集成利用 Azure Functions 来使用 REST API 请求日志数据。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-proofpoint-tap"></a>配置并连接 Proofpoint on demand 分流

Azure Functions 可以直接从 Proofpoint on demand 中集成并请求事件和日志，并将其转发到 Azure Sentinel。

1. 在 Azure Sentinel 门户中，单击 " **数据连接器** "，然后选择 " **proofpoint on demand** "。

1. 选择 " **打开连接器" 页面**。

1. 按照 **PROOFPOINT ON DEMAND 点击** 页面上的说明进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **ProofpointTAPMessagesBlocked_CL**"、" **ProofpointTAPMessagesDelivered_CL**"、" **ProofpointTAPClicksPermitted_CL** " 和 " **ProofpointTAPClicksBlocked_CL** " 表下 Log Analytics 中。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Azure Function App 将 Proofpoint on demand 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

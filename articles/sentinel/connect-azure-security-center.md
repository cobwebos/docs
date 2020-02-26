---
title: 将 Azure 安全中心数据连接到 Azure Sentinel
description: 了解如何将 Azure 安全中心数据连接到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588529"
---
# <a name="connect-data-from-azure-security-center"></a>连接 Azure 安全中心的数据





可以通过 azure Sentinel 连接[Azure 安全中心](../security-center/security-center-intro.md)的警报，并将其流式传输到 azure Sentinel。 

## <a name="prerequisites"></a>必备条件

- 若要从 Azure 安全中心导出警报，你必须在你所流式传输的日志的订阅中具有安全读者角色。

- 必须在订阅上运行[Azure 安全中心标准层](../security-center/security-center-pricing.md)。 否则，请[将订阅升级到 standard](https://azure.microsoft.com/pricing/details/security-center/)。



## <a name="connect-to-azure-security-center"></a>连接到 Azure 安全中心

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 " **Azure 安全中心**" 磁贴。

1. 在右侧，单击要流式传输到 Azure Sentinel 的每个订阅旁边的 "**连接**"。 请确保将每个订阅升级到 Azure 安全中心标准层，以将警报流式传输到 Azure Sentinel。

1. 你可以选择是否希望 Azure 安全中心的警报自动在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”，以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。

3. 单击“连接”。

4. 若要在 Azure 安全中心警报的 Log Analytics 中使用相关架构，请搜索**SecurityAlert**。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 安全中心连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

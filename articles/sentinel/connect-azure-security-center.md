---
title: 将 Azure 安全中心数据连接到 Azure 哨兵
description: 了解如何将 Azure 安全中心数据连接到 Azure 哨兵。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: e6e24a97a8b98bdd5447295880811914100563fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588529"
---
# <a name="connect-data-from-azure-security-center"></a>连接 Azure 安全中心的数据





Azure 哨兵使您能够连接 Azure[安全中心的](../security-center/security-center-intro.md)警报并将其流式传输到 Azure Sentinel 中。 

## <a name="prerequisites"></a>先决条件

- 要从 Azure 安全中心导出警报，必须在流的日志订阅中具有安全读取器角色。

- 必须在订阅上运行[Azure 安全中心标准层](../security-center/security-center-pricing.md)。 如果没有，[请将订阅升级到标准](https://azure.microsoft.com/pricing/details/security-center/)。



## <a name="connect-to-azure-security-center"></a>连接到 Azure 安全中心

1. 在 Azure 哨兵中，选择**数据连接器**，然后单击**Azure 安全中心**磁贴。

1. 在右侧，单击要流式传输到 Azure Sentinel 的每个订阅旁边的 **"连接**"。 请确保将每个订阅升级到 Azure 安全中心标准层，以便将警报流式传输到 Azure Sentinel。

1. 您可以选择是否希望 Azure 安全中心的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”，******** 以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。********

3. 单击“连接”。

4. 要在日志分析中为 Azure 安全中心警报使用相关架构，请搜索**安全警报**。

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Azure 安全中心连接到 Azure 哨兵。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

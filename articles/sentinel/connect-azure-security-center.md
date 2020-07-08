---
title: 将 Azure 安全中心数据连接到 Azure Sentinel
description: 了解如何从 Azure 安全中心（ASC）标准层连接警报并将其流式传输到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 2fc7744600a9652ad43fd0aae8d886dc94acd58f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559146"
---
# <a name="connect-data-from-azure-security-center-asc"></a>连接 Azure 安全中心的数据（ASC）

可以通过 azure Sentinel 连接[Azure 安全中心](../security-center/security-center-intro.md)的警报，并将其流式传输到 azure Sentinel。 

## <a name="prerequisites"></a>先决条件

- 若要从 Azure 安全中心导出警报，你必须在你所流式传输的日志的订阅中具有安全读者角色。

- 必须在订阅上运行[Azure 安全中心标准层](../security-center/security-center-pricing.md)。 否则，请[将订阅升级到 standard](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="connect-to-azure-security-center"></a>连接到 Azure 安全中心

1. 在 Azure Sentinel 中，从导航菜单中选择 "**数据连接器**"。

1. 从 "数据连接器" 库中，选择 " **Azure 安全中心**"，然后单击 "**打开连接器" 页面**按钮。

1. 在 "**配置**" 下，单击要流式传输到 Azure Sentinel 的每个订阅旁边的 "**连接**"。 只有在具有所需权限和 ASC 标准层订阅时，"连接" 按钮才可用。

1. 你可以选择是否希望 Azure 安全中心的警报在 Azure Sentinel 中自动生成事件。 在 "**创建事件**" 下，选择 "**启用**" 以启用自动根据警报创建事件的默认分析规则。 然后，你可以在 "**分析**" 下的 "**活动规则**" 选项卡中编辑此规则。

1. 若要在 Azure 安全中心警报的 Log Analytics 中使用相关架构，请搜索**SecurityAlert**。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 安全中心连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

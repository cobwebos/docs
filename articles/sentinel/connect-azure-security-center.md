---
title: 将 Azure 安全中心数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Azure 安全中心数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: a9c210531f2c4cab1c3c023eab795023c3ad9f0c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240222"
---
# <a name="connect-data-from-azure-security-center"></a>连接 Azure 安全中心的数据





可以通过 azure Sentinel 连接[Azure 安全中心](../security-center/security-center-intro.md)的警报，并将其流式传输到 azure Sentinel。 

## <a name="prerequisites"></a>先决条件

- 如果要从 Azure 安全中心导出警报，你必须是订阅的参与者，该订阅的日志会进行流式处理。

- 必须在订阅上运行[Azure 安全中心标准层](../security-center/security-center-pricing.md)。 否则，请[将订阅升级到 standard](https://azure.microsoft.com/pricing/details/security-center/)。

- 必须以对要连接的每个订阅具有全局管理员或安全管理员权限的用户身份登录。


## <a name="connect-to-azure-security-center"></a>连接到 Azure 安全中心

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 " **Azure 安全中心**" 磁贴。

1. 在右侧，单击要流式传输到 Azure Sentinel 的每个订阅旁边的 "**连接**"。 请确保将每个订阅升级到 Azure 安全中心标准层，以将警报流式传输到 Azure Sentinel。

1. 你可以选择是否希望 Azure 安全中心的警报自动在 Azure Sentinel 中自动生成事件。 在 "**创建事件**" 下，选择 "**启用**" 以启用从连接的安全服务中生成的警报自动创建事件的默认分析规则。 然后，你可以在 "**分析**" 和 "**活动规则**" 下编辑此规则。

3. 单击“连接”。

4. 若要在 Azure 安全中心警报的 Log Analytics 中使用相关架构，请搜索**SecurityAlert**。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 安全中心连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

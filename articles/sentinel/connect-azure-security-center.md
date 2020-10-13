---
title: 将 Azure Defender 数据连接到 Azure Sentinel
description: 了解如何从 Azure 安全中心连接 Azure Defender 警报并将其流式传输到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: b1188e533039b0137cebb22652d9921418c41deb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89659652"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>从 Azure 安全中心连接 Azure Defender 警报数据

使用 Azure Defender 警报连接器从 [Azure 安全中心](../security-center/security-center-intro.md) 引入 azure defender 警报，并将其流式传输到 azure Sentinel。 

## <a name="prerequisites"></a>必备条件

- 你的用户必须在你所流式传输的日志的订阅中具有安全读者角色。

- 需要在 Azure 安全中心内启用 Azure Defender。  (标准层已不再存在，并且不再是许可证要求。 ) 

## <a name="connect-to-azure-defender"></a>连接到 Azure Defender

1. 在 Azure Sentinel 中，从导航菜单中选择 " **数据连接器** "。

1. 从 "数据连接器" 库中，选择 " **来自 ASC (的 Azure Defender 警报** 仍称为 Azure 安全中心) ，然后单击" **打开连接器页** "按钮。

1. 在 " **配置**" 下，单击要流式传输到 Azure Sentinel 的每个订阅旁边的 " **连接** "。 只有在您具有所需的权限时，"连接" 按钮才可用。

1. 你可以选择是否希望 Azure Defender 中的警报在 Azure Sentinel 中自动生成事件。 在 " **创建事件**" 下，选择 " **启用** " 以启用自动根据警报创建事件的默认分析规则。 然后，你可以在 " **分析**" 下的 "  **活动规则** " 选项卡中编辑此规则。

1. 若要在 Azure Defender 警报 Log Analytics 中使用相关架构，请搜索 **SecurityAlert**。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure Defender 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

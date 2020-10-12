---
title: 使用实体行为分析来检测高级威胁 |Microsoft Docs
description: 在 Azure Sentinel 中启用用户和实体行为分析，并配置数据源
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
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994611"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中 (UEBA) 启用用户和实体行为分析 



## <a name="prerequisites"></a>必备条件

- 必须为用户分配 Azure AD 中的 **全局管理员** 或 **安全管理员** 角色，才能启用或禁用 UEBA，但无法运行。

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>如何启用用户和实体行为分析

1. 在 Azure Sentinel 导航菜单中，选择 " **实体行为 (预览") **。

1. 在标题下，将 **其**切换到 **"打开**"。

1. 单击 " **选择数据源** " 按钮。

1. 在 " **数据源选择** " 窗格中，标记要启用 UEBA 的数据源旁边的复选框，然后选择 " **应用**"。

    > [!NOTE]
    >
    > 在 " **数据源选择** " 窗格的下半部分中，你将看到尚未启用的 UEBA 支持的数据源的列表。 
    >
    > 启用 UEBA 后，在连接新的数据源时，可以选择在 "数据连接器" 窗格中直接从 "数据连接器" 窗格中为 UEBA 启用它们。

1. 选择 " **前往实体搜索**"。 这会将你转到实体搜索窗格，从现在开始，你可以从 Azure 的主菜单中选择 **实体行为** 时看到这一点。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何启用和配置 Azure Sentinel (UEBA) 中的用户和实体行为分析。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

---
title: Azure Monitor 工作簿-移动区域
description: 如何将工作簿移到不同的区域
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d3b8bfbef544e754f684421daa847f1724435d53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875584"
---
# <a name="move-an-azure-workbook-to-another-region"></a>将 Azure 工作簿移到另一个区域

本文介绍如何将 Azure 工作簿资源移到不同的 Azure 区域。 由于各种原因，你可能需要将资源移动到其他区域。 例如，利用新的 Azure 区域，部署仅在特定区域中可用的功能或服务，满足内部策略和监管要求，或者满足容量规划要求。

## <a name="prerequisites"></a>必备条件

* 确保目标区域支持工作簿。

* 这些说明适用于共享工作簿 (`microsoft.insights/workbooks`) 和专用工作簿 (`microsoft.insights/myworkbooks`) 保存在 Azure Monitor 和大多数资源类型中。

  但是，对于专门链接到 Application Insights 资源类型的工作簿，这些工作簿存储在保存 Application Insights 资源的 Azure 区域中。

  不能将这些工作簿单独移动到另一个区域。

## <a name="move"></a>移动

移动 Azure 工作簿的最简单方法是在门户 UI 的 "工作簿" 工具中使用 "另存为"：

1. 在工作簿查看器中打开目标工作簿。
2. 使用 "编辑" 工具栏按钮进入编辑模式。
3. 使用 "另存为" 工具栏按钮。
4. 在保存窗体中，选择工作簿的名称和所需区域。 请确保 "订阅"、"资源组" 和 "共享" 的其他字段适用。

   > [!NOTE]
   > 可能需要为工作簿使用新的名称，以避免出现重复的名称。

5. 保存。 

## <a name="verify"></a>验证

使用 Azure 工作簿浏览 UI 查找新工作簿。 确保位置是目标位置。

## <a name="clean-up"></a>清除

在新区域中创建工作簿后，删除上一个区域中的原始工作簿。
1. 在工作簿查看器中打开原始工作簿。
2. 使用 "编辑" 工具栏按钮进入编辑模式。
3. 从 "编辑工具" 下拉 (铅笔图标) ，选择 "删除工作簿"。

如果重命名了工作簿以将其导入到新区域，则可以使用编辑模式工具栏的 "编辑工具" 下拉 "重命名" 项将工作簿重命名为以前的名称。

## <a name="next-steps"></a>后续步骤

需要移动工作簿模板而不是工作簿？ 请参阅如何 [将 Azure 工作簿模板移动到另一个区域](./workbook-templates-move-region.md)。

请参阅如何通过 ARM 模板 [部署工作簿和工作簿模板](./workbooks-automate.md) 。

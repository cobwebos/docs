---
title: Azure Monitor 工作簿模板-移动区域
description: 如何将工作簿模板移动到不同的区域
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: f37550d914cf7efb0c75ed3dfa8854e1ec7be7e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875583"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>将 Azure 工作簿模板移动到另一个区域

本文介绍如何将 Azure 工作簿模板资源移到不同的 Azure 区域。 由于各种原因，你可能需要将资源移动到其他区域。 例如，利用新的 Azure 区域，部署仅在特定区域中可用的功能或服务，满足内部策略和监管要求，或者满足容量规划要求。

当前没有用于创建工作簿模板资源的门户 UI，唯一唯一的方法是 [通过 Azure 资源管理器模板 (ARM 模板) 部署](./workbooks-automate.md)。 因此，移动模板的最简单方法是重复使用以前的 ARM 模板并将其更新为部署到新区域。

## <a name="prerequisites"></a>必备条件

* 确保工作簿模板在目标区域中受支持。

## <a name="prepare"></a>准备

* 确定工作簿模板的以前用过的 ARM 模板。

## <a name="move"></a>移动

1. 更新以前使用的模板以引用新区域。

   > [!NOTE]
   > 您可能需要为工作簿模板使用一个新的名称，以避免出现重复的名称。

2. 通过 ARM 模板部署部署更新的模板，以便在所需的区域中创建新的工作簿模板。

## <a name="verify"></a>验证

使用 Azure 工作簿 "浏览 UI" 查找新部署的工作簿模板。 确保位置是目标位置。

## <a name="clean-up"></a>清除

在新区域中创建工作簿模板之后，请删除上一个区域中的原始工作簿模板。
1. 在 Azure 工作簿中查找工作簿模板浏览 UI。
2. 选择要删除的工作簿模板。
3. 选择 "删除" 命令。

如果重命名了工作簿模板以便将其导入新区域，则可以使用 Azure 工作簿模板资源视图中的 "重命名" 命令将工作簿模板重命名为以前的名称。

## <a name="next-steps"></a>后续步骤

需要移动工作簿而不是模板？ 请参阅如何 [将 Azure 工作簿移到另一个区域](./workbooks-move-region.md)。


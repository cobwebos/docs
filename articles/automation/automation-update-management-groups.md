---
title: 在 Azure 更新管理中使用动态组
description: 描述动态组如何处理更新管理
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edc4384be0f1dc73f2e7e098114080d304d92ce8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377741"
---
# <a name="use-dynamic-groups-with-update-management"></a>结合使用动态组更新管理

更新管理提供了面向动态组 Azure 或非 Azure Vm 以实现更新部署的能力。 这些组在部署时评估，因此你无需编辑你的部署即可添加计算机。

## <a name="azure-machines"></a>Azure 计算机

这些组由查询进行定义，当更新部署开始时，将评估该组的成员。 动态组不适用于经典 Vm。 定义查询时，可以结合使用以下各项来填充动态组：

* Subscription
* 资源组
* 位置
* Tags

![选择组](./media/automation-update-management/select-groups.png)

若要预览动态组的结果，请单击“预览”按钮。 那时此预览将显示组成员身份，在此示例中，我们要搜索标记“Role”等于“BackendServer”的计算机。 如果多台计算机已添加此标记，会针对该组将它们添加到任何未来部署中。

![预览组](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 计算机

对于非 Azure 计算机，保存的搜索也称为 "计算机组" 用于创建动态组。 若要了解如何创建保存的搜索，请参阅[创建计算机组](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 创建组后，可以从已保存的搜索列表中选择它。 单击 "**预览**" 以在该时间预览已保存搜索中的计算机。

![选择组](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>后续步骤

创建动态组后，可以[创建更新部署](automation-tutorial-update-management.md)

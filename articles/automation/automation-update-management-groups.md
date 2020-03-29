---
title: 将动态组与 Azure 更新管理一起使用
description: 本文介绍动态组如何使用 Azure 自动化更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420401"
---
# <a name="use-dynamic-groups-with-update-management"></a>将动态组与更新管理配合使用

更新管理提供了针对更新部署的 Azure 或非 Azure VM 的动态组的能力。 这些组在部署时进行评估，因此您不必编辑部署来添加计算机。

## <a name="azure-machines"></a>Azure 计算机

这些组由查询进行定义，当更新部署开始时，将评估该组的成员。 动态组不适用于经典 VM。 定义查询时，可以使用以下项一起填充动态组：

* 订阅
* 资源组
* 位置
* Tags

![选择组](./media/automation-update-management/select-groups.png)

若要预览动态组的结果，请单击“预览”**** 按钮。 那时此预览将显示组成员身份，在此示例中，我们要搜索标记“Role”**** 等于“BackendServer”**** 的计算机。 如果多台计算机已添加此标记，会针对该组将它们添加到任何未来部署中。

![预览组](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 计算机

对于非 Azure 计算机，保存的搜索也称为计算机组，用于创建动态组。 要了解如何创建已保存的搜索，请参阅[创建计算机组](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 创建群组后，您可以从已保存的搜索列表中选择该组。 单击 **"预览**"以预览当时保存的搜索中的计算机。

![选择组](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>后续步骤

创建动态组后，可以[创建更新部署](automation-tutorial-update-management.md)

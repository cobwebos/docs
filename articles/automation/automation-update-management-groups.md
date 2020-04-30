---
title: 在 Azure 自动化中使用动态组更新管理
description: 本文介绍动态组如何使用 Azure 自动化更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617436"
---
# <a name="use-dynamic-groups-with-update-management"></a>将动态组与更新管理配合使用

更新管理允许你将 Azure 或非 Azure Vm 的动态组作为更新部署的目标。 这些由查询定义的组是在部署时计算的，因此您无需编辑部署来添加计算机。

## <a name="azure-machines"></a>Azure 计算机

动态组不适用于经典 Vm。 定义查询时，可以结合使用以下各项来填充动态组：

* 订阅
* 资源组
* 位置
* Tags

![选择组](./media/automation-update-management/select-groups.png)

若要预览动态组的结果，请单击 "**预览**"。 预览显示当前时间组成员身份。 在此示例中，我们将搜索组**BackendServer**的计算机`Role` 。 如果有更多计算机添加此标记，则会将其添加到针对该组的任何未来部署中。

![预览组](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 计算机

对于非 Azure 计算机，保存的搜索（也称为计算机组）用于创建动态组。 若要了解如何创建保存的搜索，请参阅[创建计算机组](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 创建组后，可以从已保存的搜索列表中选择它。 单击 "**预览**" 以在该时间预览已保存搜索中的计算机。

![选择组](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>后续步骤

创建动态组后，可以[创建更新部署](automation-tutorial-update-management.md)。

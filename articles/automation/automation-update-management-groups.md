---
title: 将动态组与 Azure 自动化更新管理一起使用
description: 本文介绍动态组如何使用 Azure 自动化更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617436"
---
# <a name="use-dynamic-groups-with-update-management"></a>将动态组与更新管理配合使用

更新管理允许您针对一组动态的 Azure 或非 Azure VM 进行更新部署。 这些组由查询定义，在部署时进行评估，因此您不必编辑部署来添加计算机。

## <a name="azure-machines"></a>Azure 计算机

动态组不适用于经典 VM。 定义查询时，可以使用以下项一起填充动态组：

* 订阅
* 资源组
* 位置
* Tags

![选择组](./media/automation-update-management/select-groups.png)

要预览动态组的结果，请单击 **"预览**"。 预览显示当前时间的组成员身份。 在此示例中，我们搜索具有组**BackendServer**的标记`Role`的计算机。 如果添加了更多计算机，则它们将添加到针对该组的任何将来部署中。

![预览组](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>非 Azure 计算机

对于非 Azure 计算机，保存的搜索（也称为计算机组）用于创建动态组。 要了解如何创建已保存的搜索，请参阅[创建计算机组](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 创建群组后，您可以从已保存的搜索列表中选择该组。 单击 **"预览**"以预览当时保存的搜索中的计算机。

![选择组](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>后续步骤

创建动态组后，可以[创建更新部署](automation-tutorial-update-management.md)。

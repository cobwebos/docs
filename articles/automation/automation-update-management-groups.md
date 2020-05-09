---
title: 在 Azure 自动化中使用动态组更新管理
description: 本文介绍动态组如何使用 Azure 自动化更新管理。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf4c156d2bf9c205bd7545a96b5314dd43b2d02c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690781"
---
# <a name="use-dynamic-groups-with-update-management"></a>将动态组与更新管理配合使用

更新管理允许你将 Azure 或非 Azure Vm 的动态组作为更新部署的目标。 使用动态组使你无需编辑你的部署即可更新计算机。

> [!NOTE]
> 动态组不适用于经典 Vm。

可以通过 Azure 门户中的**更新管理**为 azure 或非 Azure 计算机定义动态组。 请参阅[管理多个 Azure 虚拟机的更新](manage-update-multi.md)。

动态组由 Azure Automation 在部署时计算的查询定义。 即使动态组查询检索大量计算机，Azure Automation 一次最多只能处理1000台计算机。 请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management)。 

> [!NOTE]
> 如果你希望更新超过1000台计算机，我们建议你将更新拆分为多个更新计划。 

## <a name="define-dynamic-groups-for-azure-machines"></a>为 Azure 计算机定义动态组

为 Azure 计算机定义动态组查询时，可以使用以下项填充动态组：

* 订阅
* 资源组
* 位置
* Tags

![选择组](./media/automation-update-management/select-groups.png)

若要预览动态组查询的结果，请单击 "**预览**"。 预览显示当前时间组成员身份。 在此示例中，我们将搜索组**BackendServer**的计算机`Role` 。 如果有更多计算机添加此标记，则会将其添加到针对该组的任何未来部署中。

![预览组](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>为非 Azure 计算机定义动态组

非 Azure 计算机的动态组使用保存的搜索，也称为计算机组。 若要了解如何创建保存的搜索，请参阅[创建计算机组](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 创建保存的搜索后，可以从 Azure 门户中的 "**更新管理**" 中保存的搜索列表中选择它。 单击 "**预览**" 可预览已保存搜索中的计算机。

![选择组](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>后续步骤

创建动态组后，可以[创建更新部署](automation-tutorial-update-management.md)。

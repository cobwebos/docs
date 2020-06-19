---
title: 将动态组与 Azure 自动化更新管理配合使用
description: 本文介绍如何将动态组与 Azure 自动化更新管理配合使用。
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830933"
---
# <a name="use-dynamic-groups-with-update-management"></a>将动态组与更新管理配合使用

使用更新管理可定位 Azure VM 或非 Azure VM 的动态组以进行更新部署。 使用动态组可以在不编辑部署的情况下更新计算机。

> [!NOTE]
> 动态组不适用于经典 VM。

你可以通过 Azure 门户中的更新管理为 Azure 或非 Azure 计算机定义动态组。 请参阅[管理多个 Azure 虚拟机的更新](manage-update-multi.md)。

动态组由 Azure 自动化在部署时计算的查询定义。 即使动态组查询检索到大量计算机，Azure 自动化一次最多只能处理 1000 台计算机。 请参阅 [Azure 订阅和服务限制、配额和约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management)。 

> [!NOTE]
> 如果希望更新 1000 台以上计算机，我们建议将更新拆分为多个更新计划。 

## <a name="define-dynamic-groups-for-azure-machines"></a>为 Azure 计算机定义动态组

为 Azure 计算机定义动态组查询时，可以使用以下项填充动态组：

* 订阅
* 资源组
* 位置
* Tags

![选择组](./media/automation-update-management/select-groups.png)

若要预览动态组查询的结果，请单击“预览”。 预览将显示当前的组成员身份。 在本例中，我们将为组 BackendServer 搜索具有标记 `Role` 的计算机。 如果多台计算机已添加此标记，系统会针对该组将它们添加到任何未来部署中。

![预览组](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>为非 Azure 计算机定义动态组

非 Azure 计算机的动态组使用保存的搜索，也称为计算机组。 若要了解如何创建保存的搜索，请参阅[创建计算机组](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)。 创建保存的搜索后，可以从 Azure 门户“更新管理”中的保存的搜索列表中选择它。 单击“预览”，可预览保存的搜索中的计算机。

![选择组](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>后续步骤

若要使用更新管理，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。

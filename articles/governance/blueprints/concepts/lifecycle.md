---
title: 了解蓝图的生命周期
description: 了解蓝图的整个生命周期及每个阶段的详细信息。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 71a5233c6d5af21d7bc5430cda14e156e16d5cd4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58001567"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>了解 Azure 蓝图的生命周期

与 Azure 中的众多资源一样，Azure 蓝图中的蓝图也有一个典型的自然生命周期。 这些蓝图会被创建、部署，并在不再需要或相关时被删除。
蓝图支持标准的生命周期操作。 它将在这些操作的基础之上进行构建，以提供附加的状态级别，用于支持常见的持续集成和持续部署管道，供管理基础结构即代码（DevOps 中的一项关键要素）的组织使用。

为了让你完全了解蓝图及其各个阶段，我们将讲解标准生命周期：

> [!div class="checklist"]
> - 创建和编辑蓝图
> - 发布蓝图
> - 创建和编辑新版本的蓝图
> - 发布新版本的蓝图
> - 删除特定版本的蓝图
> - 删除蓝图

## <a name="creating-and-editing-a-blueprint"></a>创建和编辑蓝图

创建蓝图时，请向其添加项目、将其保存到管理组或订阅，并提供唯一名称和唯一版本。 目前，蓝图处于“草稿”模式，尚不可分配。 但在“草稿”模式下，仍可继续更新和更改此蓝图。

如果“草稿”模式下的某个蓝图从未发布过，则它在“蓝图定义”页面上显示的图标将与已发布的蓝图所显示的不同。 **最新版本**显示为**草稿**这些永远不会发布蓝图。

使用 [Azure 门户](../create-blueprint-portal.md#create-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#create-a-blueprint) 创建和编辑蓝图。

## <a name="publishing-a-blueprint"></a>发布蓝图

在对“草稿”模式下的蓝图进行所有计划的更改之后，此蓝图即可发布并可进行分配。 已发布的蓝图版本不可更改。
一旦发布，该蓝图显示的图标就与“草稿”蓝图的不同，并在“最新版本”列中显示所提供的版本号。

使用 [Azure 门户](../create-blueprint-portal.md#publish-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#publish-a-blueprint) 发布蓝图。

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>创建和编辑新版本的蓝图

**已发布的**蓝图版本不可更改。 但是，可向现有蓝图添加新版蓝图且可按需更改此新版本。 通过编辑对现有蓝图进行更改。 保存新更改时，蓝图将包含**未发布的更改**。 这些更改是蓝图的新**草稿**版本。

使用 [Azure 门户](../create-blueprint-portal.md#edit-a-blueprint)创建蓝图。

## <a name="publishing-a-new-version-of-the-blueprint"></a>发布新版本的蓝图

蓝图的每个编辑版本必须在**发布**之后才可分配。 当对蓝图进行未发布的更改，但它们尚未发布时，“发布蓝图”按钮在“编辑蓝图”页面上可用。 如果未显示该按钮，则表示蓝图已发布，但具有“未发布的更改”。

> [!NOTE]
> 一个蓝图可具有多个已发布的版本，每个版本都可分配到订阅。

若要发布包含**未发布更改**的蓝图，请使用发布新蓝图的相同步骤。

## <a name="deleting-a-specific-version-of-the-blueprint"></a>删除特定版本的蓝图

蓝图的每一个版本都是唯一对象，可单独发布。 因此，还可以删除蓝图的每个版本。 删除其中一个蓝图版本将不对该蓝图的其他版本造成任何影响。

> [!NOTE]
> 不能删除具有活动分配项的蓝图。 请先删除分配项，再删除要移除的版本。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 选择**蓝图定义**的页面上的左侧和使用筛选器的选项，可以找到你想要删除的版本的蓝图。 单击它以打开“编辑”页面。

1. 单击“已发布的版本”选项卡，找到要删除的版本。

1. 右键单击要删除和选择的版本**删除此版本**。

## <a name="deleting-the-blueprint"></a>删除蓝图

此外，还可删除核心蓝图。 删除核心蓝图也会删除该蓝图的任何蓝图版本，包括**草稿**和**已发布的**蓝图。 与删除蓝图版本一样，删除核心蓝图时不会删除任何蓝图版本的现有分配项。

> [!NOTE]
> 不能删除具有活动分配项的蓝图。 请先删除分配项，再删除要移除的版本。

使用 [Azure 门户](../create-blueprint-portal.md#delete-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#delete-a-blueprint) 删除蓝图。

## <a name="assignments"></a>分配

可在蓝图生命周期的多个时间点向订阅分配此蓝图。 当蓝图版本处于“已发布”模式时，可向订阅分配此版本。 在开发较新的版本期间，此生命周期使蓝图版本可供使用和主动分配。

由于蓝图的版本已分配，因此有必要了解其分配位置及其分配有的具体参数。 参数可以是静态的，也可以是动态的。 要了解详细信息，请参阅[静态和动态参数](parameters.md)。

### <a name="updating-assignments"></a>更新分配

分配蓝图时可更新分配。 众多原因导致要更新现有分配，其中包括：

- 添加或删除[资源锁定](resource-locking.md)
- 更改[动态参数](parameters.md#dynamic-parameters)的值
- 将分配升级到新发布的蓝图版本

要了解操作方式，请参阅[更新现有分配](../how-to/update-existing-assignments.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[静态和动态参数](parameters.md)。
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](resource-locking.md)。
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。
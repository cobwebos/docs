---
title: 了解 Azure 蓝图的生命周期
description: 了解蓝图的整个生命周期及每个阶段的详细信息。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: e0790168a8b9590aaa440a04cd99f26c2ece2818
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991533"
---
# <a name="understand-the-life-cycle-of-an-azure-blueprint"></a>了解 Azure 蓝图的生命周期

与 Azure 中的众多资源一样，Azure 蓝图中的蓝图也有一个典型的自然生命周期。 这些蓝图会被创建、部署，并在不再需要或相关时被删除。
蓝图支持传统的 CRUD（创建/读取/更新/删除）生命周期操作，但还在此基础之上提供其他级别的状态，支持常见的持续集成/持续部署 (CI/CD) 管道，供通过代码管理其基础结构的用户使用 - 这是 DevOps 中的一项关键元素，被称作基础结构即代码 (IaC)。

为了让你完全了解蓝图及其各个阶段，我们将讲解标准生命周期：

> [!div class="checklist"]
> - 创建和编辑蓝图
> - 发布蓝图
> - 创建和编辑新版本的蓝图
> - 发布新版本的蓝图
> - 删除特定版本的蓝图
> - 删除蓝图

## <a name="creating-and-editing-a-blueprint"></a>创建和编辑蓝图

创建蓝图时，请向其添加项目、将其保存到管理组，并提供唯一名称和唯一版本。 此时，蓝图处于“草稿”模式下，尚不可分配。 但在“草稿”模式下，仍可继续更新和更改此蓝图。

如果“草稿”模式下的某个蓝图从未发布过，则它在“蓝图定义”页面上显示的图标将与已发布的蓝图所显示的不同。 对于从未发布过的蓝图，最新版本也显示为“草稿”。

使用 [Azure 门户](../create-blueprint-portal.md#create-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#create-a-blueprint) 创建和编辑蓝图。

## <a name="publishing-a-blueprint"></a>发布蓝图

在对“草稿”模式下的蓝图进行所有必需更改之后，此蓝图即可发布并可进行分配。 已发布的蓝图版本不可更改。
一旦发布，该蓝图显示的图标就与“草稿”蓝图的不同，并在“最新版本”列中显示所提供的版本号。

使用 [Azure 门户](../create-blueprint-portal.md#publish-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#publish-a-blueprint) 发布蓝图。

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>创建和编辑新版本的蓝图

虽然已发布的蓝图版本不可更改，但可向现有蓝图添加新版本的蓝图且可按需更改此新版本。 操作方式是更改现有蓝图。 如果蓝图已发布，则在保存这些更改时，它们将在蓝图定义列表中显示为“未发布的更改”。 保存更改时将保存蓝图的草稿版本。

使用 [Azure 门户](../create-blueprint-portal.md#edit-a-blueprint)创建蓝图。

## <a name="publishing-a-new-version-of-the-blueprint"></a>发布新版本的蓝图

正如蓝图的第一个版本在发布后才可分配，同一蓝图的所有后续版本都必须在发布后才能进行分配。 当对蓝图进行未发布的更改，但它们尚未发布时，“发布蓝图”按钮在“编辑蓝图”页面上可用。 如果未显示该按钮，则表示蓝图已发布，但具有“未发布的更改”。

> [!NOTE]
> 一个蓝图可具有多个已发布的版本，每个版本都可分配到订阅。

将带有“未发布的更改”的蓝图发布为现有蓝图的新版本时，操作步骤与发布新蓝图的步骤相同。

## <a name="deleting-a-specific-version-of-the-blueprint"></a>删除特定版本的蓝图

蓝图的每一个版本都是唯一对象，可单独发布。 这还表示蓝图的每一个版本都可删除。 删除其中一个蓝图版本将不对该蓝图的其他版本造成任何影响。

> [!NOTE]
> 不能删除具有活动分配项的蓝图。 请先删除分配项，再删除要移除的版本。

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择左侧窗格中的“策略”，启动 Azure 蓝图服务。 在“策略”页上，单击“蓝图”。

1. 从左侧页面选择“蓝图定义”，再使用筛选器选项找到要删除其版本的蓝图。 单击它以打开“编辑”页面。

1. 单击“已发布的版本”选项卡，找到要删除的版本。

1. 右键单击要删除的版本，再选择“删除此版本”。

## <a name="deleting-the-blueprint"></a>删除蓝图

此外，还可删除核心蓝图。 无论蓝图是“草稿”还是“已发布”状态，删除核心蓝图时都将一并删除该蓝图的所有蓝图版本。 与删除蓝图版本一样，删除核心蓝图时不会删除任何蓝图版本的现有分配项。

> [!NOTE]
> 不能删除具有活动分配项的蓝图。 请先删除分配项，再删除要移除的版本。

使用 [Azure 门户](../create-blueprint-portal.md#delete-a-blueprint)或 [REST API](../create-blueprint-rest-api.md#delete-a-blueprint) 删除蓝图。

## <a name="assignments"></a>分配

可在蓝图生命周期的多个时间点向订阅分配此蓝图。
每当蓝图版本处于“已发布”模式下时，就可向订阅分配此版本。 即使存在蓝图的草稿版本，如果一个或多个蓝图版本处于“已发布”模式下，则其中每个“已发布”的版本仍可进行分配。 这样，在开发较新的版本期间，就可使用并主动分配蓝图的版本。

由于蓝图的版本已分配，因此有必要了解其分配位置及其分配有的具体参数。 参数可以是静态的，也可以是动态的。 要了解详细信息，请参阅[静态和动态参数](parameters.md)。

### <a name="updating-assignments"></a>更新分配

分配蓝图时可更新分配。 众多原因导致要更新现有分配，其中包括：

- 添加或删除[资源锁定](resource-locking.md)
- 更改[动态参数](parameters.md#dynamic-parameters)的值
- 将分配升级到新发布的蓝图版本

要了解操作方式，请参阅[更新现有分配](../how-to/update-existing-assignments.md)。

## <a name="next-steps"></a>后续步骤

- 了解如何使用[静态和动态参数](parameters.md)
- 了解如何自定义[蓝图排序顺序](sequencing-order.md)
- 了解如何使用[蓝图资源锁定](resource-locking.md)
- 了解如何[更新现有分配](../how-to/update-existing-assignments.md)
- 使用[常规疑难解答](../troubleshoot/general.md)在蓝图分配期间解决问题
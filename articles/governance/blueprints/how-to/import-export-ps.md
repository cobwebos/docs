---
title: 通过 PowerShell 导入和导出蓝图
description: 了解如何以代码的形式使用蓝图定义。 使用导出和导入命令进行共享、源控制和管理。
ms.date: 05/06/2020
ms.topic: how-to
ms.openlocfilehash: 7cc6bc241dc6b7b4baa669e64a0d5e43641a55b8
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864039"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>通过 PowerShell 导入和导出蓝图定义

可以通过 Azure 门户完全管理 Azure 蓝图。 随着组织开始使用 Azure 蓝图，它们应该开始将蓝图定义视为托管代码。 此概念通常称为 "基础结构即代码（IaC）"。 将蓝图定义视为代码，可提供除 Azure 门户提供的其他优势。 这些优势包括：

- 共享蓝图定义
- 备份蓝图定义
- 在不同租户或订阅中重复使用蓝图定义
- 在源代码管理中放置蓝图定义
  - 在测试环境中自动测试蓝图定义
  - 支持持续集成和持续部署（CI/CD）管道

无论出于何种原因，在代码中管理蓝图定义都有好处。 本文介绍如何在[Az](https://powershellgallery.com/packages/Az.Blueprint/)模块中`Import-AzBlueprintWithArtifact`使用`Export-AzBlueprintWithArtifact`和命令。

## <a name="prerequisites"></a>必备条件

本文假定使用 Azure 蓝图的中等实践知识。 如果尚未执行此操作，请完成以下文章：

- [在门户中创建蓝图](../create-blueprint-portal.md)
- 了解[部署阶段](../concepts/deployment-stages.md)和[蓝图生命周期](../concepts/lifecycle.md)
- 通过 PowerShell[创建](../create-blueprint-powershell.md)和[管理](./manage-assignments-ps.md)蓝图定义和分配

如果尚未安装，请按照[添加 Az.Blueprint 模块](./manage-assignments-ps.md#add-the-azblueprint-module)中的说明安装并验证 PowerShell 库中的 Az.Blueprint 模块  。

## <a name="folder-structure-of-a-blueprint-definition"></a>蓝图定义的文件夹结构

在查看导出和导入蓝图之前，让我们先来看看组成蓝图定义的文件是如何构建的。 蓝图定义应存储在其自己的文件夹中。

> [!IMPORTANT]
> 如果没有任何值传递到`Import-AzBlueprintWithArtifact` Cmdlet 的**Name**参数，则使用存储蓝图定义的文件夹的名称。

除了蓝图定义外，还必须命名`blueprint.json`蓝图定义的项目。 每个项目都必须位于名为`artifacts`的子文件夹中。
将蓝图定义的结构组合起来，因为文件夹中的 JSON 文件应如下所示：

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>导出蓝图定义

导出蓝图定义的步骤非常简单。 导出蓝图定义对于共享、备份或置于源代码管理中非常有用。

- **蓝图**[必需]
  - 指定蓝图定义
  - 用于`Get-AzBlueprint`获取 reference 对象
- **OutputPath** [必需]
  - 指定用于保存蓝图定义 JSON 文件的路径
  - 输出文件位于具有蓝图定义名称的子文件夹中
- **版本**（可选）
  - 如果**蓝图**引用对象包含对多个版本的引用，则指定要输出的版本。

1. 获取对蓝图定义的引用以从表示为`{subId}`的订阅导出：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. 使用`Export-AzBlueprintWithArtifact` cmdlet 导出指定的蓝图定义：

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>导入蓝图定义

如果已[导出蓝图定义](#export-your-blueprint-definition)，或者在[所需的文件夹结构](#folder-structure-of-a-blueprint-definition)中手动创建了蓝图定义，则可以将该蓝图定义导入到不同的管理组或订阅。

有关内置蓝图定义的示例，请参阅[Azure 蓝图 GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)存储库。

- **名称**[必需]
  - 指定新蓝图定义的名称
- **InputPath** [必需]
  - 指定从其创建蓝图定义的路径
  - 必须与[所需的文件夹结构](#folder-structure-of-a-blueprint-definition)相匹配
- **ManagementGroupId** （可选）
  - 在当前上下文默认情况下，用于保存蓝图定义的管理组 ID
  - 必须指定**ManagementGroupId**或**SubscriptionId**
- **SubscriptionId** （可选）
  - 如果不是当前上下文默认值，则为将蓝图定义保存到的订阅 ID
  - 必须指定**ManagementGroupId**或**SubscriptionId**

1. 使用`Import-AzBlueprintWithArtifact` cmdlet 导入指定的蓝图定义：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

导入蓝图定义后，请[将其分配给 PowerShell](./manage-assignments-ps.md#create-blueprint-assignments)。

有关创建高级蓝图定义的信息，请参阅以下文章：

- 使用[静态和动态参数](../concepts/parameters.md)。
- 自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 利用[蓝图资源锁定](../concepts/resource-locking.md)保护部署。
- 将[蓝图作为代码进行管理](https://github.com/Azure/azure-blueprints/blob/master/README.md)。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。
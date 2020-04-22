---
title: 使用 PowerShell 导入和导出蓝图
description: 了解如何将蓝图定义用作代码。 使用导出和导入命令共享、源代码管理并管理它们。
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: dcdf48f8941198591b39d6cf89ec5e6dac7ba94c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686837"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>使用 PowerShell 的导入和导出蓝图定义

Azure 蓝图可以通过 Azure 门户完全管理。 随着组织在 Azure 蓝图的使用方面取得进展，他们应该开始将蓝图定义视为托管代码。 这个概念通常被称为基础结构作为代码 （IaC）。 将蓝图定义视为代码提供了 Azure 门户提供的其他优势。 这些优势包括：

- 共享蓝图定义
- 备份蓝图定义
- 重用不同租户或订阅中的蓝图定义
- 将蓝图定义置于源代码管理中
  - 测试环境中蓝图定义的自动测试
  - 支持持续集成和持续部署 （CI/CD） 管道

无论您的理由是什么，将蓝图定义管理为代码都有其好处。 本文演示如何在[Az.Blueprint](https://powershellgallery.com/packages/Az.Blueprint/) `Export-AzBlueprintWithArtifact`模块中使用`Import-AzBlueprintWithArtifact`和 命令。

## <a name="prerequisites"></a>先决条件

本文假定 Azure 蓝图具有中等的工作知识。 如果您尚未这样做，则执行以下文章：

- [在门户中创建蓝图](../create-blueprint-portal.md)
- 阅读有关[部署阶段](../concepts/deployment-stages.md)和[蓝图生命周期](../concepts/lifecycle.md)
- 使用 PowerShell[创建](../create-blueprint-powershell.md)[和管理](./manage-assignments-ps.md)蓝图定义和分配

如果尚未安装，请按照[添加 Az.Blueprint 模块](./manage-assignments-ps.md#add-the-azblueprint-module)中的说明安装并验证 PowerShell 库中的 Az.Blueprint 模块  。

## <a name="folder-structure-of-a-blueprint-definition"></a>蓝图定义的文件夹结构

在查看导出和导入蓝图之前，我们来看看构成蓝图定义的文件的构造方式。 蓝图定义应存储在其自己的文件夹中。

> [!IMPORTANT]
> 如果未将任何值传递给`Import-AzBlueprintWithArtifact`cmdlet 的名称参数，则将使用蓝图定义中存储的文件夹的名称。 **Name**

与必须命名的`blueprint.json`蓝图定义一起，是蓝图定义由工件组成的。 每个项目必须位于名为`artifacts`的子文件夹中。
综合起来，蓝图定义的结构作为文件夹中的 JSON 文件应如下所示：

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

导出蓝图定义的步骤非常简单。 导出蓝图定义可用于共享、备份或放入源代码管理。

- **蓝图**[必需]
  - 指定蓝图定义
  - 用于`Get-AzBlueprint`获取引用对象
- **输出路径**[必需]
  - 指定将蓝图定义 JSON 文件保存到
  - 输出文件位于具有蓝图定义名称的子文件夹中
- **版本**（可选）
  - 如果**蓝图**引用对象包含对多个版本的引用，则指定要输出的版本。

1. 获取对蓝图定义的引用，以便从表示为`{subId}`的订阅导出：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. 使用`Export-AzBlueprintWithArtifact`cmdlet 导出指定的蓝图定义：

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>导入蓝图定义

在[所需的文件夹结构](#folder-structure-of-a-blueprint-definition)中具有[导出的蓝图定义](#export-your-blueprint-definition)或手动创建的蓝图定义后，可以将该蓝图定义导入到不同的管理组或订阅。

有关内置蓝图定义的示例，请参阅 Azure 蓝图[GitHub 存储库](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins)。

- **名称**[必需]
  - 指定新蓝图定义的名称
- **输入路径**[必需]
  - 指定从
  - 必须匹配[所需的文件夹结构](#folder-structure-of-a-blueprint-definition)
- **管理组 Id（** 可选）
  - 管理组 ID，用于将蓝图定义保存到当前上下文默认值（如果不是当前上下文默认）
  - 必须指定**管理组 Id**或**订阅 Id**
- **订阅 Id（** 可选）
  - 将蓝图定义保存到当前上下文默认值（如果不是当前上下文默认值）的订阅 ID
  - 必须指定**管理组 Id**或**订阅 Id**

1. 使用`Import-AzBlueprintWithArtifact`cmdlet 导入指定的蓝图定义：

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

导入蓝图定义后，使用[PowerShell 分配它](./manage-assignments-ps.md#create-blueprint-assignments)。

有关创建高级蓝图定义的信息，请参阅以下文章：

- 使用[静态和动态参数](../concepts/parameters.md)。
- 自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 使用[蓝图资源锁定](../concepts/resource-locking.md)来保护部署。
- [将蓝图管理为代码](https://github.com/Azure/azure-blueprints/blob/master/README.md)。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../concepts/resource-locking.md)。
- 使用[一般故障排除](../troubleshoot/general.md)在蓝图的分配期间解决问题。
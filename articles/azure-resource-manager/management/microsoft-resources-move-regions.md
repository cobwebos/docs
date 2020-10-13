---
title: 移动 Microsoft 资源中资源的区域
description: 演示如何将位于 Microsoft .Resources 命名空间中的资源移动到新区域。
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 098e5c36969da12966d793b6e1eddd4f79701ead
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88951049"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>将 Microsoft .Resources 资源移动到新区域

可能需要将现有资源移动到新区域。 本文介绍如何移动两个资源类型-templateSpecs 和 deploymentScripts，这些资源类型位于 Microsoft .Resources 命名空间中。

## <a name="move-template-specs-to-new-region"></a>将模板规范移动到新区域

如果一个区域中有一个 [模板规范](../templates/template-specs.md) ，并且想要将其移动到新区域，则可以导出该模板规范并重新部署它。

1. 使用命令导出现有的模板规范。对于参数值，请提供与要导出的模板规范匹配的值。

   对于 Azure PowerShell，请使用：

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   对于 Azure CLI，请使用：

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. 使用导出的模板规范创建新的模板规范。下面的示例显示 `westus` 了新区域，但可以提供所需的区域。

   对于 Azure PowerShell，请使用：

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   对于 Azure CLI，请使用：

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>将部署脚本移动到新区域

1. 选择包含要移动到新区域的部署脚本的资源组。

1. [导出模板](../templates/export-template-portal.md)。 导出时，选择部署脚本和任何其他必需的资源。

1. 在导出的模板中，删除以下属性：

   * tenantId
   * principalId
   * clientId

1. 导出的模板中包含部署脚本区域的硬编码值。

   ```json
   "location": "westus2",
   ```

   将模板更改为允许参数设置位置。 有关详细信息，请参阅 [在 ARM 模板中设置资源位置](../templates/resource-location.md)

   ```json
   "location": "[parameters('location')]",
   ```

1. [部署导出的模板](../templates/deploy-powershell.md) ，并为部署脚本指定新区域。

## <a name="next-steps"></a>后续步骤

* 若要了解如何将资源移动到新的资源组或订阅，请参阅 [将资源移到新的资源组或订阅](move-resource-group-and-subscription.md)。
* 若要了解如何将资源移到新的区域，请参阅 [跨区域移动 Azure 资源](move-region.md)。

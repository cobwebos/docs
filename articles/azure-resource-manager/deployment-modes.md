---
title: Azure 资源管理器部署模式 | Microsoft Docs
description: 介绍如何使用 Azure 资源管理器指定是使用完整部署模式还是增量部署模式。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: c4347254df59c62085b2bfb195496bf479cf7b35
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2018
ms.locfileid: "51344567"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure 资源管理器部署模式

部署资源时，可以指定部署为增量更新还是完整更新。  这两种模式的主要区别是资源管理器如何处理资源组中不在模板中的现有资源。 默认模式为增量模式。

## <a name="incremental-and-complete-deployments"></a>增量部署和完整部署

资源部署时：

* 在完整模式下，资源管理器删除资源组中已存在但尚未在模板中指定的资源。
* 在增量模式下，资源管理器保留资源组中已存在但尚未在模板中指定的未更改资源。

对于这两种模式，资源管理器都会尝试创建模板中指定的所有资源。 如果资源已存在于资源组中且其设置未更改，该操作不会导致任何更改。 如果更改资源的属性值，则使用这些新值更新资源。 如果尝试更新现有资源的位置或类型，则部署会失败并出现错误。 请改用所需的位置或类型部署新资源。

以增量模式重新部署资源时，请指定资源的所有属性值，而不仅仅是要更新的属性值。 如果未指定某些属性，资源管理器会将更新解释为覆盖这些值。

## <a name="example-result"></a>示例结果

为了说明增量模式和完整模式的差异，请考虑以下方案。

**资源组**包含：

* 资源 A
* 资源 B
* 资源 C

**模板**包含：

* 资源 A
* 资源 B
* 资源 D

在“增量”模式下部署时，资源组具有：

* 资源 A
* 资源 B
* 资源 C
* 资源 D

在“完整”模式下部署时，会删除资源 C。 资源组具有：

* 资源 A
* 资源 B
* 资源 D

## <a name="set-deployment-mode"></a>设置部署模式

在使用 PowerShell 部署时若要设置部署模式，请使用 `Mode` 参数。

```azurepowershell-interactive
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

在使用 Azure CLI 部署时若要设置部署模式，请使用 `mode` 参数。

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

使用[链接或嵌套模板](resource-group-linked-templates.md)时，必须将 `mode` 属性设为 `Incremental`。 仅根级别模板支持完整部署模式。

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建资源管理器模板，请参阅[创作 Azure 资源管理器模板](resource-group-authoring-templates.md)。
* 若要了解如何部署资源，请参阅[使用 Azure 资源管理器模板部署应用程序](resource-group-template-deploy.md)。
* 若要查看资源提供程序的操作，请参阅 [Azure REST API](/rest/api/)。

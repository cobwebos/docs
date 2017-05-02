---
title: "使用 PowerShell 和模板部署资源 | Microsoft Docs"
description: "使用 Azure Resource Manager 和 Azure PowerShell 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: f5119fef1fb0a316b4109ccbc4433f8c9a18d128
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>使用 Resource Manager 模板和 Azure PowerShell 部署资源

本主题介绍如何将 Azure PowerShell 与 Resource Manager 模板配合使用向 Azure 部署资源。 你的模板可以是本地文件或是可通过 URI 访问的外部文件。

可以从[创建你的第一个 Azure Resource Manager 模板](resource-manager-create-first-template.md#final-template)一文中获取这些示例中使用的模板 (storage.json)。 若要将该模板用于这些示例，请创建一个 JSON 文件并添加复制的内容。

## <a name="deploy-local-template"></a>部署本地模板
若要快速开始部署，请使用以下 cmdlet 来部署具有内联参数的本地模板。 

```powershell
Login-AzureRmAccount
 
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息：

```powershell
ProvisioningState       : Succeeded
```

前面的示例在默认订阅中创建了资源组。 若要使用其他订阅，请在登录后添加 [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext) cmdlet。

## <a name="deploy-external-template"></a>部署外部模板

若要部署外部模板，请使用 **TemplateUri** 参数。 模板可以在任何可公开访问的 URI（如存储帐户中的文件）中。

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -storageNamePrefix contoso -storageSKU Standard_GRS
```

可以通过要求使用共享访问签名 (SAS) 令牌进行访问来保护模板。 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)。

## <a name="parameter-files"></a>参数文件

前面的示例介绍了如何将参数作为内联值传递。 可以在文件中指定参数值，然后在部署过程中传递该文件。 

参数文件必须采用以下格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageNamePrefix": {
         "value": "contoso"
     },
     "storageSKU": {
         "value": "Standard_GRS"
     }
  }
}
```

若要传递本地参数文件，请使用 **TemplateParameterFile** 参数：

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要传递外部参数文件，请使用 **TemplateParameterUri** 参数：

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json `
  -TemplateParameterUri https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.parameters.json
```

可以在同一部署操作中使用内联参数和本地参数文件。 例如，可以在本地参数文件中指定某些值，并在部署期间添加其他内联值。 如果同时为本地参数文件中的参数和内联参数提供值，则内联值优先。

但是，使用外部参数文件时，不能传递是内联值或来自本地文件的其他值。 如果在 **TemplateParameterUri** 参数中指定参数文件，则将忽略所有内联参数。 提供外部文件中的所有参数值。 如果模板包括参数文件中无法包括的敏感值，可将该值添加到密钥保管库，或者以内联方式动态提供所有参数值。

如果模板包括的一个参数与 PowerShell 命令中的某个参数同名，PowerShell 使用后缀 **FromTemplate** 显示模板的参数。 例如，模板中名为 **ResourceGroupName** 的参数与 [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.3.0/new-azurermresourcegroupdeployment) cmdlet 中的 **ResourceGroupName** 参数冲突。 系统会提示提供 **ResourceGroupNameFromTemplate** 的值。 通常，为避免此类混乱，不应按部署操作所用的参数名称命令参数。

## <a name="test-a-deployment"></a>测试部署

若要测试模板和参数值而不实际部署任何资源，请使用 [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment)。 无论使用本地文件还是远程文件，它的选项都完全相同。

```powershell
Test-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageNamePrefix contoso -storageSKU Standard_GRS
```

## <a name="log-deployment-content-for-debugging"></a>记录要调试的部署内容

有关部署操作的信息自动记录在活动日志中。 但是，若要记录可能帮助排查任何部署错误的有关部署的其他信息，请使用 **DeploymentDebugLogLevel** 参数。 你可以指定在对部署操作进行日志记录时记录请求内容或/和响应内容。
   
```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -DeploymentDebugLogLevel All `
  -ResourceGroupName ExampleGroup -TemplateFile storage.json
```

有关查看日志的信息，请参阅[查看活动日志以审核对资源的操作](resource-group-audit.md)。

## <a name="export-resource-manager-template"></a>导出 Resource Manager 模板
对于现有的资源组（通过 PowerShell 或门户等其他方法部署），可以查看资源组的 Resource Manager 模板。 导出模板有两个好处：

1. 由于模板中定义了所有基础结构，因此将来可以轻松地自动完成解决方案的部署。
2. 可以查看代表解决方案的 JavaScript 对象表示法 (JSON)，以此熟悉模板语法。

若要查看资源组的模板，请运行 [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) cmdlet。

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleResourceGroup
```

有关详细信息，请参阅[从现有资源导出 Azure Resource Manager 模板](resource-manager-export-template.md)。


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

若要使用完整模式，请使用 `Mode` 参数：

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```


## <a name="next-steps"></a>后续步骤
* 有关用于部署模板的完整示例脚本，请参阅 [Resource Manager 模板部署脚本](resource-manager-samples-powershell-deploy.md)。
* 若要在模板中定义参数，请参阅[创作模板](resource-group-authoring-templates.md#parameters)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。



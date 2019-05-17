---
title: 使用 PowerShell 和模板部署资源 | Microsoft Docs
description: 使用 Azure Resource Manager 和 Azure PowerShell 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: 5203519b1553de54d4e3cd1fafe6fb3d1c18ebd6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65779962"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>使用 Resource Manager 模板和 Azure PowerShell 部署资源

了解如何将 Azure PowerShell 与 资源管理器模板配合使用，以向 Azure 部署资源。 有关部署和管理 Azure 解决方案的概念的详细信息，请参阅 [Azure 资源管理器概述](resource-group-overview.md)。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为 Azure 订阅或订阅中的资源组。 大多数情况下，我们会将以资源组指定为部署目标。 可以使用订阅部署在整个订阅中应用策略和角色分配。 还可以使用订阅部署创建资源组并向其部署资源。 你将根据部署范围使用不同的命令。

若要部署到**资源组**，请使用 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

若要部署到**订阅**，请使用 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)：

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

本文中的示例使用资源组部署。 有关订阅部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

## <a name="prerequisites"></a>必备组件

你需要使用模板进行部署。 如果还没有模板，请从 Azure 快速入门模板存储库下载并保存一个[示例模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)。 本文中使用的本地文件名称为 **c:\MyTemplates\azuredeploy.json**。

除非你使用 Azure Cloud shell 中，将模板部署，需要安装 Azure PowerShell 并连接到 Azure:

- **在本地计算机上安装 Azure PowerShell cmdlet。** 有关详细信息，请参阅 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)。
- **使用 [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount) 连接到 Azure**。 如果有多个 Azure 订阅，则可能还需要运行 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。 有关详细信息，请参阅[使用多个 Azure 订阅](/powershell/azure/manage-subscriptions-azureps)。

## <a name="deploy-local-template"></a>部署本地模板

以下示例将创建一个资源组，并从本地计算机部署模板。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 它不能以句点结尾。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

部署可能需要几分钟才能完成。

## <a name="deploy-remote-template"></a>部署远程模板

可能更愿意将 Resource Manager 模板存储在外部位置，而不是将它们存储在本地计算机上。 可以将模板存储在源控件存储库（例如 GitHub）中。 另外，还可以将其存储在 Azure 存储帐户中，以便在组织中共享访问。

若要部署外部模板，请使用 TemplateUri 参数。 使用示例中的 URI 从 GitHub 部署示例模板。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

前面的示例要求模板的 URI 可公开访问，它适用于大多数情况，因为模板应该不会包含敏感数据。 如果需要指定敏感数据（如管理员密码），请以安全参数的形式传递该值。 但是，如果不希望模板可公开访问，可以通过将其存储在专用存储容器中来保护它。 有关部署需要共享访问签名 (SAS) 令牌的模板的信息，请参阅[部署具有 SAS 令牌的专用模板](resource-manager-powershell-sas-token.md)。 若要完成教程，请参阅[教程：在资源管理器模板部署中集成 Azure Key Vault](./resource-manager-tutorial-use-key-vault.md)。

## <a name="deploy-from-azure-cloud-shell"></a>从 Azure Cloud shell 部署

可以使用 [Azure Cloud Shell](https://shell.azure.com) 来部署模板。 若要部署外部模板，请提供模板的 URI。 要部署本地模板，必须先将模板加载到 Cloud Shell 的存储帐户。 若要将文件上传到 shell，请从 shell 窗口选择“上传/下载文件”菜单图标。

若要打开 Cloud shell，请浏览到 [https://shell.azure.com](https://shell.azure.com)，或从以下代码部分选择 **Try-It**：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

若要将代码粘贴到 shell 中，请在 shell 内右键单击，然后选择“粘贴”。

## <a name="redeploy-when-deployment-fails"></a>部署失败时，重新部署

此功能也称为“出错时回滚”。 部署失败时，可以自动重新部署部署历史记录中先前成功的部署。 若要指定重新部署，请在部署命令中使用 `-RollbackToLastDeployment` 或 `-RollBackDeploymentName` 参数。 如果已经有已知的良好状态的基础结构部署并且想要还原到此状态，此功能很有用。 有许多需要注意的问题和限制：

- 重新部署使用与以前运行它时相同的参数以相同的方式运行。 不能更改参数。
- 以前的部署是使用[完整模式](./deployment-modes.md#complete-mode)运行的。 以前的部署中未包括的任何资源都将被删除，任何资源配置都将设置为以前的状态。 请确保你完全理解[部署模式](./deployment-modes.md)。
- 重新部署只会影响资源，任何数据更改不会受到影响。
- 只有资源组部署支持此功能，订阅级部署不支持此功能。 有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](./deploy-to-subscription.md)。

若要使用此选项，部署必须具有唯一的名称，以便可以在历史记录中标识它们。 如果没有唯一名称，则当前失败的部署可能会覆盖历史记录中以前成功的部署。 只能将此选项用于根级别部署。 从嵌套模板进行的部署不可用于重新部署。

若要重新部署最后一个成功的部署，请将 `-RollbackToLastDeployment` 参数添加为标志。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

若要重新部署某个特定部署，请使用 `-RollBackDeploymentName` 参数并提供部署名称。

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

指定的部署必须已成功。

## <a name="pass-parameter-values"></a>粘贴参数值

若要传递参数值，可以使用内联参数或参数文件。 本文中前面的示例显示了内联参数。

### <a name="inline-parameters"></a>内联参数

若要传递内联参数，请使用 `New-AzResourceGroupDeployment` 命令提供参数的名称。 例如，若要将字符串和数组传递给模板，请使用：

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

还可以获取文件的内容并将该内容作为内联参数提供。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

当需要提供配置值时，从文件中获取参数值非常有用。 例如，可以[为 Linux 虚拟机提供 cloud-init 值](../virtual-machines/linux/using-cloud-init.md)。

如果需要传递一个对象数组中，在 PowerShell 中创建的哈希表，并将其添加到一个数组。 在部署期间作为参数传递该数组。

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```


### <a name="parameter-files"></a>参数文件

与在脚本中以内联值的形式传递参数相比，可能会发现使用包含参数值的 JSON 文件更为容易。 参数文件可以是本地文件，也可以是具有可访问 URI 的外部文件。

参数文件必须采用以下格式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

请注意，parameters 部分包含与模板中定义的参数匹配的参数名称 (storageAccountType)。 参数文件针对该参数包含了一个值。 此值在部署期间自动传递给模板。 可以创建多个参数文件，然后为方案传入适当的参数文件。

复制上面的示例，并将其保存为名为 `storage.parameters.json` 的文件。

若要传递本地参数文件，请使用 **TemplateParameterFile** 参数：

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要传递外部参数文件，请使用 **TemplateParameterUri** 参数：

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>参数优先级

可以在同一部署操作中使用内联参数和本地参数文件。 例如，可以在本地参数文件中指定某些值，并在部署期间添加其他内联值。 如果同时为本地参数文件中的参数和内联参数提供值，则内联值优先。

但是，使用外部参数文件时，不能传递是内联值或来自本地文件的其他值。 如果在 **TemplateParameterUri** 参数中指定参数文件，则将忽略所有内联参数。 提供外部文件中的所有参数值。 如果模板包括参数文件中无法包括的敏感值，可将该值添加到密钥保管库，或者以内联方式动态提供所有参数值。

### <a name="parameter-name-conflicts"></a>参数名冲突

如果模板包括的一个参数与 PowerShell 命令中的某个参数同名，PowerShell 使用后缀 **FromTemplate** 显示模板的参数。 例如，模板中名为 **ResourceGroupName** 的参数与 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet 中的 **ResourceGroupName** 参数冲突。 系统会提示你提供 **ResourceGroupNameFromTemplate** 的值。 通常，不应将参数命名为与用于部署操作的参数的名称相同以避免这种混乱。

## <a name="test-template-deployments"></a>测试模板部署

若要测试模板和参数值而不实际部署任何资源，请使用 [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)。 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

如果没有检测到错误，该命令在没有响应的情况下完成。 如果检测到错误，则该命令将返回一条错误消息。 例如，如果为存储帐户 SKU 传递不正确的值，将返回以下错误：

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

如果模板有语法错误，该命令将返回一个错误，指示它无法分析该模板。 该消息会指出分析错误的行号和位置。

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>后续步骤

- 若要安全地将服务扩展到多个区域，请参阅 [Azure 部署管理器](deployment-manager-overview.md)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何在模板中定义参数，请参阅[了解 Azure 资源管理器模板的结构和语法](resource-group-authoring-templates.md)。
- 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-powershell-sas-token.md)。

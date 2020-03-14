---
title: 通过 PowerShell 和模板部署资源
description: 使用 Azure 资源管理器和 Azure PowerShell 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: c31cde9d3023c49a03f4a7a6c434c16405c88bea
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273873"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>使用 Resource Manager 模板和 Azure PowerShell 部署资源

了解如何将 Azure PowerShell 与 资源管理器模板配合使用，以向 Azure 部署资源。 有关部署和管理 Azure 解决方案的概念的详细信息，请参阅[模板部署概述](overview.md)。

## <a name="deployment-scope"></a>部署范围

可以将部署定向到订阅中的 Azure 订阅或资源组。 在大多数情况下，将部署到资源组。 使用订阅部署在整个订阅中应用策略和角色分配。 你还可以使用订阅部署创建资源组，并将资源部署到该资源组。 根据部署的范围，你可以使用不同的命令。

若要部署到**资源组**，请使用[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

若要部署到**订阅**，请使用[AzDeployment](/powershell/module/az.resources/new-azdeployment)：

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

有关订阅级别部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

目前，只能通过 REST API 来支持管理组部署。 有关管理组级别部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

本文中的示例使用资源组部署。

## <a name="prerequisites"></a>必备条件

需要部署模板。 如果尚未安装，请从 Azure 快速入门模板存储库下载并保存[示例模板](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)。 本文中使用的本地文件名称为 **c:\MyTemplates\azuredeploy.json**。

除非使用 Azure Cloud shell 部署模板，否则需要安装 Azure PowerShell 并连接到 Azure：

- **在本地计算机上安装 Azure PowerShell cmdlet。** 有关详细信息，请参阅 [Azure PowerShell 入门](/powershell/azure/get-started-azureps)。
- **使用 [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount) 连接到 Azure**。 如果有多个 Azure 订阅，则可能还需要运行 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。 有关详细信息，请参阅[使用多个 Azure 订阅](/powershell/azure/manage-subscriptions-azureps)。

## <a name="deploy-local-template"></a>部署本地模板

以下示例创建一个资源组，并从本地计算机部署模板。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 它不能以句点结尾。

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

若要部署外部模板，请使用 **TemplateUri** 参数。 使用示例中的 URI 从 GitHub 部署示例模板。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

前面的示例要求模板的 URI 可公开访问，它适用于大多数情况，因为模板应该不会包含敏感数据。 如果需要指定敏感数据（如管理员密码），请以安全参数的形式传递该值。 但是，如果不希望模板可公开访问，可以通过将其存储在专用存储容器中来保护它。 有关部署需要共享访问签名 (SAS) 令牌的模板的信息，请参阅[部署具有 SAS 令牌的专用模板](secure-template-with-sas-token.md)。 若要学习教程，请参阅[教程：将 Azure Key Vault 集成资源管理器模板部署中](template-tutorial-use-key-vault.md)。

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

## <a name="pass-parameter-values"></a>粘贴参数值

若要传递参数值，可以使用内联参数或参数文件。

### <a name="inline-parameters"></a>内联参数。

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

当需要提供配置值时，从文件中获取参数值非常有用。 例如，可以[为 Linux 虚拟机提供 cloud-init 值](../../virtual-machines/linux/using-cloud-init.md)。

如果需要传入对象的数组，请在 PowerShell 中创建哈希表并将其添加到数组中。 在部署过程中将该数组作为参数传递。

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

有关参数文件的详细信息，请参阅[Create 资源管理器 parameter file](parameter-files.md)。

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

## <a name="test-template-deployments"></a>测试模板部署

若要测试模板和参数值而不实际部署任何资源，请使用[AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)。 

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

- 若要在出现错误时回滚到成功的部署，请参阅[出错时回滚到成功部署](rollback-on-error.md)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何在模板中定义参数，请参阅[了解 Azure 资源管理器模板的结构和语法](template-syntax.md)。
- 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。

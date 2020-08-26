---
title: 使用 Azure CLI 和模板部署资源
description: 使用 Azure 资源管理器和 Azure CLI 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: da865d3b425da6b5969e540a424b513d9a58bd9a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040799"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>通过 ARM 模板和 Azure CLI 来部署资源

本文介绍如何使用 Azure 资源管理器模板（ARM 模板） Azure CLI 将资源部署到 Azure。 如果不熟悉部署和管理 Azure 解决方案的概念，请参阅[模版部署概述](overview.md)。

部署命令在 Azure CLI 版本 2.2.0 中已更改。 本文中的示例需要 Azure CLI 2.2.0 或更高版本。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

如果没有安装 Azure CLI，则可使用 [Cloud Shell](#deploy-template-from-cloud-shell)。

## <a name="deployment-scope"></a>部署范围

可将部署目标设定为资源组、订阅、管理组或租户。 大多数情况下，我们会将以资源组指定为部署目标。 若要在更大范围内应用策略和角色分配，请使用订阅、管理组或租户部署。 部署到订阅时，可以创建资源组并将资源部署到该资源组。

根据部署范围使用不同的命令。

* 若要部署到资源组****，请使用 [az deployment group create](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create)：

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* 若要部署到订阅****，请使用 [az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)：

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  有关订阅级部署的详细信息，请参阅[在订阅级别创建资源组和资源](deploy-to-subscription.md)。

* 若要部署到管理组****，请使用 [az deployment mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)：

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  有关管理组级部署的详细信息，请参阅[在管理组级别创建资源](deploy-to-management-group.md)。

* 若要部署到租户****，请使用 [az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)：

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  有关租户级别部署的详细信息，请参阅[在租户级别创建资源](deploy-to-tenant.md)。

本文中的示例使用资源组部署。

## <a name="deploy-local-template"></a>部署本地模板

将资源部署到 Azure 时，执行以下操作：

1. 登录到 Azure 帐户
2. 创建用作已部署资源的容器的资源组。 资源组名称只能包含字母数字字符、句点、下划线、连字符和括号。 它最多可以包含 90 个字符。 它不能以句点结尾。
3. 将定义要创建的资源的模板部署到资源组。

模板可以包括可用于自定义部署的参数。 例如，可以提供为特定环境（如开发环境、测试环境和生产环境）定制的值。 示例模板定义了存储帐户 SKU 的参数。

以下示例将创建一个资源组，并从本地计算机部署模板：

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

部署可能需要几分钟才能完成。 完成之后，会看到一条包含以下结果的消息：

```output
"provisioningState": "Succeeded",
```

## <a name="deployment-name"></a>部署名称

在前面的示例中，你已将部署命名为 `ExampleDeployment` 。 如果未提供部署的名称，将使用模板文件的名称。 例如，如果部署名为的模板 `azuredeploy.json` ，但未指定部署名称，则会将部署命名为 `azuredeploy` 。

每次运行部署时，会在资源组的部署历史记录中添加一个包含部署名称的条目。 如果运行另一个部署并为其指定了相同的名称，则会将以前的条目替换为当前部署。 如果要在部署历史记录中维护唯一条目，请为每个部署指定唯一名称。

若要创建唯一名称，可以分配一个随机数字。

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

或者，添加日期值。

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

如果以相同的部署名称运行对同一资源组的并发部署，则只完成最后一个部署。 任何没有完成的同名部署都将替换为最后一个部署。 例如，如果你运行一个名为 `newStorage` 的部署，它将部署一个名为的存储帐户 `storage1` ，同时运行名为的另一个部署， `newStorage` 该部署将部署名为的存储帐户 `storage2` ，你只部署一个存储帐户。 生成的存储帐户名为 `storage2` 。

但是，如果运行名为的部署 `newStorage` （部署名为的存储帐户 `storage1` ），并且在完成后立即运行名为的另一个部署 `newStorage` ，该部署将部署名为的存储帐户 `storage2` ，则你有两个存储帐户。 一个名为 `storage1` ，另一个名为 `storage2` 。 但是，部署历史记录中只有一个条目。

为每个部署指定唯一名称时，可以在不发生冲突的情况下同时运行它们。 如果运行名为的部署 `newStorage1` ，该部署将部署名为的存储帐户 `storage1` ，同时运行名为的另一个部署，该部署将部署 `newStorage2` 名为的存储帐户 `storage2` ，则部署历史记录中有两个存储帐户和两个条目。

若要避免与并发部署冲突并确保部署历史记录中的唯一条目，请为每个部署指定唯一名称。

## <a name="deploy-remote-template"></a>部署远程模板

你可能更愿意将 ARM 模板存储在外部位置，而不是存储在本地计算机上。 可以将模板存储在源控件存储库（例如 GitHub）中。 另外，还可以将其存储在 Azure 存储帐户中，以便在组织中共享访问。

若要部署外部模板，请使用 **template-uri** 参数。 使用示例中的 URI 从 GitHub 部署示例模板。

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

前面的示例要求模板的 URI 可公开访问，它适用于大多数情况，因为模板应该不会包含敏感数据。 如果需要指定敏感数据（如管理员密码），请以安全参数的形式传递该值。 但是，如果不希望模板可公开访问，可以通过将其存储在专用存储容器中来保护它。 若要了解如何部署需要共享访问签名 (SAS) 令牌的模板，请参阅[部署具有 SAS 令牌的专用模板](secure-template-with-sas-token.md)。

## <a name="preview-changes"></a>预览更改

在部署模板之前，可以预览模板将对环境做出的更改。 使用[假设操作](template-deploy-what-if.md)验证模板是否进行了预期的更改。 假设操作还验证模板是否有错误。

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

在 Cloud Shell 中使用以下命令：

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>parameters

若要传递参数值，可以使用内联参数或参数文件。

### <a name="inline-parameters"></a>内联参数。

若要传递内联参数，请在 `parameters` 中提供值。 例如，若要在 Bash shell 中将字符串和数组传递给模板，请使用：

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

如果要将 Azure CLI 与 Windows 命令提示符 (CMD) 或 PowerShell 配合使用，请以以下格式传递数组：`exampleArray="['value1','value2']"`。

还可以获取文件的内容并将该内容作为内联参数提供。

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

当需要提供配置值时，从文件中获取参数值非常有用。 例如，可以[为 Linux 虚拟机提供 cloud-init 值](../../virtual-machines/linux/using-cloud-init.md)。

arrayContent.json 格式为：

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>参数文件

你可能会发现，与在脚本中以内联值的形式传递参数相比，使用包含参数值的 JSON 文件更为容易。 参数文件必须是本地文件。 Azure CLI 不支持外部参数文件。

有关参数文件的详细信息，请参阅[创建资源管理器参数文件](parameter-files.md)。

若要传递本地参数文件，请使用 `@` 指定名为 storage.parameters.json 的本地文件。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>处理扩展 JSON 格式

若要使用 Azure CLI 2.3.0 或更低版本部署包含多行字符串或注释的模板，必须使用 `--handle-extended-json-format` 开关。  例如：

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>后续步骤

- 若要在出错时回退到成功的部署，请参阅[出错时回退到成功的部署](rollback-on-error.md)。
- 若要指定如何处理存在于资源组中但未在模板中定义的资源，请参阅 [Azure 资源管理器部署模式](deployment-modes.md)。
- 若要了解如何在模板中定义参数，请参阅[了解 ARM 模板的结构和语法](template-syntax.md)。
- 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](common-deployment-errors.md)。
- 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](secure-template-with-sas-token.md)。

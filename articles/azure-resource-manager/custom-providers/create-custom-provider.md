---
title: 创建资源提供程序
description: 介绍如何创建资源提供程序并部署其自定义资源类型。
author: MSEvanhi
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: evanhi
ms.openlocfilehash: 541d140716e52b4fe1db4bc999682914a380a5f0
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/25/2020
ms.locfileid: "85368101"
---
# <a name="quickstart-create-a-custom-provider-and-deploy-custom-resources"></a>快速入门：创建自定义提供程序并部署自定义资源

在本快速入门中，你将创建自己的资源提供程序，并为该资源提供程序部署自定义资源类型。 有关自定义提供程序的详细信息，请参阅 [Azure 自定义提供程序预览版概述](overview.md)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 完成本快速入门中的步骤需要调用 `REST` 操作。 可以通过[不同的方法来发送 REST 请求](/rest/api/azure/)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) 命令需要扩展。 有关详细信息，请参阅[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)。
- Azure CLI 示例将 `az rest` 用于 `REST` 请求。 有关详细信息，请参阅 [az rest](/cli/azure/reference-index#az-rest)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- PowerShell 命令是使用 PowerShell 7 或更高版本以及 Azure PowerShell 模块在本地运行的。 有关详细信息，请参阅[安装 Azure PowerShell](/powershell/azure/install-az-ps)。
- 如果还没有用于 `REST` 操作的工具，请安装 [ARMClient](https://github.com/projectkudu/ARMClient)。 它是一种可简化 Azure 资源管理器 API 调用的开源命令行工具。
- 安装 ARMClient 后，可从 PowerShell 命令提示符中通过键入 `armclient.exe` 来显示使用情况信息。 或者，转到 [ARMClient wiki](https://github.com/projectkudu/ARMClient/wiki)。

---

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-custom-provider"></a>部署自定义提供程序

若要设置自定义提供程序，请将[示例模板](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json)部署到 Azure 订阅。

部署模板之后，订阅将具有以下资源：

- 可以针对资源和操作运行的 Function App。
- 存储帐户，用于存储通过自定义提供程序创建的用户。
- 自定义提供程序，用于定义自定义资源类型和操作。 它使用函数应用终结点来发送请求。
- 自定义提供程序提供的自定义资源。

若要部署自定义提供程序，请使用 Azure CLI、PowerShell 或 Azure 门户：

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

本示例提示你输入资源组、位置和提供程序的函数应用名称。 名称会存储在其他命令中所使用的变量中。 [az group create](/cli/azure/group#az-group-create) 和 [az deployment group create](/cli/azure/deployment/group#az-deployment-group-create) 命令将部署资源。

```azurecli-interactive
read -p "Enter a resource group name:" rgName &&
read -p "Enter the location (i.e. eastus):" location &&
read -p "Enter the provider's function app name:" funcName &&
templateUri="https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json" &&
az group create --name $rgName --location "$location" &&
az deployment group create --resource-group $rgName --template-uri $templateUri --parameters funcName=$funcName &&
echo "Press [ENTER] to continue ..." &&
read
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

本示例提示你输入资源组、位置和提供程序的函数应用名称。 名称会存储在其他命令中所使用的变量中。 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 和 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 命令将部署资源。

```powershell
$rgName = Read-Host -Prompt "Enter a resource group name"
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$funcName = Read-Host -Prompt "Enter the provider's function app name"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json"
New-AzResourceGroup -Name $rgName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $rgName -TemplateUri $templateUri -funcName $funcName
Read-Host -Prompt "Press [ENTER] to continue ..."
```

---

还可以从 Azure 门户部署解决方案。 选择“部署到 Azure”按钮以在 Azure 门户中打开模板。

[![部署到 Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json)

## <a name="view-custom-provider-and-resource"></a>查看自定义提供程序和资源

在门户中，自定义提供程序是隐藏的资源类型。 要确认是否已部署资源提供程序，请导航到资源组。 选择“显示隐藏类型”选项。

![显示隐藏的资源类型](./media/create-custom-provider/show-hidden.png)

要查看已部署的自定义资源类型，请对资源类型使用 `GET` 操作。

```http
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
subID=$(az account show --query id --output tsv)
requestURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"
az rest --method get --uri $requestURI
```

你会收到响应：

```json
{
  "value": [
    {
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole",
        "provisioningState": "Succeeded"
      },
      "resourceGroup": "<rg-name>",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

你会收到响应：

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

---

## <a name="call-action"></a>调用操作

自定义提供程序还有名为 `ping` 的操作。 处理请求的代码在函数应用中实现。 `ping` 操作使用一条问候信息进行回复。

要发送 `ping` 请求，请对自定义提供程序使用 `POST` 操作。

```http
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
pingURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"
az rest --method post --uri $pingURI
```

你会收到响应：

```json
{
  "message": "hello <function-name>.azurewebsites.net",
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  }
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

你会收到响应：

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

---

## <a name="create-a-resource-type"></a>创建资源类型

若要创建自定义资源类型，可以在模板中部署资源。 此方法显示在本快速入门部署的模板中。 也可针对此资源类型发送 `PUT` 请求。

```http
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
addURI="https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
az rest --method put --uri $addURI --body "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"
```

你会收到响应：

```json
{
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "properties": {
    "FullName": "Test User",
    "Location": "Earth",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "<rg-name>",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

你会收到响应：

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

---

## <a name="custom-resource-provider-commands"></a>自定义资源提供程序命令

通过 [custom-providers](/cli/azure/ext/custom-providers/custom-providers/resource-provider) 命令来使用自定义资源提供程序。

### <a name="list-custom-resource-providers"></a>列出自定义资源提供程序

使用 `list` 命令显示订阅中的所有自定义资源提供程序。 默认列出当前订阅的自定义资源提供程序，你也可以指定 `--subscription` 参数。 要列出资源组，请使用 `--resource-group` 参数。

```azurecli-interactive
az custom-providers resource-provider list --subscription $subID
```

```json
[
  {
    "actions": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "ping",
        "routingType": "Proxy"
      }
    ],
    "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceproviders/<provider-name>",
    "location": "eastus",
    "name": "<provider-name>",
    "provisioningState": "Succeeded",
    "resourceGroup": "<rg-name>",
    "resourceTypes": [
      {
        "endpoint": "https://<provider-name>.azurewebsites.net/api/{requestPath}",
        "name": "users",
        "routingType": "Proxy, Cache"
      }
    ],
    "tags": {},
    "type": "Microsoft.CustomProviders/resourceproviders",
    "validations": null
  }
]
```

### <a name="show-the-properties"></a>显示属性

使用 `show` 命令显示自定义资源提供程序的属性。 输出格式类似于 `list` 输出。

```azurecli-interactive
az custom-providers resource-provider show --resource-group $rgName --name $funcName
```

### <a name="create-a-new-resource"></a>创建新资源

使用 `create` 命令创建或更新自定义资源提供程序。 此示例更新了 `actions` 和 `resourceTypes`。

```azurecli-interactive
az custom-providers resource-provider create --resource-group $rgName --name $funcName \
--action name=ping endpoint=https://myTestSite.azurewebsites.net/api/{requestPath} routing_type=Proxy \
--resource-type name=users endpoint=https://myTestSite.azurewebsites.net/api{requestPath} routing_type="Proxy, Cache"
```

```json
"actions": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api/{requestPath}",
    "name": "ping",
    "routingType": "Proxy"
  }
],

"resourceTypes": [
  {
    "endpoint": "https://myTestSite.azurewebsites.net/api{requestPath}",
    "name": "users",
    "routingType": "Proxy, Cache"
  }
],
```

### <a name="update-the-providers-tags"></a>更新提供程序的标记

`update` 命令仅更新自定义资源提供程序的标记。 在 Azure 门户中，自定义资源提供程序的应用服务会显示标记。

```azurecli-interactive
az custom-providers resource-provider update --resource-group $rgName --name $funcName --tags new=tag
```

```json
"tags": {
  "new": "tag"
},
```

### <a name="delete-a-custom-resource-provider"></a>删除自定义资源提供程序

`delete` 命令将发出提示，并仅删除自定义资源提供程序。 不会删除存储帐户、应用服务和应用服务计划。 删除提供程序后，将返回到命令提示符。

```azurecli-interactive
az custom-providers resource-provider delete --resource-group $rgName --name $funcName
```

## <a name="next-steps"></a>后续步骤

有关自定义提供程序的简介，请参阅以下文章：

> [!div class="nextstepaction"]
> [Azure 自定义提供程序预览版概述](overview.md)

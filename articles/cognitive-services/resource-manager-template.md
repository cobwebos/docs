---
title: 快速入门：使用 ARM 模板创建 Azure 认知服务资源 | Microsoft Docs
description: 开始使用 Azure 资源管理器模板部署认知服务资源。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 06/18/2020
ms.author: aahi
ms.custom: subject-armqs
ms.openlocfilehash: fbaa10ec8a0b48d40644bb90144d89f53ab52563
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2020
ms.locfileid: "85513544"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>快速入门：使用 ARM 模板创建认知服务资源

通过本文使用 Azure 资源管理器 (ARM) 模板创建和部署认知服务资源。 借助这个多服务资源可以实现以下操作：
* 通过单个密钥和终结点访问多个 Azure 认知服务。
* 合并所用资源的计费。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

* [免费创建一个 Azure 订阅](https://azure.microsoft.com/free/cognitive-services)（如果没有）。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/)。

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json" highlight="27-41":::

模板中定义了一个 Azure 资源：
* [Microsoft.CognitiveServices/accounts](https://docs.microsoft.com/azure/templates/microsoft.cognitiveservices/accounts)：创建一个认知服务资源。

## <a name="deploy-the-template"></a>部署模板

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 单击“**部署到 Azure**”按钮。

    [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. 输入以下值。
    
    |值  |说明  |
    |---------|---------|
    | **订阅** | 选择 Azure 订阅。 |
    | **资源组** | 选择“新建”，输入资源组的唯一名称，然后单击“确定” 。 |
    | **区域** | 选择区域。  例如“美国东部” |
    | **认知服务名称** | 替换为资源的唯一名称。 在下一部分验证部署时，需要该名称。 |
    | **位置** | 替换为上面使用的区域。 |
    | **Sku** | 资源的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/)。 |
    
    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="“创建资源”屏幕。":::

3. 选择“查看 + 创建”，然后选择“创建” 。 资源部署成功完成后，“转到资源”按钮会高亮显示。


# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

> [!NOTE]
> `az deployment group` create 需要使用 Azure CLI 2.6 或更高版本。 使用 `az --version` 可显示版本。 有关详细信息，请参阅[本文档](https://docs.microsoft.com/cli/azure/deployment/group)。

[在本地计算机上](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)使用 Azure 命令行接口 (CLI) 或在浏览器中单击“试用”来运行以下脚本。 输入新资源组的名称和位置（例如 `centralus`）后，系统将使用 ARM 模板在该资源组中部署认知服务资源。 记住此处使用的名称。 该名称稍后将用于部署验证。


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---


## <a name="validate-the-deployment"></a>验证部署

# <a name="portal"></a>[门户](#tab/portal)

部署完成后，单击“转到资源”按钮以查看新资源。 通过以下方式也可找到该资源组：

1. 在左侧导航菜单中选择“资源组”。
2. 选择资源组名称。

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

使用 Azure CLI 运行以下脚本，然后输入之前创建的资源组的名称。

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组时也会删除资源组中包含的任何其他资源。

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 在 Azure 门户中展开左侧的菜单，打开服务菜单，然后选择“资源组”以显示资源组的列表。
2. 找到包含要删除的资源的资源组。
3. 右键单击资源组列表。 选择“删除资源组”并进行确认。

# <a name="azure-cli"></a>[Azure CLI](#tab/CLI)

使用 Azure CLI 运行以下脚本，然后输入之前创建的资源组的名称。 

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>后续步骤

* [对 Azure 认知服务的请求进行身份验证](authentication.md)
* [什么是 Azure 认知服务？](Welcome.md)
* [自然语言支持](language-support.md)
* [Docker 容器支持](cognitive-services-container-support.md)

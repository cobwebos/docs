---
title: "使用 Azure CLI 和模板部署资源 | Microsoft Docs"
description: "使用 Azure Resource Manager 和 Azure CLI 将资源部署到 Azure。 资源在 Resource Manager 模板中定义。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: c889a609b8d49474216fe1dcfba69a881edb4133
ms.lasthandoff: 04/21/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>使用 Resource Manager 模板和 Azure CLI 部署资源

本主题介绍了如何将 [Azure CLI 2.0](/cli/azure/install-az-cli2) 与 Resource Manager 模板配合使用来将资源部署到 Azure。  你的模板可以是本地文件或是可通过 URI 访问的外部文件。

可以从[创建你的第一个 Azure Resource Manager 模板](resource-manager-create-first-template.md#final-template)一文中获取这些示例中使用的模板 (storage.json)。 若要将该模板用于这些示例，请创建一个 JSON 文件并添加复制的内容。

## <a name="deploy-local-template"></a>部署本地模板

若要快速开始部署，请使用以下命令来部署具有内联参数的本地模板：

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

部署可能需要几分钟才能完成。 完成后，会看到一条包含结果的消息：

```azurecli
"provisioningState": "Succeeded",
```

前面的示例在默认订阅中创建了资源组。 若要使用其他订阅，请在登录后添加 [az account set](/cli/azure/account#set) 命令。

## <a name="deploy-external-template"></a>部署外部模板

若要部署外部模板，请使用 **template-uri** 参数。 模板可以在任何可公开访问的 URI（如存储帐户中的文件）中。
   
```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```

可以通过要求使用共享访问签名 (SAS) 令牌进行访问来保护模板。 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-cli-sas-token.md)。

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

若要传递本地参数文件，请使用：

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-deployment"></a>测试部署

若要测试模板和参数值而不实际部署任何资源，请使用 [az group deployment validate](/cli/azure/group/deployment#validate)。 无论使用本地文件还是远程文件，它的选项都完全相同。

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="debug"></a>调试

若要查看对失败部署执行的操作的信息，请使用：
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。


## <a name="export-resource-manager-template"></a>导出 Resource Manager 模板
对于现有的资源组（通过 Azure CLI 或门户等其他方法部署），可以查看资源组的 Resource Manager 模板。 导出模板有两个好处：

1. 由于模板中定义了所有基础结构，因此将来可以轻松地自动完成解决方案的部署。
2. 可以查看代表解决方案的 JavaScript 对象表示法 (JSON)，以此熟悉模板语法。

若要查看资源组的模板，请运行 [az group export](/cli/azure/group#export) 命令。

```azurecli
az group export --name ExampleGroup
```

有关详细信息，请参阅[从现有资源导出 Azure Resource Manager 模板](resource-manager-export-template.md)。


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

若要使用完整模式，请使用 `mode` 参数：

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters "{\"storageNamePrefix\":{\"value\":\"contoso\"},\"storageSKU\":{\"value\":\"Standard_GRS\"}}"
```


## <a name="next-steps"></a>后续步骤
* 有关用于部署模板的完整示例脚本，请参阅 [Resource Manager 模板部署脚本](resource-manager-samples-cli-deploy.md)。
* 若要在模板中定义参数，请参阅[创作模板](resource-group-authoring-templates.md#parameters)。
* 有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。
* 有关部署需要 SAS 令牌的模板的信息，请参阅[使用 SAS 令牌部署专用模板](resource-manager-cli-sas-token.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。


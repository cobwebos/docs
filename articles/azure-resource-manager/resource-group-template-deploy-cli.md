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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b084c722b75152b8a2b867f21d546abd04a96f04
ms.lasthandoff: 03/15/2017


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>使用 Resource Manager 模板和 Azure CLI 部署资源
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [门户](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

本主题介绍了如何将 [Azure CLI 2.0](/cli/azure/install-az-cli2) 与 Resource Manager 模板配合使用来将资源部署到 Azure。  你的模板可以是本地文件或是可通过 URI 访问的外部文件。 如果模板驻留在存储帐户中，你可以限制对该模板的访问，并在部署过程中提供共享访问签名 (SAS) 令牌。

## <a name="deploy"></a>部署

* 若要快速开始部署，请使用以下命令来部署具有内联参数的本地模板：

  ```azurecli
  az login
  az account set --subscription {subscription-id}

  az group create --name ExampleGroup --location "Central US"
  az group deployment create \
      --name ExampleDeployment \
      --resource-group ExampleGroup \
      --template-file storage.json \
      --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
  ```

  部署可能需要几分钟才能完成。 完成之后，将看到如下消息：

  ```azurecli
  "provisioningState": "Succeeded",
  ```
  
* 仅当要使用非默认订阅时，才需要 `az account set` 命令。 若要查看所有订阅及其 ID，请使用：

  ```azurecli
  az account list
  ```

* 若要部署外部模板，请使用 **template-uri** 参数：
   
   ```azurecli
   az group deployment create \
       --name ExampleDeployment \
       --resource-group ExampleGroup \
       --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/storage.json" \
       --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
   ```

* 若要在文件中传递参数值，请使用：

   ```azurecli
   az group deployment create \
       --name ExampleDeployment \
       --resource-group ExampleGroup \
       --template-file storage.json \
       --parameters @storage.parameters.json
   ```

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


[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

若要使用完整模式，请使用 mode 参数：

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters '{"storageNamePrefix":{"value":"contoso"},"storageSKU":{"value":"Standard_GRS"}}'
```

## <a name="deploy-template-from-storage-with-sas-token"></a>使用 SAS 令牌从存储空间部署模板
可以将模板添加到存储帐户，并在部署过程中使用 SAS 令牌链接到这些模板。

> [!IMPORTANT]
> 通过执行以下步骤，只有帐户所有者可以访问包含模板的 blob。 但是，如果为 blob 创建 SAS 令牌，则拥有该 URI 的任何人都可以访问 blob。 如果其他用户截获了该 URI，则此用户可以访问该模板。 使用 SAS 令牌是限制对模板的访问的好方法，但不应直接在模板中包括密码等敏感数据。
> 
> 

### <a name="add-private-template-to-storage-account"></a>将专用模板添加到存储帐户
以下命令设置一个专用存储帐户容器并上载一个模板：
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>在部署期间提供 SAS 令牌
若要在存储帐户中部署专用模板，请生成 SAS 令牌，并将其包括在模板的 URI 中。 设置到期时间以允许足够的时间来完成部署。
   
```azurecli
seconds='@'$(( $(date +%s) + 1800 ))
expiretime=$(date +%Y-%m-%dT%H:%MZ --date=$seconds)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

有关将 SAS 令牌与链接模板配合使用的示例，请参阅[将已链接的模版与 Azure Resource Manager 配合使用](resource-group-linked-templates.md)。

## <a name="debug"></a>调试

若要查看对失败部署执行的操作的信息，请使用：
   
```azurecli
az group deployment operation list --resource-group ExampleGroup --name vmlinux --query "[*].[properties.statusMessage]"
```

有关解决常见部署错误的提示，请参阅[排查使用 Azure Resource Manager 时的常见 Azure 部署错误](resource-manager-common-deployment-errors.md)。

## <a name="next-steps"></a>后续步骤
* 有关通过 .NET 客户端库部署资源的示例，请参阅[使用 .NET 库和模板部署资源](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 若要在模板中定义参数，请参阅[创作模板](resource-group-authoring-templates.md#parameters)。
* 有关将解决方案部署到不同环境的指南，请参阅 [Microsoft Azure 中的开发和测试环境](solution-dev-test-environments.md)。
* 有关使用 KeyVault 引用来传递安全值的详细信息，请参阅[在部署期间传递安全值](resource-manager-keyvault-parameter.md)。
* 有关企业可如何使用 Resource Manager 有效管理订阅的指南，请参阅 [Azure 企业基架 - 出于合规目的监管订阅](resource-manager-subscription-governance.md)。
* 有关自动部署的四部分系列，请参阅[将应用程序自动部署到 Azure 虚拟机](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 此系列介绍应用程序体系结构、访问和安全、可用性和缩放以及应用程序部署。



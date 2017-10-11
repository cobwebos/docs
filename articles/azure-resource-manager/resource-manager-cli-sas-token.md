---
title: "部署使用 SAS 令牌和 Azure CLI 的 Azure 模板 |Microsoft 文档"
description: "使用 Azure 资源管理器和 Azure CLI 从模板使用 SAS 令牌保护将资源部署到 Azure。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: 22387aadd8f53a65efb76a29a9403c46a2c25954
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>部署使用 SAS 令牌和 Azure CLI 的专用资源管理器模板

当你的模板驻留在存储帐户中时，可以限制对模板的访问并在部署过程中提供的共享的访问签名 (SAS) 令牌。 本主题说明如何使用资源管理器模板与 Azure PowerShell 在部署过程中提供的 SAS 令牌。 

## <a name="add-private-template-to-storage-account"></a>将专用模板添加到存储帐户

可以将你的模板添加到存储帐户和到它们在与 SAS 令牌的部署过程中的链接。

> [!IMPORTANT]
> 通过执行以下步骤，包含模板的 blob 是可供只有帐户所有者访问。 但是，创建 blob 的 SAS 令牌，blob 时，该 URI 的任何人都可以访问。 另一个用户截获 URI，该用户能够访问的模板。 使用 SAS 令牌是限制对你的模板，访问的一种好方法，但不是应直接在模板中包含密码等敏感数据。
> 
> 

下面的示例设置专用存储帐户容器，并将上载一个模板：
   
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

### <a name="provide-sas-token-during-deployment"></a>在部署过程提供 SAS 令牌
若要部署的存储帐户中的专用模板，生成 SAS 令牌并将其包括在模板的 URI。 设置允许足够时间来完成部署的到期时间。
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
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

与链接的模板使用 SAS 令牌的示例，请参阅[使用与 Azure 资源管理器中的链接的模板](resource-group-linked-templates.md)。

## <a name="next-steps"></a>后续步骤
* 部署模板的简介，请参阅[部署使用资源管理器模板和 Azure PowerShell 的资源](resource-group-template-deploy-cli.md)。
* 部署模板的完整示例脚本，请参阅[部署资源管理器模板脚本](resource-manager-samples-cli-deploy.md)
* 若要在模板中定义参数，请参阅[创作模板](resource-group-authoring-templates.md#parameters)。
* 有关企业如何使用资源管理器能够有效地管理订阅的指南，请参阅[Azure 企业基架的规范性订阅监管](resource-manager-subscription-governance.md)。

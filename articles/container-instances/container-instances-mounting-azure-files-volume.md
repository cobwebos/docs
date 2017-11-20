---
title: "在 Azure 容器实例中装载 Azure 文件卷"
description: "了解如何装载 Azure 文件卷以保持 Azure 容器实例的状态"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0f824dad7ba5b661941e952383025e5171f32e55
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>装载与 Azure 容器实例的 Azure 文件共享

默认情况下，Azure 容器实例是无状态的。 如果容器崩溃或停止，其所有状态都会丢失。 若要将状态保持至超过容器寿命，必须从外部存储装载卷。 本文介绍如何装载 Azure 文件共享，以用于 Azure 容器实例。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

必须先创建 Azure 文件共享，才能将其用于 Azure 容器实例。 运行以下脚本来创建存储帐户，以托管文件共享和共享其本身。 存储帐户名必须是全局唯一的，因此该脚本会向基础字符串添加一个随机值。

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>获取存储帐户访问详细信息

若要在 Azure 容器实例中将 Azure 文件共享装载为卷，需要 3 个值：存储帐户名、共享名和存储访问密钥。

如果使用上述脚本，则创建的存储帐户名末尾会带有一个随机值。 若要查询最终字符串（包括随机部分），请使用以下命令：

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

共享名已知（即上述脚本中的 acishare），因此剩下的就是找到存储帐户密钥，可使用以下命令：

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>使用 Azure Key Vault 存储存储帐户访问详细信息

存储帐户密钥会保护数据访问，因此建议将其存储在 Azure Key Vault 中。

使用 Azure CLI 创建密钥保管库：

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

`enabled-for-template-deployment` 交换机允许 Azure 资源管理器在部署时从密钥保管库拉取机密。

在密钥保管库中将存储帐户密钥存储为新机密：

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>装载卷

在容器中将 Azure 文件共享装载为卷需要两步。 首先，提供共享的详细信息作为容器组定义过程的一个环节，然后指定如何在组中的 1 个或多个容器内装载卷。

若要定义要用于装载的卷，请向 Azure 资源管理器模板中的容器组定义添加 `volumes` 数组，然后在各个容器定义中引用它们。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

模板将存储帐户名和密钥包含为参数，这些参数可在单独的参数文件中提供。 若要填充参数文件，需要 3 个值：存储帐户名、Azure Key Vault 的资源 ID，以及用于存储存储密钥的密钥保管库机密名称。 如果已按照前面的步骤操作，则可使用以下脚本获取这些值：

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

将值插入参数文件：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>部署容器和管理文件

定义模板后，即可创建容器并使用 Azure CLI 为其装载卷。 假设模板文件名为 azuredeploy.json，参数文件名为 azuredeploy.parameters.json，则命令行为：

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group $ACI_PERS_RESOURCE_GROUP
```

启动容器后，可使用通过 **seanmckenna/aci-hellofiles** 映像部署的简单 Web 应用管理指定装载路径下 Azure 文件共享中的文件。 使用 [az container show](/cli/azure/container#az_container_show) 命令获取 Web 应用的 IP 地址：

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

可使用 [Microsoft Azure 存储资源管理器](https://storageexplorer.com)之类的工具检索及检查写入到文件共享的文件。

>[!NOTE]
> 若要深入了解如何使用 Azure 资源管理器模板、参数文件，以及如何使用 Azure CLI 进行部署，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../azure-resource-manager/resource-group-template-deploy-cli.md)。

## <a name="next-steps"></a>后续步骤

- 使用 Azure 容器实例 [quickstart](container-instances-quickstart.md) 部署第一个容器
- 了解 [Azure 容器实例和容器协调器之间的关系](container-instances-orchestrator-relationship.md)

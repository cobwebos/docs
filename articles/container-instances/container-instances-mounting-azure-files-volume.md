---
title: "在 Azure 容器实例中装载 Azure 文件卷"
description: "了解如何装载 Azure 文件卷以保持 Azure 容器实例的状态"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 12/05/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: b2e8e27cecb4d1225e378690063b42f5d5242868
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2017
---
# <a name="mount-an-azure-file-share-with-azure-container-instances"></a>装载与 Azure 容器实例的 Azure 文件共享

默认情况下，Azure 容器实例是无状态的。 如果容器崩溃或停止，其所有状态都会丢失。 若要将状态保持至超过容器寿命，必须从外部存储装载卷。 本文介绍如何装载 Azure 文件共享，以用于 Azure 容器实例。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

必须先创建 Azure 文件共享，才能将其用于 Azure 容器实例。 运行以下脚本来创建存储帐户，以托管文件共享和共享本身。 存储帐户名必须是全局唯一的，因此该脚本会向基础字符串添加一个随机值。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>获取存储帐户访问详细信息

若要在 Azure 容器实例中将 Azure 文件共享装载为卷，需要 3 个值：存储帐户名、共享名和存储访问密钥。

如果使用上述脚本，则创建的存储帐户名末尾会带有一个随机值。 若要查询最终字符串（包括随机部分），请使用以下命令：

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

共享名已知（在上述脚本中定义为 acishare），因此剩下的就是找到存储帐户密钥，可使用以下命令：

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="deploy-the-container-and-mount-the-volume"></a>部署容器和装载卷

若要将 Azure 文件共享装载为容器中的卷，请在使用 [az container create](/cli/azure/container#az_container_create) 创建容器时指定共享和卷装入点。 如果已执行前面的步骤，则可以使用以下命令装载先前创建的共享以创建容器：

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image seanmckenna/aci-hellofiles \
    --ip-address Public \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

## <a name="manage-files-in-mounted-volume"></a>管理已装载卷中的文件

启动容器后，可使用通过 [seanmckenna/aci-hellofiles](https://hub.docker.com/r/seanmckenna/aci-hellofiles/) 映像部署的简单 Web 应用管理指定装载路径下 Azure 文件共享中的文件。 使用 [az container show](/cli/azure/container#az_container_show) 命令获取 Web 应用的 IP 地址：

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles -o table
```

可使用 [Azure 门户](https://portal.azure.com)或 [Microsoft Azure 存储资源管理器](https://storageexplorer.com)之类的工具检索及检查写入到文件共享的文件。

## <a name="next-steps"></a>后续步骤

了解 [Azure 容器实例和容器协调程序](container-instances-orchestrator-relationship.md)之间的关系。

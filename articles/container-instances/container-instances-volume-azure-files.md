---
title: 在 Azure 容器实例中装载 Azure 文件卷
description: 了解如何装载 Azure 文件卷以保持 Azure 容器实例的状态
services: container-instances
author: seanmck
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/20/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 83c86d8310aff80f148e878261ba33b01846006b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441317"
---
# <a name="mount-an-azure-file-share-in-azure-container-instances"></a>在 Azure 容器实例中装载 Azure 文件共享

默认情况下，Azure 容器实例是无状态的。 如果容器崩溃或停止，其所有状态都会丢失。 若要将状态保持至超过容器寿命，必须从外部存储装载卷。 本文介绍如何装载 Azure 文件共享，以用于 Azure 容器实例。

> [!NOTE]
> 当前只有 Linux 容器能装载 Azure 文件共享。 我们正致力于为 Windows 容器提供全部功能，你可在 [Azure 容器实例的配额和区域可用性](container-instances-quotas.md)中了解当前的平台差异。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

必须先创建 Azure 文件共享，才能将其用于 Azure 容器实例。 运行以下脚本来创建存储帐户，以托管文件共享和共享本身。 存储帐户名必须是全局唯一的，因此该脚本会向基础字符串添加一个随机值。

```azurecli-interactive
# Change these four parameters as needed
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --location $ACI_PERS_LOCATION \
    --sku Standard_LRS

# Export the connection string as an environment variable. The following 'az storage share create' command
# references this environment variable when creating the Azure file share.
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string --resource-group $ACI_PERS_RESOURCE_GROUP --name $ACI_PERS_STORAGE_ACCOUNT_NAME --output tsv`

# Create the file share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="get-storage-credentials"></a>获取存储凭据

若要在 Azure 容器实例中将 Azure 文件共享装载为卷，需要 3 个值：存储帐户名、共享名和存储访问密钥。

如果使用上述脚本，则创建的存储帐户名末尾会带有一个随机值。 若要查询最终字符串（包括随机部分），请使用以下命令：

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" --output tsv)
echo $STORAGE_ACCOUNT
```

共享名已知（在上述脚本中定义为 acishare），因此剩下的就是找到存储帐户密钥，可使用以下命令：

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" --output tsv)
echo $STORAGE_KEY
```

## <a name="deploy-container-and-mount-volume"></a>部署容器并装载卷

若要将 Azure 文件共享作为卷装载到容器中，请在使用 [az container create][az-container-create] 创建容器时指定共享和卷装载点。 如果已执行前面的步骤，则可以使用以下命令装载先前创建的共享以创建容器：

```azurecli-interactive
az container create \
    --resource-group $ACI_PERS_RESOURCE_GROUP \
    --name hellofiles \
    --image microsoft/aci-hellofiles \
    --dns-name-label aci-demo \
    --ports 80 \
    --azure-file-volume-account-name $ACI_PERS_STORAGE_ACCOUNT_NAME \
    --azure-file-volume-account-key $STORAGE_KEY \
    --azure-file-volume-share-name $ACI_PERS_SHARE_NAME \
    --azure-file-volume-mount-path /aci/logs/
```

在创建容器实例时所在的 Azure 区域中，`--dns-name-label` 值必须是唯一的。 如果在执行命令时收到 DNS 名称标签错误消息，请更新前一命令中的值。

## <a name="manage-files-in-mounted-volume"></a>管理已装载卷中的文件

启动容器后，可使用通过 [microsoft/aci-hellofiles][aci-hellofiles] 映像部署的简单 Web 应用管理指定装载路径下 Azure 文件共享中的文件。 使用 [az container show][az-container-show] 命令获取 Web 应用的完全限定域名 (FQDN)：

```azurecli-interactive
az container show --resource-group $ACI_PERS_RESOURCE_GROUP --name hellofiles --query ipAddress.fqdn
```

可使用 [Azure 门户][portal]或 [Microsoft Azure 存储资源管理器][storage-explorer]之类的工具检索及检查写入到文件共享的文件。

## <a name="mount-multiple-volumes"></a>装载多个卷

若要将多个卷装载到容器实例中，必须使用 [Azure 资源管理器模板](/azure/templates/microsoft.containerinstance/containergroups)进行部署。

首先，提供共享详细信息，并通过在模板的 `properties` 部分填充 `volumes` 数组定义卷。 例如，如果已在存储帐户 myStorageAccount 中创建两个 Azure 文件存储（名为 share1 和 share2），`volumes` 数组将类似于：

```json
"volumes": [{
  "name": "myvolume1",
  "azureFile": {
    "shareName": "share1",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
},
{
  "name": "myvolume2",
  "azureFile": {
    "shareName": "share2",
    "storageAccountName": "myStorageAccount",
    "storageAccountKey": "<storage-account-key>"
  }
}]
```

接下来，针对容器组中希望装载卷的每个容器，在容器定义的 `properties` 部分填充 `volumeMounts` 数组。 例如，填充以下内容将装载之前定义的两个卷：myvolume1 和 myvolume2：

```json
"volumeMounts": [{
  "name": "myvolume1",
  "mountPath": "/mnt/share1/"
},
{
  "name": "myvolume2",
  "mountPath": "/mnt/share2/"
}]
```

若要查看使用 Azure 资源管理器模板进行的容器实例部署示例，请参阅[在 Azure 容器实例中部署多容器组](container-instances-multi-container-group.md)。

## <a name="next-steps"></a>后续步骤

了解如何在 Azure 容器实例中装载其他卷类型：

* [在 Azure 容器实例中装载 emptyDir 卷](container-instances-volume-emptydir.md)
* [在 Azure 容器实例中装载 gitRepo 卷](container-instances-volume-gitrepo.md)
* [在 Azure 容器实例中装载机密卷](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-hellofiles]: https://hub.docker.com/r/microsoft/aci-hellofiles/
[portal]: https://portal.azure.com
[storage-explorer]: https://storageexplorer.com

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
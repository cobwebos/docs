---
title: 将 Azure 文件与 AKS 配合使用
description: 将 Azure 磁盘与 AKS 配合使用
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 163db8fdaecefbf51174392ba37039115cdb91c8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
---
# <a name="volumes-with-azure-files"></a>含 Azure 文件的卷

基于容器的应用程序通常需要访问数据并将数据保存在外部数据卷中。 Azure 文件可以用作此外部数据存储。 本文详细介绍使用 Azure 文件作为 Azure Kubernetes 服务中的 Kubernetes 卷。

有关 Kubernetes 卷的详细信息，请参阅 [Kubernetes 卷][kubernetes-volumes]。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

在使用 Azure 文件共享作为 Kubernetes 卷之前，必须创建 Azure 存储帐户和文件共享。 可以使用以下脚本来完成这些任务。 记下或更新参数值，其中一些值在创建 Kubernetes 卷时需要。

```azurecli-interactive
#!/bin/bash

# Change these four parameters
AKS_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
AKS_PERS_RESOURCE_GROUP=myAKSShare
AKS_PERS_LOCATION=eastus
AKS_PERS_SHARE_NAME=aksshare

# Create the Resource Group
az group create --name $AKS_PERS_RESOURCE_GROUP --location $AKS_PERS_LOCATION

# Create the storage account
az storage account create -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -l $AKS_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $AKS_PERS_STORAGE_ACCOUNT_NAME -g $AKS_PERS_RESOURCE_GROUP -o tsv`

# Create the file share
az storage share create -n $AKS_PERS_SHARE_NAME

# Get storage account key
STORAGE_KEY=$(az storage account keys list --resource-group $AKS_PERS_RESOURCE_GROUP --account-name $AKS_PERS_STORAGE_ACCOUNT_NAME --query "[0].value" -o tsv)

# Echo storage account name and key
echo Storage account name: $AKS_PERS_STORAGE_ACCOUNT_NAME
echo Storgae account key: $STORAGE_KEY
```

## <a name="create-kubernetes-secret"></a>创建 Kubernetes 机密

Kubernetes 需要凭据才能访问文件共享。 这些凭据存储在 [Kubernetes 机密][kubernetes-secret]中，创建 Kubernetes Pod 时将引用它。

使用以下命令创建机密。 请将 `STORAGE_ACCOUNT_NAME` 替换为存储帐户名称，将 `STORAGE_ACCOUNT_KEY` 替换为存储密钥。

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=STORAGE_ACCOUNT_KEY
```

## <a name="mount-file-share-as-volume"></a>将文件共享装载为卷

通过在卷的规范中配置卷，可以将 Azure 文件共享装载到 pod。使用以下内容创建名为 `azure-files-pod.yaml` 的新文件。 使用提供给 Azure 文件共享的名称更新 `aksshare`。

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: aksshare
      readOnly: false
```

使用 kubectl 创建 pod。

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

现在你有一个正在运行的容器，其中 Azure 文件共享被装载到 `/mnt/azure` 目录中。  通过 `kubectl describe pod azure-files-pod` 检查 pod 时，可以看到此卷装载。

## <a name="next-steps"></a>后续步骤

使用 Azure 文件了解有关 Kubernetes 卷的详细信息。

> [!div class="nextstepaction"]
> [用于 Azure 文件的 Kubernetes 插件][kubernetes-files]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create

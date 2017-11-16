---
title: "将 Azure 文件与 AKS 配合使用 | Microsoft Docs"
description: "将 Azure 磁盘与 AKS 配合使用"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 11457e6556e6400d8f58f71c71ab1e790bcef8f1
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="using-azure-files-with-kubernetes"></a>将 Azure 文件与 Kubernetes 配合使用

基于容器的应用程序通常需要访问数据并将数据保存在外部数据卷中。 Azure 文件可以用作此外部数据存储。 本文详细介绍使用 Azure 文件作为 Azure 容器服务中的 Kubernetes 卷。

有关 Kubernetes 卷的详细信息，请参阅 [Kubernetes 卷][kubernetes-volumes]。

## <a name="creating-a-file-share"></a>创建文件共享

现有 Azure 文件共享可以与 Azure 容器服务配合使用。 如果需要创建一个文件共享，请使用以下命令集。

使用 [az group create][az-group-create] 命令创建 Azure 文件共享的资源组。 存储帐户的资源组和 Kubernetes 群集必须位于同一区域。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

可使用 [az storage account create][az-storage-create] 命令创建 Azure 存储帐户。 存储帐户名称必须是唯一的。 使用唯一值更新 `--name` 参数的值。

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

使用 [az storage account keys list ][az-storage-key-list] 命令返回存储密钥。 使用唯一的存储帐户名称更新 `--account-name` 参数的值。

记下密钥值之一，该值将用于后续步骤。

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

使用 [az storage share create][az-storage-share-create] 命令创建 Azure 文件共享。 使用在最后一步中收集的值更新 `--account-key` 值。

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>创建 Kubernetes 机密

Kubernetes 需要凭据才能访问文件共享。 Azure 存储帐户名称和密钥被一次性存储在 [Kubernetes 机密][kubernetes-secret]中，并由每个 Azure 文件卷引用，而不是通过每个 pod 进行存储。 

Kubernetes 机密清单中的值必须为 base64 编码的值。 使用以下命令返回已编码的值。

首先，对存储帐户的名称进行编码。 将 `storage-account` 替换为 Azure 存储帐户的名称。

```azurecli-interactive
echo -n <storage-account> | base64
```

其次，需要存储帐户访问密钥。 运行以下命令返回已编码的密钥。 将 `storage-key` 替换为在前面步骤中收集的密钥

```azurecli-interactive
echo -n <storage-key> | base64
```

创建名为 `azure-secret.yml` 的文件，并将其复制到以下 YAML 中。 使用在最后一步检索到的 base64 编码的值更新 `azurestorageaccountname` 和 `azurestorageaccountkey` 值。

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

使用 [kubectl apply][kubectl-apply] 命令创建机密。

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>将文件共享装载为卷

通过在卷的规范中配置卷，可以将 Azure 文件共享装载到 pod。使用以下内容创建名为 `azure-files-pod.yml` 的新文件。 使用提供给 Azure 文件共享的名称更新 `share-name`。

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: <share-name>
      readOnly: false
```

使用 kubectl 创建 pod。

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

现在你有一个正在运行的容器，其中 Azure 文件共享被装载到 `/mnt/azure` 目录中。 通过 `kubectl describe pod azure-files-pod` 检查 pod 时，可以看到此卷装载。

## <a name="next-steps"></a>后续步骤

使用 Azure 文件了解有关 Kubernetes 卷的详细信息。

> [!div class="nextstepaction"]
> [用于 Azure 文件的 Kubernetes 插件](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create
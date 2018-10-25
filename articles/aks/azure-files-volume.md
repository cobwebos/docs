---
title: 在 Azure Kubernetes 服务 (AKS) 中创建用于多个 Pod 的静态卷
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中使用 Azure 文件手动创建用于多个并发 Pod 的卷
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/08/2018
ms.author: iainfou
ms.openlocfilehash: 1a8609dbf5fa1c1e7d5f4e35b081ecaa09994eb6
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49068068"
---
# <a name="manually-create-and-use-a-volume-with-azure-files-share-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 中通过 Azure 文件共享手动创建并使用卷

基于容器的应用程序通常需要访问数据并将数据保存在外部数据卷中。 如果多个 Pod 需要同时访问同一存储卷，则可以使用 Azure 文件通过[服务器消息块 (SMB) 协议][smb-overview]进行连接。 本文介绍了如何手动创建 Azure 文件共享并将其附加到 AKS 中的 Pod。

有关 Kubernetes 卷的详细信息，请参阅 [Kubernetes 卷][kubernetes-volumes]。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.46 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

必须先创建 Azure 存储帐户和文件共享，然后才能将 Azure 文件共享用作 Kubernetes 卷。 以下脚本创建一个名为 *myAKSShare* 的资源组、一个存储帐户和一个名为 *aksshare* 的文件共享：

```azurecli
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
echo Storage account key: $STORAGE_KEY
```

记下脚本输出末尾显示的存储帐户名称和密钥。 在下面的步骤之一中创建 Kubernetes 卷时需要这些值。

## <a name="create-a-kubernetes-secret"></a>创建 Kubernetes 机密

Kubernetes 需要使用凭据访问上一步骤中创建的文件共享。 这些凭据存储在 [Kubernetes 机密][kubernetes-secret]中，创建 Kubernetes Pod 时将引用它。

使用 `kubectl create secret` 命令创建机密。 以下示例创建一个名为 *azure-secret* 的机密并填充上一步骤中的 *azurestorageaccountname* 和 *azurestorageaccountkey*。 若要使用现有 Azure 存储帐户，请提供帐户名称和密钥。

```console
kubectl create secret generic azure-secret --from-literal=azurestorageaccountname=$AKS_PERS_STORAGE_ACCOUNT_NAME --from-literal=azurestorageaccountkey=$STORAGE_KEY
```

## <a name="mount-the-file-share-as-a-volume"></a>将文件共享装载为卷

若要将 Azure 文件共享装载到 Pod 中，请在容器规范中配置卷。使用以下内容创建名为 `azure-files-pod.yaml` 的新文件。 如果更改了文件共享名称或机密名称，请更新 *shareName* 和 *secretName*。 如果需要，请更新 `mountPath`，这是文件共享在 Pod 中的装载路径。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.5
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
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

使用 `kubectl` 命令创建 Pod。

```azurecli-interactive
kubectl apply -f azure-files-pod.yaml
```

现在你有一个正在运行的 Pod，其中 Azure 文件共享装载在 */mnt/azure* 处。 可以使用 `kubectl describe pod mypod` 来验证共享是否已成功装载。 以下精简示例输出显示容器中装载的卷：

```
Containers:
  mypod:
    Container ID:   docker://86d244cfc7c4822401e88f55fd75217d213aa9c3c6a3df169e76e8e25ed28166
    Image:          nginx:1.15.5
    Image ID:       docker-pullable://nginx@sha256:9ad0746d8f2ea6df3a17ba89eca40b48c47066dfab55a75e08e2b70fc80d929e
    State:          Running
      Started:      Mon, 08 Oct 2018 19:28:34 +0000
    Ready:          True
    Mounts:
      /mnt/azure from azure (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-z5sd7 (ro)
[...]
Volumes:
  azure:
    Type:        AzureFile (an Azure File Service mount on the host and bind mount to the pod)
    SecretName:  azure-secret
    ShareName:   aksshare
    ReadOnly:    false
  default-token-z5sd7:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-z5sd7
[...]
```

## <a name="next-steps"></a>后续步骤

有关 AKS 群集与 Azure 文件进行交互的详细信息，请参阅 [Azure 文件的 Kubernetes 插件][kubernetes-files]。

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli

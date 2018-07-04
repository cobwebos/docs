---
title: 将 Azure 磁盘与 AKS 配合使用
description: 将 Azure 磁盘与 AKS 配合使用
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4af4620ff7a17cae76c4d5f2cf1a30ce4a3dccd8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597061"
---
# <a name="volumes-with-azure-disks"></a>含 Azure 磁盘的卷

基于容器的应用程序通常需要访问数据并将数据保存在外部数据卷中。 Azure 磁盘可以用作此外部数据存储。 本文详细介绍如何使用 Azure 磁盘作为 Azure Kubernetes 服务 (AKS) 群集中的 Kubernetes 卷。

有关 Kubernetes 卷的详细信息，请参阅 [Kubernetes 卷][kubernetes-volumes]。

## <a name="create-an-azure-disk"></a>创建 Azure 磁盘

在将 Azure 托管磁盘装载为 Kubernetes 卷之前，该磁盘必须位于 AKS 节点资源组中。 使用 [az resource show][az-resource-show] 命令获取资源组名称。

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

使用 [az disk create][az-disk-create] 命令创建 Azure 磁盘。

将 `--resource-group` 更新为上一个步骤中收集的资源组的名称，并将 `--name` 更新为你选择的名称。

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

创建磁盘后，你应该会看到与下面类似的输出。 此值为磁盘 ID，在装载磁盘时会有用。

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>装载磁盘作为卷

通过按容器规范配置卷，将 Azure 磁盘装载至 Pod。

使用以下内容创建名为 `azure-disk-pod.yaml` 的新文件。 将 `diskName` 更新为新创建的磁盘名称，并将 `diskURI` 更新为磁盘 ID。 此外，请记下 `mountPath`，这是 Azure 磁盘在 Pod 中的装载路径。

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

使用 kubectl 创建 Pod。

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

现在你有一个正在运行的 Pod，其中 Azure 磁盘被装载到 `/mnt/azure`。

## <a name="next-steps"></a>后续步骤

详细了解使用 Azure 磁盘的 Kubernetes 卷。

> [!div class="nextstepaction"]
> [用于 Azure 磁盘的 Kubernetes 插件][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show

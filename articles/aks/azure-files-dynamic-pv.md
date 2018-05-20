---
title: 将 Azure 文件与 AKS 配合使用
description: 将 Azure 磁盘与 AKS 配合使用
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 21245688076cf0a21164b549eb68bc6f55d6ec6c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="persistent-volumes-with-azure-files"></a>含 Azure 文件的持久卷

永久性卷表示已经过预配的可以在 Kubernetes 群集中使用的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 本文档详述了如何在 AKS 群集中将 Azure 文件共享动态预配为 Kubernetes 永久性卷。

有关 Kubernetes 永久性卷的详细信息，请参阅 [Kubernetes 永久性卷][kubernetes-volumes]。

## <a name="create-storage-account"></a>创建存储帐户

将 Azure 文件共享动态预配为 Kubernetes 卷时，可以使用任何存储帐户，只要该帐户与 AKS 群集包含在同一资源组中。 根据需要在 AKS 群集所在的资源组中创建一个存储帐户。

若要标识正确的资源组，请使用 [az group list][az-group-list] 命令。

```azurecli-interactive
az group list --output table
```

你在查找名称类似 `MC_clustername_clustername_locaton` 的资源组，其中 clustername 为 AKS 群集的名称，location 为部署群集的 Azure 区域。

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

可使用 [az storage account create][az-storage-account-create] 命令创建存储帐户。

使用此示例，将 `--resource-group` 更新为资源组的名称，并将 `--name` 更新为你选择的名称。

```azurecli-interactive
az storage account create --resource-group MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>创建存储类

存储类用于定义如何创建 Azure 文件共享。 可在此类中指定特定的存储帐户。 如果未指定存储帐户，必须指定 `skuName` 和 `location`，并评估关联的资源组中的所有存储帐户的匹配度。

有关 Azure 文件的 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

创建名为 `azure-file-sc.yaml` 的文件，并将其复制到以下清单中。 将 `storageAccount` 更新为目标存储帐户的名称。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: mystorageaccount
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类。

```azurecli-interactive
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-persistent-volume-claim"></a>创建永久性卷声明

永久性卷声明 (PVC) 使用存储类对象来动态预配 Azure 文件共享。

可以使用以下清单创建大小为 `5GB`、访问权限为 `ReadWriteOnce` 的永久性卷声明。

创建名为 `azure-file-pvc.yaml` 的文件，并将其复制到以下清单中。 请确保 `storageClassName` 与上一步骤中创建的存储类匹配。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

使用 [kubectl apply][kubectl-apply] 命令创建永久性卷声明。

```azurecli-interactive
kubectl apply -f azure-file-pvc.yaml
```

完成此步骤后，文件共享即创建完毕。 同时还会创建一个包含连接信息和凭据的 Kubernetes 机密。

## <a name="using-the-persistent-volume"></a>使用永久性卷

以下清单创建的 Pod 使用永久性卷声明 `azurefile` 将 Azure 文件共享装载到 `/mnt/azure` 路径。

创建名为 `azure-pvc-files.yaml` 的文件，并将其复制到以下清单中。 请确保 `claimName` 与上一步骤中创建的 PVC 匹配。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod。

```azurecli-interactive
kubectl apply -f azure-pvc-files.yaml
```

现在你有一个正在运行的 Pod，其中 Azure 磁盘被装载到 `/mnt/azure` 目录中。 通过 `kubectl describe pod mypod` 检查 pod 时，可以看到此配置。

## <a name="mount-options"></a>装载选项

默认的 fileMode 和 dirMode 值的 Kubernetes 版本有差异，如下表所述。

| 版本 | 值 |
| ---- | ---- |
| v1.6.x、v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 或更高版本 | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 或更高版本 | 0755 |

如果使用 1.8.5 或更高版本的群集，则可在存储类对象上指定装载选项。 以下示例设置 `0777`。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

如果使用版本为 1.8.0 - 1.8.4 的群集，则可在指定安全性上下文时，将 `runAsUser` 值设置为 `0`。 有关 Pod 安全性上下文的详细信息，请参阅[配置安全性上下文][kubernetes-security-context]。

## <a name="next-steps"></a>后续步骤

详细了解使用 Azure 文件的 Kubernetes 永久性卷。

> [!div class="nextstepaction"]
> [用于 Azure 文件的 Kubernetes 插件][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create

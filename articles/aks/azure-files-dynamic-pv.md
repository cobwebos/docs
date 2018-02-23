---
title: "将 Azure 文件与 AKS 配合使用"
description: "将 Azure 磁盘与 AKS 配合使用"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/04/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ce37cfdd70f95822a912f6ea71b9e4a3f9a30a14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2018
---
# <a name="persistent-volumes-with-azure-files"></a>含 Azure 文件的持久卷

永久性卷表示已经过预配的可以在 Kubernetes 群集中使用的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 本文档详述了如何在 AKS 群集中将 Azure 文件共享动态预配为 Kubernetes 永久性卷。 

有关 Kubernetes 永久性卷的详细信息，请参阅 [Kubernetes 永久性卷][kubernetes-volumes]。

## <a name="prerequisites"></a>先决条件

将 Azure 文件共享动态预配为 Kubernetes 卷时，可以使用任何存储帐户，只要该帐户与 AKS 群集包含在同一资源组中。 根据需要在 AKS 群集所在的资源组中创建一个存储帐户。 

若要标识正确的资源组，请使用 [az group list][az-group-list] 命令。

```azurecli-interactive
az group list --output table
```

以下示例输出显示了资源组，二者都与 AKS 群集相关联。 名称类似于 *MC_myAKSCluster_myAKSCluster_eastus* 的资源组包含 AKS 群集资源，是需要在其中创建存储帐户的地方。 

```
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

确定资源组以后，即可使用 [az storage account create][az-storage-account-create] 命令创建存储帐户。

```azurecli-interactive
az storage account create --resource-group  MC_myAKSCluster_myAKSCluster_eastus --name mystorageaccount --location eastus --sku Standard_LRS
```

## <a name="create-storage-class"></a>创建存储类

存储类用于定义动态创建的永久性卷的配置方法。 可以在存储类对象中定义 Azure 存储帐户名称、SKU、区域之类的项。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

以下示例指定，在请求存储时，可以使用 `eastus` 区域中 SKU 类型为 `Standard_LRS` 的任何存储帐户。 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  skuName: Standard_LRS
```

若要使用特定的存储帐户，可以使用 `storageAccount` 参数。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
parameters:
  storageAccount: azure_storage_account_name
```

## <a name="create-persistent-volume-claim"></a>创建永久性卷声明

永久性卷声明使用存储类对象来动态预配存储块。 使用 Azure 文件时，请在存储帐户（在存储类对象中选择或指定）中创建一个 Azure 文件共享。

>  [!NOTE]
>   确保已在 AKS 群集资源所在的资源组中预先创建了合适的存储帐户。 此资源组的名称类似于 *MC_myAKSCluster_myAKSCluster_eastus*。 如果存储帐户不可用，则永久性卷声明将无法预配 Azure 文件共享。 

可以使用以下清单创建大小为 `5GB`、访问权限为 `ReadWriteOnce` 的永久性卷声明。 有关 PVC 访问模式的详细信息，请参阅[访问模式][access-modes]。

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

## <a name="using-the-persistent-volume"></a>使用永久性卷

创建永久性卷声明并成功预配存储以后，即可创建可以访问卷的 Pod。 以下清单创建的 Pod 使用永久性卷声明 `azurefile` 将 Azure 文件共享装载到 `/var/www/html` 路径。 

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
      - mountPath: "/var/www/html"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azurefile
```

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
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#create
[kubectl-describe]: https://kubernetes-v1-4.github.io/docs/user-guide/kubectl/kubectl_describe/
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az_group_create
[az-group-list]: /cli/azure/group#az_group_list
[az-storage-account-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
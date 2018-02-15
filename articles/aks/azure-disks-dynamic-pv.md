---
title: "将 Azure 磁盘与 AKS 配合使用"
description: "将 Azure 磁盘与 AKS 配合使用"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 1/25/2018
ms.author: nepeters
ms.openlocfilehash: e1f5b68d5d39dd846ebec525d1e83a6c0ef4971a
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2018
---
# <a name="persistent-volumes-with-azure-disks---dynamic-provisioning"></a>Azure 磁盘的永久性卷 - 动态预配

永久性卷表示已经过预配的可以在 Kubernetes 群集中使用的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 本文档详细介绍如何在 AKS 群集中将 Azure 磁盘动态预配为 Kubernetes 永久性卷。 

有关 Kubernetes 永久性卷的详细信息，请参阅 [Kubernetes 永久性卷][kubernetes-volumes]。

## <a name="built-in-storage-classes"></a>内置存储类

存储类用于定义动态创建的永久性卷的配置方法。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

每个 AKS 群集包含两个预先创建的存储类，两者均配置为使用 Azure 磁盘。 使用 `kubectl get storageclass` 命令来进行查看。

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

如果这些存储类可满足你的需求，则无需创建新的存储类。

## <a name="create-storage-class"></a>创建存储类

如果要创建为 Azure 磁盘配置的新存储类，则可以使用下面的示例清单来完成操作。 

`Standard_LRS` 的 `storageaccounttype` 值指示已创建一个标准磁盘。 可将此值更改为 `Premium_LRS`，以创建[高级磁盘][premium-storage]。 要使用高级磁盘，AKS 节点虚拟机的大小必须与高级磁盘兼容。 参阅[本文档][premium-storage]获取兼容大小列表。

```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: azure-managed-disk
provisioner: kubernetes.io/azure-disk
parameters:
  kind: Managed
  storageaccounttype: Standard_LRS
```

## <a name="create-persistent-volume-claim"></a>创建永久性卷声明

永久性卷声明使用存储类对象来动态预配存储块。 使用 Azure 磁盘时，会在与 AKS 资源相同的资源组中创建磁盘。

此示例清单使用 `azure-managed-disk` 存储类来创建永久性卷声明，以创建具有 `ReadWriteOnce` 访问权限的 `5GB` 大小的磁盘。 有关 PVC 访问模式的详细信息，请参阅[访问模式][access-modes]。

> [!NOTE]
> Azure 磁盘只能使用访问模式类型 ReadWriteOnce 装载，这使其只可供单个 AKS 节点使用。 如果需要在多个节点之间共享持久卷，请考虑使用 [Azure 文件][azure-files-pvc]。 

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

## <a name="using-the-persistent-volume"></a>使用永久性卷

创建永久性卷声明并成功预配磁盘以后，即可创建可以访问磁盘的 Pod。 以下清单创建的 Pod 使用永久性卷声明 `azure-managed-disk` 将 Azure 磁盘装载到 `/var/www/html` 路径。 

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
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>后续步骤

详细了解使用 Azure 磁盘的 Kubernetes 永久性卷。

> [!div class="nextstepaction"]
> [用于 Azure 磁盘的 Kubernetes 插件][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
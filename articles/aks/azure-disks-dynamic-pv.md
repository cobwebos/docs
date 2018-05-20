---
title: 将 Azure 磁盘与 AKS 配合使用
description: 将 Azure 磁盘与 AKS 配合使用
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 858961db439b28a71d3475d2608073287e02f2fd
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2018
---
# <a name="persistent-volumes-with-azure-disks"></a>含 Azure 磁盘的永久性卷

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 有关 Kubernetes 永久性卷的详细信息，请参阅 [Kubernetes 永久性卷][kubernetes-volumes]。

本文档详细介绍如何在 Azure Kubernetes 服务 (AKS) 群集的 Azure 磁盘中使用永久性卷。

> [!NOTE]
> Azure 磁盘只能使用访问模式类型 ReadWriteOnce 装载，这使其只可供单个 AKS 节点使用。 如果需要在多个节点之间共享持久卷，请考虑使用 [Azure 文件][azure-files-pvc]。

## <a name="built-in-storage-classes"></a>内置存储类

存储类用于定义使用永久性卷动态创建存储单位的方式。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

每个 AKS 群集包含两个预先创建的存储类，两者均配置为使用 Azure 磁盘。 `default` 存储类可预配标准 Azure 磁盘。 `managed-premium` 存储类可预配高级 Azure 磁盘。 如果群集中的 AKS 节点使用高级存储，请选择 `managed-premium` 类。

使用 [kubectl get sc][kubectl-get] 命令查看预先创建的存储类。

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

## <a name="create-persistent-volume-claim"></a>创建永久性卷声明

永久卷声明 (PVC) 用于基于存储类自动预配存储。 在这种情况下，PVC 可以使用预先创建的存储类之一创建标准或高级 Azure 托管磁盘。

创建名为 `azure-premimum.yaml` 的文件，并将其复制到以下清单中。

请注意，`managed-premium` 存储类在注释中指定，该声明使用 `ReadWriteOnce` 访问权限请求大小为 `5GB` 的磁盘。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

使用 [kubectl apply][kubectl-apply] 命令创建永久性卷声明。

```azurecli-interactive
kubectl apply -f azure-premimum.yaml
```

## <a name="using-the-persistent-volume"></a>使用永久性卷

创建永久性卷声明并成功预配磁盘以后，即可创建可以访问磁盘的 Pod。 以下清单创建的 Pod 使用永久性卷声明 `azure-managed-disk` 将 Azure 磁盘装载到 `/mnt/azure` 路径。

创建名为 `azure-pvc-disk.yaml` 的文件，并将其复制到以下清单中。

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
        claimName: azure-managed-disk
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod。

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

现在你有一个正在运行的 Pod，其中 Azure 磁盘被装载到 `/mnt/azure` 目录中。 通过 `kubectl describe pod mypod` 检查 pod 时，可以看到此配置。

## <a name="next-steps"></a>后续步骤

详细了解使用 Azure 磁盘的 Kubernetes 永久性卷。

> [!div class="nextstepaction"]
> [用于 Azure 磁盘的 Kubernetes 插件][kubernetes-disk]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-disk]: https://kubernetes.io/docs/concepts/storage/storage-classes/#new-azure-disk-storage-class-starting-from-v172
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/

<!-- LINKS - internal -->
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
---
title: 使用 Azure Kubernetes 服务创建永久性卷
description: 了解如何使用 Azure 磁盘为 Azure Kubernetes 服务 (AKS) 中的 Pod 创建永久性卷
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 129a39294d52a312c91fc6a4fd009d76e88b4ff7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185216"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>使用 Azure 磁盘为 Azure Kubernetes 服务 (AKS) 创建永久性卷

永久性卷表示已经过预配可以用于 Kubernetes Pod 的存储块。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 有关 Kubernetes 永久性卷的详细信息，请参阅 [Kubernetes 永久性卷][kubernetes-volumes]。 本文介绍如何在 Azure Kubernetes 服务 (AKS) 群集的 Azure 磁盘中使用永久性卷。

> [!NOTE]
> Azure 磁盘只能使用“访问模式”类型 ReadWriteOnce 装载，这使其只可供单个 AKS 节点使用。 如果需要在多个节点之间共享持久卷，请考虑使用 [Azure 文件][azure-files-pvc]。

## <a name="built-in-storage-classes"></a>内置存储类

存储类用于定义使用永久性卷动态创建存储单位的方式。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

每个 AKS 群集包含两个预先创建的存储类，两者均配置为使用 Azure 磁盘：

* default 存储类可预配标准 Azure 磁盘。
    * 标准存储受 HDD 支持，可以在确保性能的同时提供经济高效的存储。 标准磁盘适用于经济高效的开发和测试工作负荷。
* managed-premium 存储类可预配高级 Azure 磁盘。
    * 高级磁盘由基于 SSD 的高性能、低延迟磁盘提供支持。 完美适用于运行生产工作负荷的 VM。 如果群集中的 AKS 节点使用高级存储，请选择 managed-premium 类。

使用 [kubectl get sc][kubectl-get] 命令查看预先创建的存储类。 以下示例显示了 AKS 群集中可用的预先创建存储类：

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> 永久卷声明在 GiB 中指定，但 Azure 托管磁盘由 SKU 针对特定大小计费。 这些 SKU 的范围从用于 S4 或 P4 磁盘的 32 GiB 到用于 S50 或 P50 磁盘的 4 TiB。 高级托管磁盘的吞吐量和 IOPS 性能取决于 SKU 和 AKS 群集中节点的实例大小。 有关详细信息，请参阅[托管磁盘的定价和性能][managed-disk-pricing-performance]。

## <a name="create-a-persistent-volume-claim"></a>创建永久性卷声明

永久卷声明 (PVC) 用于基于存储类自动预配存储。 在这种情况下，PVC 可以使用预先创建的存储类之一创建标准或高级 Azure 托管磁盘。

创建名为 `azure-premium.yaml` 的文件，并将其复制到以下清单中。 该声明请求名为 `azure-managed-disk`、大小为 *5 GB*、具有 *ReadWriteOnce* 访问权限的磁盘。 *managed-premium* 存储类指定为存储类。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

> [!TIP]
> 若要创建使用标准存储的磁盘，请使用 `storageClassName: default` 而不是 *managed-premium*。

使用 [kubectl create][kubectl-create] 命令创建永久性卷声明，并指定 *azure-premium.yaml* 文件：

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>使用永久性卷

创建永久性卷声明并成功预配磁盘以后，即可创建可以访问磁盘的 Pod。 以下清单创建的基本 NGINX Pod 使用名为 *azure-managed-disk* 的永久性卷声明将 Azure 磁盘装载到 `/mnt/azure` 路径。

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

使用 [kubectl create][kubectl-create] 命令创建 Pod，如以下示例所示：

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

现在你有一个正在运行的 Pod，其中 Azure 磁盘被装载到 `/mnt/azure` 目录中。 通过 `kubectl describe pod mypod` 检查 Pod 时可以看到此配置，如以下精简示例所示：

```
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>备份永久性卷

若要备份持久卷中的数据，请为该卷的托管磁盘创建快照。 然后，可以使用此快照创建还原的磁盘，并通过还原数据的方式附加到 Pod。

首先，使用 `kubectl get pvc` 命令获取卷名称，例如，获取 PVC 名称 *azure-managed-disk*：

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

此卷名称构成了基础 Azure 磁盘名称。 使用 [az disk list][az-disk-list] 查询磁盘 ID 并提供 PVC 卷名称，如以下示例所示：

```
$ az disk list --query '[].id|[?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

运行 [az snapshot create][az-snapshot-create]，使用磁盘 ID 创建快照磁盘。 以下示例在 AKS 群集所在的同一资源组 (*MC_myResourceGroup_myAKSCluster_eastus*) 中创建名为 *pvcSnapshot* 的快照。 如果在 AKS 群集无权访问的资源组中创建快照和还原磁盘，可能会遇到权限问题。

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

根据磁盘上的数据量，可能需要花费几分钟时间来创建快照。

## <a name="restore-and-use-a-snapshot"></a>还原并使用快照

若要还原磁盘并将其用于 Kubernetes Pod，请在使用 [az disk create][az-disk-create] 创建磁盘时，将快照用作源。 如果以后需要访问原始数据快照，此操作可保留原始资源。 以下示例基于名为 *pvcSnapshot* 的快照创建名为 *pvcRestored* 的磁盘：

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

若要使用包含 Pod 的已还原磁盘，请指定清单中磁盘的 ID。 使用 [az disk show][az-disk-show] 命令获取磁盘 ID。 以下示例获取上一步骤中创建的 *pvcRestored* 的磁盘 ID：

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

创建名为 `azure-restored.yaml` 的 Pod 清单，并指定上一步骤中获取的磁盘 URI。 以下示例创建一个基本的 NGINX Web 服务器，其中的 *mnt/azure* 处已将还原的磁盘装载为卷：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

使用 [kubectl create][kubectl-create] 命令创建 Pod，如以下示例所示：

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

可以使用 `kubectl describe pod mypodrestored` 查看 Pod 详细信息，例如，以下精简示例显示了卷信息：

```
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>后续步骤

详细了解使用 Azure 磁盘的 Kubernetes 永久性卷。

> [!div class="nextstepaction"]
> [用于 Azure 磁盘的 Kubernetes 插件][azure-disk-volume]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show

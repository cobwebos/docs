---
title: '使用容器存储接口 (CSI) Azure 上的 Azure 磁盘驱动程序 (AKS) '
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中使用 Azure 磁盘 (CSI) 驱动程序的容器存储接口。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: 4b5ccd2712a95f5f020daa0161f1b5908a38a62e
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422019"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>使用 azure Kubernetes Service 中的 Azure 磁盘容器存储接口 (CSI) 驱动程序 (AKS)  (预览版) 
Azure 磁盘 CSI 驱动程序是 AKS 用于管理 Azure 磁盘生命周期的 [CSI 规范](https://github.com/container-storage-interface/spec/blob/master/spec.md) 兼容驱动程序。 

 (CSI) 的容器存储接口是一种将任意块和文件存储系统公开给 Kubernetes 上容器化工作负荷的标准。 通过采用和使用 CSI，Azure Kubernetes Service (AKS) 可以在 Kubernetes 中编写、部署和循环访问现有存储系统，而无需接触核心 Kubernetes 代码并等待其发布周期。

若要创建具有 CSI 驱动程序支持的 AKS 群集，请参阅 [在 AKS 上为 Azure 磁盘和 Azure 文件启用 CSI 驱动程序](csi-storage-drivers.md)。

>[!NOTE]
> *"树内驱动程序"* 是指核心 kubernetes 代码与作为插件的新 CSI 驱动程序的一部分的当前存储驱动程序。

## <a name="use-csi-persistent-volumes-pv-with-azure-disks"></a>将 CSI 永久性卷用于 Azure 磁盘 (PV)  

[永久性卷](concepts-storage.md#persistent-volumes)表示为与 Kubernetes pod 一起使用而预配的一段存储。 永久性卷可供一个或多个 Pod 使用，并可动态或静态预配。 本文介绍如何使用 Azure 磁盘动态创建永久性卷，以供 Azure Kubernetes 服务 (AKS) 群集中的单个 Pod 使用。 有关静态设置，请参阅 [手动创建和使用 Azure 磁盘的卷](azure-disk-volume.md)。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

有关 Kubernetes 卷的详细信息，请参阅 [AKS 中应用程序的存储选项][concepts-storage]。

## <a name="dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes"></a>使用内置存储类动态创建 Azure 磁盘 PVs

存储类用于定义使用永久性卷动态创建存储单位的方式。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。 在 AKS 上使用存储 CSI 驱动程序时，有2个额外 `StorageClasses` 的内置利用了 **AZURE 磁盘 CSI 存储驱动程序**。 其他 CSI 存储类随群集的默认存储类一起创建。

>[!NOTE]
> *"树内驱动程序"* 是指作为插件的核心 kubernetes 代码与 CSI 驱动程序的一部分的当前存储驱动程序。

- `managed-csi` -使用 Azure StandardSSD 本地冗余存储 (LRS) 创建托管磁盘。
- `managed-csi-premium` -使用 Azure 高级本地冗余存储 (LRS) 创建托管磁盘。 

这两个存储类中的回收策略可确保在删除相应的持久卷时删除基础 Azure 磁盘。 存储类还会将永久性卷配置为可扩展，只需编辑具有新大小的永久性卷声明。

若要利用这些存储类，只需创建一个 [永久性卷声明 (PVC) ](concepts-storage.md#persistent-volume-claims) ，以及引用并利用它们的相应 pod。 永久卷声明 (PVC) 用于基于存储类自动预配存储。 PVC 可以使用预先创建的存储类之一或用户定义存储类来创建 Azure 托管磁盘，以获得所需的 SKU 和大小。 创建 Pod 定义时，将指定永久性卷声明来请求所需的存储。

使用 [kubectl apply][kubectl-apply] 命令创建示例 pod 和各自的永久性卷声明：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/pvc-azuredisk-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

persistentvolumeclaim/pvc-azuredisk created
pod/nginx-azuredisk created
```

在 pod 处于运行状态后，创建一个名为的新文件 `test.txt` 。

```bash
$ kubectl exec nginx-azuredisk -- touch /mnt/azuredisk/test.txt
```

你现在可以通过运行以下命令并验证是否在输出中看到该文件，来验证磁盘是否已正确装载 `test.txt` ： 

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>创建自定义存储类

默认存储类适合最常见的方案，但并非全部。 在某些情况下，你可能希望使用自己的参数自定义自己的存储类。 到求知欲，我们有一个方案，你可能需要更改 `volumeBindingMode` 。 

默认存储类使用 `volumeBindingMode: Immediate` 保证在创建 PersistentVolumeClaim 后立即发生的。 如果节点池属于拓扑约束（例如，使用可用性区域），则会在不知道 Pod 计划要求的情况下绑定或预配永久性卷 (在这种情况下，会在) 特定区域。

若要解决这种情况，可以使用 `volumeBindingMode: WaitForFirstConsumer` ，这会延迟 PersistentVolume 的绑定和预配，直到使用 PersistentVolumeClaim 创建一个 Pod。 通过这种方式，PV 将符合并在可用性区域中进行设置 (或由 Pod 的计划约束指定的其他拓扑) 。 

创建一个名为 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 的文件，并粘贴下面的清单。
存储类与存储类相同， `managed-csi` 但具有不同的 `volumeBindingMode` 。 

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azuredisk-csi-waitforfirstconsumer
provisioner: disk.csi.azure.com
parameters:
  skuname: StandardSSD_LRS 
allowVolumeExpansion: true
reclaimPolicy: Delete
volumeBindingMode: WaitForFirstConsumer
```

使用 [kubectl 应用][kubectl-apply] 命令创建存储类并指定 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 文件：

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>卷快照

Azure 磁盘 CSI 驱动程序支持创建 [持久卷的快照](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)。 作为此功能的一部分，驱动程序可以执行 *完整* 或 [*增量* 快照](../virtual-machines/windows/disks-incremental-snapshots.md) ，具体取决于参数 (中设置的值， `incremental` 默认情况下为 true) 。 

有关所有参数的详细信息，请参阅 [卷快照类参数](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)。

### <a name="create-a-volume-snapshot"></a>创建卷快照

若要求知欲此功能，请使用[kubectl apply][kubectl-apply]命令创建[卷快照类](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

现在，让我们从[我们在本教程开头动态创建](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes)的 PVC 创建[卷快照](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) `pvc-azuredisk` 。


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml

volumesnapshot.snapshot.storage.k8s.io/azuredisk-volume-snapshot created
```

检查快照是否已正确创建：

```bash
$ kubectl describe volumesnapshot azuredisk-volume-snapshot

Name:         azuredisk-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T05:27:58Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  714582
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azuredisk-volume-snapshot
  UID:               dd953ab5-6c24-42d4-ad4a-f33180e0ef87
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azuredisk
  Volume Snapshot Class Name:      csi-azuredisk-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-dd953ab5-6c24-42d4-ad4a-f33180e0ef87
  Creation Time:                       2020-08-31T05:27:59Z
  Ready To Use:                        true
  Restore Size:                        10Gi
Events:                                <none>
```

### <a name="create-a-new-pvc-based-on-a-volume-snapshot"></a>基于卷快照创建新的 PVC

可以基于卷快照创建新的 PVC。 使用在上一步创建的快照，并创建 [新的 PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) 和 [新的 pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) 来使用它。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

最后，请确保它是之前创建的 PVC，通过检查内容来创建。

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

如果需要，我们仍然可以看到我们先前创建 `test.txt` 的文件。

## <a name="clone-volumes"></a>克隆卷

克隆的卷定义为现有 Kubernetes 卷的副本。 有关在 Kubernetes 中克隆卷的详细信息，请参阅有关 [卷克隆](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)的概念文档。

适用于 Azure 磁盘的 CSI 驱动程序支持卷克隆。 若要演示，请创建[之前创建](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes)的[克隆卷](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) `azuredisk-pvc` ，并使用[新的 pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)。



```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

现在，我们可以通过运行以下内容来检查克隆卷的内容，确认仍然会看到 `test.txt` 创建的文件。

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume-pv"></a>调整持久卷的大小 (PV) 

可以改为为 PVC 请求更大的卷。 编辑 PVC 对象并指定更大的大小。 此更改触发对 PersistentVolume 的基础卷的扩展。 

> [!NOTE] 
> 永远不会创建新的 PersistentVolume 来满足声明。 相反，会调整现有卷的大小。

在 AKS 中，内置 `managed-csi` 存储类已允许进行扩展，因此利用 [之前使用此存储类创建的 PVC](#dynamically-create-azure-disk-pvs-using-the-built-in-storage-classes)。 PVC 请求了10Gi 永久性卷，我们可以通过运行以下内容来确认：

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```
> [!IMPORTANT]
> 目前，Azure 磁盘 CSI 驱动程序只支持将没有与 (关联的 pod 和未装载到特定节点) 的的工作程序大小调整。

这样，便可以删除之前创建的 pod：

```console
$ kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod "nginx-azuredisk" deleted
```

让我们通过增加字段来展开 PVC `spec.resources.requests.storage` ：

```console
$ kubectl patch pvc pvc-azuredisk --type merge --patch '{"spec": {"resources": {"requests": {"storage": "15Gi"}}}}'

persistentvolumeclaim/pvc-azuredisk patched
```

让我们确认该卷现在是否更大：

```console
$ kubectl get pv

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                                     STORAGECLASS   REASON   AGE
pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            Delete           Bound    default/pvc-azuredisk                     managed-csi             2d2h
(...)
```

> [!NOTE] 
> PVC 不会反映新大小，直到它再次与它关联。

让我们创建一个新的 pod：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/nginx-pod-azuredisk.yaml

pod/nginx-azuredisk created
```

最后，确认 PVC 和 pod 内的大小： 
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

<!--- ## Shared disk

[Azure shared disks](../virtual-machines/windows/disks-shared.md) is an Azure managed disks feature that enables attaching an Azure disk to agent nodes simultaneously. Attaching a managed disk to multiple agent nodes allows you, for example, to deploy new or migrate existing clustered applications to Azure.

> [!IMPORTANT] Currently, only raw block device (`volumeMode: Block`) is supported by the Azure disk CSI driver. Applications should manage the coordination and control of writes, reads, locks, caches, mounts and fencing on the shared disk which is exposed as raw block device.

Let's create file called `shared-disk.yaml` by copying the below that contains the shared disk storage class and PVC:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-csi-shared
provisioner: disk.csi.azure.com
parameters:
  skuname: Premium_LRS  # Currently shared disk is only available with premium SSD
  maxShares: "2"
  cachingMode: None  # ReadOnly cache is not available for premium SSD with maxShares>1
reclaimPolicy: Delete
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-azuredisk-shared
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Gi  # minimum size of shared disk is 256GB (P15)
  volumeMode: Block
  storageClassName: managed-csi-shared
```

Create the storage class with the [kubectl apply][kubectl-apply] command and specify your `shared-disk.yaml` file:

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

Now let's create a file called `deployment-shared.yml` by copying the below:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: deployment-azuredisk
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
      name: deployment-azuredisk
    spec:
      containers:
        - name: deployment-azuredisk
          image: nginx
          volumeDevices:
            - name: azuredisk
              devicePath: /dev/sdx
      volumes:
        - name: azuredisk
          persistentVolumeClaim:
            claimName: pvc-azuredisk-shared
```

Create the deployment with the [kubectl apply][kubectl-apply] command and specify your `deployment-shared.yml` file:

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

Finally, let's check the block device inside the pod:

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```
-->

## <a name="windows-containers"></a>Windows 容器

Azure 磁盘 CSI 驱动程序还支持 Windows 节点和容器。 如果要使用 windows 容器，请按照 [Windows 容器教程](windows-container-cli.md) 添加 windows 节点池。

使用 windows 节点池后，现在可以只利用内置的存储类，如 `managed-csi` 。 你可以部署 [基于 windows 的有状态集](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) 示例，它 `data.txt` 通过使用 [kubectl apply][kubectl-apply] 命令部署以下命令将时间戳保存到文件中：

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azuredisk created
```

你现在可以通过运行以下内容来验证卷的内容：

```console
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\\mnt\\azuredisk\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azuredisk-0 -- cat c:\mnt\azuredisk\data.txt # on Windows Powershell/CMD

2020-08-27 08:13:41Z
2020-08-27 08:13:42Z
2020-08-27 08:13:44Z
(...)
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用适用于 Azure 文件的 CSI 驱动程序，请参阅 [将 azure 文件与 csi 驱动程序配合使用](azure-files-csi.md)。
- 有关存储最佳实践的详细信息，请参阅 [Azure Kubernetes Service 中存储和备份的最佳实践 (AKS) ][operator-best-practices-storage]


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/disks-types.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
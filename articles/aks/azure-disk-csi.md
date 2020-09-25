---
title: '使用容器存储接口 (CSI) Azure 上的 Azure 磁盘驱动程序 (AKS) '
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中使用 Azure 磁盘 (CSI) 驱动程序的容器存储接口。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: dfbef8da1349c2b86595f520e173aee9d455e3a4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299572"
---
# <a name="use-the-azure-disk-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>使用 azure Kubernetes Service 中的 Azure 磁盘容器存储接口 (CSI) 驱动程序 (AKS)  (预览版) 
Azure 磁盘容器存储接口 (CSI) 驱动程序是一个符合 [CSI 规范](https://github.com/container-storage-interface/spec/blob/master/spec.md)的驱动程序，由 Azure Kubernetes SERVICE (AKS) 用于管理 azure 磁盘的生命周期。

CSI 是一种将任意块和文件存储系统公开给 Kubernetes 上容器化工作负荷的标准。 通过采用和使用 CSI，AKS 可以在 Kubernetes 中编写、部署和循环访问现有存储系统，而无需接触核心 Kubernetes 代码并等待其发布周期。

若要创建具有 CSI 驱动程序支持的 AKS 群集，请参阅 [在 AKS 上为 azure 磁盘和 Azure 文件启用 CSI 驱动程序](csi-storage-drivers.md)。

>[!NOTE]
> *树内驱动程序* 指作为核心 Kubernetes 代码的一部分的当前存储驱动程序与新的 CSI 驱动程序（即插件）。

## <a name="use-csi-persistent-volumes-with-azure-disks"></a>将 CSI 永久性卷用于 Azure 磁盘

[永久性卷](concepts-storage.md#persistent-volumes) (PV) 表示预配的一段存储，用于 Kubernetes pod。 PV 可由一个或多个 pod 使用，并可动态或静态预配。 本文介绍如何使用 Azure 磁盘动态创建 PVs，以供 AKS 群集中的单个 pod 使用。 有关静态设置，请参阅 [手动创建和使用 Azure 磁盘的卷](azure-disk-volume.md)。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

有关 Kubernetes 卷的详细信息，请参阅 [AKS 中应用程序的存储选项][concepts-storage]。

## <a name="dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes"></a>使用内置存储类动态创建 Azure 磁盘 PVs

存储类用于定义使用永久性卷动态创建存储单位的方式。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。 在 AKS 上使用存储 CSI 驱动程序时，有两个 `StorageClasses` 使用 Azure 磁盘 CSI 存储驱动程序的附加内置。 其他 CSI 存储类随群集的默认存储类一起创建。

- `managed-csi`：使用 Azure 标准 SSD 本地冗余存储 (LRS) 来创建托管磁盘。
- `managed-csi-premium`：使用 Azure Premium LRS 创建托管磁盘。

这两个存储类中的回收策略可确保删除相应的 PV 时删除基础 Azure 磁盘。 存储类还会将 PVs 配置为可展开。 只需) 具有新大小的 PVC 编辑永久性卷声明 (。

若要利用这些存储类，请创建一个 [PVC](concepts-storage.md#persistent-volume-claims) ，并使用它们来引用和使用它们。 PVC 用于基于存储类自动预配存储。 PVC 可以使用预先创建的存储类之一或用户定义存储类来创建 Azure 托管磁盘，以获得所需的 SKU 和大小。 创建 pod 定义时，将指定 PVC 来请求所需的存储。

使用 [kubectl apply][kubectl-apply] 命令创建示例 pod 和各自的 PVC：

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

你现在可以通过运行以下命令来验证磁盘是否已正确装载，并验证是否 `test.txt` 在输出中看到了该文件：

```console
$ kubectl exec nginx-azuredisk -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="create-a-custom-storage-class"></a>创建自定义存储类

默认存储类适合最常见的方案，但并非全部。 在某些情况下，你可能希望使用自己的参数自定义自己的存储类。 例如，我们有一个方案，你可能想要更改 `volumeBindingMode` 类。

默认存储类使用 `volumeBindingMode: Immediate` 可保证在创建 PVC 后立即发生的类。 如果你的节点池属于拓扑约束（例如，使用可用性区域），则在此情况下，将绑定或设置 PVs，而无需 (知道此情况下) 的特定区域。

若要解决这种情况，可以使用 `volumeBindingMode: WaitForFirstConsumer` ，它会在创建使用 PVC 的 pod 之前延迟对 PV 的绑定和设置。 通过这种方式，PV 将符合并在可用性区域中进行设置 (或由 pod 的计划约束指定的其他拓扑) 。

创建一个名为 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 的文件，并粘贴以下清单。
存储类与存储类相同， `managed-csi` 但具有不同的 `volumeBindingMode` 类。

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

使用 [kubectl apply][kubectl-apply] 命令创建存储类，并指定 `sc-azuredisk-csi-waitforfirstconsumer.yaml` 文件：

```console
$ kubectl apply -f sc-azuredisk-csi-waitforfirstconsumer.yaml

storageclass.storage.k8s.io/azuredisk-csi-waitforfirstconsumer created
```

## <a name="volume-snapshots"></a>卷快照

Azure 磁盘 CSI 驱动程序支持创建 [持久卷的快照](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html)。 作为此功能的一部分，驱动程序可以执行 *完全* 或 [*增量* 快照](../virtual-machines/windows/disks-incremental-snapshots.md) ，具体取决于参数 (中设置的值 `incremental` 默认情况下为 true) 。

有关所有参数的详细信息，请参阅 [卷快照类参数](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/driver-parameters.md#volumesnapshotclass)。

### <a name="create-a-volume-snapshot"></a>创建卷快照

有关此功能的示例，请使用[kubectl 应用][kubectl-apply]命令创建[卷快照类](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/storageclass-azuredisk-snapshot.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azuredisk-vsc created
```

现在，让我们从[我们在本教程开头动态创建](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)的 PVC 创建[卷快照](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/azuredisk-volume-snapshot.yaml) `pvc-azuredisk` 。


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

可以基于卷快照创建新的 PVC。 使用在上一步中创建的快照，并创建 [新的 PVC](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml) 和 [新的 pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml) 来使用它。

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/pvc-azuredisk-snapshot-restored.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/snapshot/nginx-pod-restored-snapshot.yaml

persistentvolumeclaim/pvc-azuredisk-snapshot-restored created
pod/nginx-restored created
```

最后，请确保它是之前创建的 PVC，然后再检查内容。

```console
$ kubectl exec nginx-restored -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

按预期，我们仍然可以看到以前创建的 `test.txt` 文件。

## <a name="clone-volumes"></a>克隆卷

克隆的卷定义为现有 Kubernetes 卷的副本。 有关在 Kubernetes 中克隆卷的详细信息，请参阅有关 [卷克隆](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-cloning)的概念文档。

适用于 Azure 磁盘的 CSI 驱动程序支持卷克隆。 若要演示，请创建[之前创建](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)的[克隆卷](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml) `azuredisk-pvc` ，并使用[新的 pod](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml)。


```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/pvc-azuredisk-cloning.yaml

$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/cloning/nginx-pod-restored-cloning.yaml

persistentvolumeclaim/pvc-azuredisk-cloning created
pod/nginx-restored-cloning created
```

现在，我们可以通过运行以下命令来检查克隆卷的内容，并确认仍会看到 `test.txt` 创建的文件。

```console
$ kubectl exec nginx-restored-cloning -- ls /mnt/azuredisk

lost+found
outfile
test.txt
```

## <a name="resize-a-persistent-volume"></a>调整持久卷的大小

可以改为为 PVC 请求更大的卷。 编辑 PVC 对象，并指定更大的大小。 此更改触发了支持 PV 的基础卷的扩展。

> [!NOTE]
> 永远不会创建新的 PV 来满足声明。 相反，会调整现有卷的大小。

在 AKS 中，内置 `managed-csi` 存储类已允许进行扩展，因此请使用先前使用 [此存储类创建的 PVC](#dynamically-create-azure-disk-pvs-by-using-the-built-in-storage-classes)。 PVC 请求了10个 Gi 永久性卷。 可以通过运行以下内容来确认：

```console 
$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk

Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc        9.8G   42M  9.8G   1% /mnt/azuredisk
```

> [!IMPORTANT]
> 目前，Azure 磁盘 CSI 驱动程序只支持将没有与 (关联的 pod 和未装载到特定节点) 的的工作程序大小调整。

因此，让我们删除前面创建的 pod：

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

最后，确认 PVC 的大小以及 pod 内的内容：
```console
$ kubectl get pvc pvc-azuredisk
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-azuredisk   Bound    pvc-391ea1a6-0191-4022-b915-c8dc4216174a   15Gi       RWO            managed-csi    2d2h

$ kubectl exec -it nginx-azuredisk -- df -h /mnt/azuredisk
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdc         15G   46M   15G   1% /mnt/azuredisk
```

## <a name="shared-disk"></a>共享磁盘

[Azure 共享磁盘](../virtual-machines/windows/disks-shared.md) 是一项 azure 托管磁盘功能，可同时将 azure 磁盘附加到代理节点。 例如，将托管磁盘附加到多个代理节点可以将现有的群集应用程序部署到 Azure 或将其迁移到 Azure。

> [!IMPORTANT] 
> 目前， `volumeMode: Block` Azure 磁盘 CSI 驱动程序仅支持原始块设备 () 。 应用程序应管理共享磁盘上的写入、读取、锁、缓存、装载和防护的协调和控制，它作为原始块设备公开。

让我们 `shared-disk.yaml` 通过复制以下包含共享磁盘存储类和 PVC 的命令来创建一个名为的文件：

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

使用 [kubectl apply][kubectl-apply] 命令创建存储类，并指定 `shared-disk.yaml` 文件：

```console
$ kubectl apply -f shared-disk.yaml

storageclass.storage.k8s.io/managed-csi-shared created
persistentvolumeclaim/pvc-azuredisk-shared created
``` 

现在，让我们 `deployment-shared.yml` 通过复制以下命令来创建一个名为的文件：

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

使用 [kubectl apply][kubectl-apply] 命令创建部署，并指定 `deployment-shared.yml` 文件：

```console
$ kubectl apply -f deployment-shared.yml

deployment/deployment-azuredisk created
```

最后，让我们检查 pod 内的块设备：

```console
# kubectl exec -it deployment-sharedisk-7454978bc6-xh7jp bash
root@deployment-sharedisk-7454978bc6-xh7jp:/# dd if=/dev/zero of=/dev/sdx bs=1024k count=100
100+0 records in
100+0 records out
104857600 bytes (105 MB, 100 MiB) copied, 0.0502999 s, 2.1 GB/s
```

## <a name="windows-containers"></a>Windows 容器

Azure 磁盘 CSI 驱动程序还支持 Windows 节点和容器。 如果要使用 Windows 容器，请按照 [windows 容器教程](windows-container-cli.md) 添加 windows 节点池。

使用 Windows 节点池后，现在可以使用内置的存储类，如 `managed-csi` 。 你可以部署 [基于 Windows 的有状态集](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) 示例，该程序集 `data.txt` 通过使用 [kubectl apply][kubectl-apply] 命令部署以下命令将时间戳保存到文件中：

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

- 若要了解如何将 CSI 驱动程序用于 Azure 文件，请参阅 [将 Azure 文件与 csi 驱动程序配合使用](azure-files-csi.md)。
- 有关存储最佳实践的详细信息，请参阅 [Azure Kubernetes 服务中存储和备份的最佳实践][operator-best-practices-storage]。


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
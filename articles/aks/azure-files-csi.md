---
title: '使用容器存储接口 (CSI) Azure 上的 Azure 文件的驱动程序 (AKS) '
description: 了解如何在 Azure Kubernetes 服务中使用 Azure 文件 (CSI) 驱动程序 (AKS) 群集中使用容器存储接口。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: d845e7589b57bf76d3da48c48fa0a520b09e1f94
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91299300"
---
# <a name="use-azure-files-container-storage-interface-csi-drivers-in-azure-kubernetes-service-aks-preview"></a>使用 azure 文件容器存储接口 (CSI) Azure Kubernetes Service 中的驱动程序 (AKS)  (预览版) 

Azure 文件容器存储接口 (CSI) 驱动程序是一个 [csi 规范](https://github.com/container-storage-interface/spec/blob/master/spec.md)兼容的驱动程序，由 Azure Kubernetes SERVICE (AKS) 用于管理 azure 文件共享的生命周期。

CSI 是一种将任意块和文件存储系统公开给 Kubernetes 上容器化工作负荷的标准。 通过采用和使用 CSI，AKS 现在可以编写、部署和循环访问插件以在 Kubernetes 中公开新的或改进现有存储系统，而无需接触核心 Kubernetes 代码并等待其发布周期。

若要创建具有 CSI 驱动程序支持的 AKS 群集，请参阅 [在 AKS 上为 azure 磁盘和 Azure 文件启用 CSI 驱动程序](csi-storage-drivers.md)。

>[!NOTE]
> *树内驱动程序* 指作为核心 Kubernetes 代码的一部分的当前存储驱动程序与新的 CSI 驱动程序（即插件）。

## <a name="use-a-persistent-volume-with-azure-files"></a>使用 Azure 文件的持久卷

[永久性卷 (PV) ](concepts-storage.md#persistent-volumes)表示预配的一段存储，用于 Kubernetes pod。 PV 可由一个或多个 pod 使用，并可动态或静态预配。 如果多个 pod 需要同时访问同一存储卷，则可以使用 Azure 文件通过使用 [服务器消息块 (SMB) 协议][smb-overview]进行连接。 本文介绍如何动态创建 Azure 文件共享以供 AKS 群集中多个 pod 使用。 有关静态设置，请参阅 [手动创建和使用具有 Azure 文件共享的卷](azure-files-volume.md)。

有关 Kubernetes 卷的详细信息，请参阅 [AKS 中应用程序的存储选项][concepts-storage]。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes"></a>使用内置存储类动态创建 Azure 文件 PVs

存储类用于定义如何创建 Azure 文件共享。 将在 [节点资源组][node-resource-group] 中自动创建一个存储帐户，以便与存储类一起用于保存 Azure 文件共享。 选择以下适用于*skuName*的[Azure 存储冗余 sku][storage-skus]之一：

* **Standard_LRS**：标准本地冗余存储
* **Standard_GRS**：标准异地冗余存储
* **Standard_ZRS**：标准区域冗余存储
* **Standard_RAGRS**：标准读取访问异地冗余存储
* **Premium_LRS**：高级本地冗余存储

> [!NOTE]
> Azure 文件支持 Azure 高级存储。 最低级别的高级文件共享为 100 GB。

在 AKS 上使用存储 CSI 驱动程序时，有两个 `StorageClasses` 使用 Azure 文件 CSI 存储驱动程序的附加内置。 其他 CSI 存储类随群集的默认存储类一起创建。

- `azurefile-csi`：使用 Azure 标准存储创建 Azure 文件共享。
- `azurefile-csi-premium`：使用 Azure 高级存储创建 Azure 文件共享。

对于这两个存储类的回收策略，可确保在删除各自的 PV 时删除基础 Azure 文件共享。 存储类还会将文件共享配置为可展开，只需编辑具有新大小 (PVC) 的永久性卷声明。

若要使用这些存储类，请创建用于引用和使用它们的 [PVC](concepts-storage.md#persistent-volume-claims) 和各自的 pod。 PVC 用于基于存储类自动预配存储。 PVC 可以使用预先创建的存储类之一或用户定义的存储类，为所需的 SKU 和大小创建 Azure 文件共享。 创建 pod 定义时，将指定 PVC 来请求所需的存储。

创建一个使用[kubectl apply][kubectl-apply]命令将[当前日期输出到 `outfile` 的示例 PVC 和 pod](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) ：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/pvc-azurefile-csi.yaml
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/nginx-pod-azurefile.yaml

persistentvolumeclaim/pvc-azurefile created
pod/nginx-azurefile created
```

在 pod 处于运行状态后，你可以通过运行以下命令来验证是否已正确装载了文件共享，并验证输出中是否包含 `outfile` ：

```console
$ kubectl exec nginx-azurefile -- ls -l /mnt/azurefile

total 29
-rwxrwxrwx 1 root root 29348 Aug 31 21:59 outfile
```

## <a name="create-a-custom-storage-class"></a>创建自定义存储类

默认存储类适合最常见的方案，但并非全部。 在某些情况下，你可能希望使用自己的参数自定义自己的存储类。 例如，使用以下清单来配置 `mountOptions` 文件共享的。

对于 Kubernetes 装入的文件共享，"DirMode *" 和 "* *dirMode* " 的默认值为*0777* 。 可以在存储类对象上指定不同的装载选项。

创建一个名为 `azure-file-sc.yaml` 的文件，并粘贴下面的示例清单：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: my-azurefile
provisioner: file.csi.azure.com
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
mountOptions:
  - dir_mode=0640
  - file_mode=0640
  - uid=0
  - gid=0
  - mfsymlinks
  - cache=strict # https://linux.die.net/man/8/mount.cifs
  - nosharesock
parameters:
  skuName: Standard_LRS
```

使用 [kubectl apply][kubectl-apply] 命令创建存储类：

```console
kubectl apply -f azure-file-sc.yaml

storageclass.storage.k8s.io/my-azurefile created
```

Azure 文件 CSI 驱动程序支持创建 [持久卷](https://kubernetes-csi.github.io/docs/snapshot-restore-feature.html) 和基础文件共享的快照。

使用[kubectl apply][kubectl-apply]命令创建[卷快照类](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml)：

```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshotclass-azurefile.yaml

volumesnapshotclass.snapshot.storage.k8s.io/csi-azurefile-vsc created
```

从[我们在本教程开头动态创建](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)的 PVC 创建[卷快照](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml) `pvc-azurefile` 。


```bash
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/snapshot/volumesnapshot-azurefile.yaml


volumesnapshot.snapshot.storage.k8s.io/azurefile-volume-snapshot created
```

验证是否正确创建了快照：

```bash
$ kubectl describe volumesnapshot azurefile-volume-snapshot

Name:         azurefile-volume-snapshot
Namespace:    default
Labels:       <none>
Annotations:  API Version:  snapshot.storage.k8s.io/v1beta1
Kind:         VolumeSnapshot
Metadata:
  Creation Timestamp:  2020-08-27T22:37:41Z
  Finalizers:
    snapshot.storage.kubernetes.io/volumesnapshot-as-source-protection
    snapshot.storage.kubernetes.io/volumesnapshot-bound-protection
  Generation:        1
  Resource Version:  955091
  Self Link:         /apis/snapshot.storage.k8s.io/v1beta1/namespaces/default/volumesnapshots/azurefile-volume-snapshot
  UID:               c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
Spec:
  Source:
    Persistent Volume Claim Name:  pvc-azurefile
  Volume Snapshot Class Name:      csi-azurefile-vsc
Status:
  Bound Volume Snapshot Content Name:  snapcontent-c359a38f-35c1-4fb1-9da9-2c06d35ca0f4
  Ready To Use:                        false
Events:                                <none>
```

## <a name="resize-a-persistent-volume"></a>调整持久卷的大小

可以为 PVC 请求更大的卷。 编辑 PVC 对象，并指定更大的大小。 此更改触发了支持 PV 的基础卷的扩展。

> [!NOTE]
> 永远不会创建新的 PV 来满足声明。 相反，会调整现有卷的大小。

在 AKS 中，内置 `azurefile-csi` 存储类已支持扩展，因此请使用先前使用 [此存储类创建的 PVC](#dynamically-create-azure-files-pvs-by-using-the-built-in-storage-classes)。 PVC 请求了100Gi 文件共享。 可以通过运行以下内容来确认：

```console 
$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile

Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  100G  128K  100G   1% /mnt/azurefile
```

通过增加字段来展开 PVC `spec.resources.requests.storage` ：

```console
$ kubectl patch pvc pvc-azurefile --type merge --patch '{"spec": {"resources": {"requests": {"storage": "200Gi"}}}}'

persistentvolumeclaim/pvc-azurefile patched
```

验证 pod 中的 PVC 和文件系统是否显示新的大小：

```console
$ kubectl get pvc pvc-azurefile
NAME            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS    AGE
pvc-azurefile   Bound    pvc-5e5d9980-da38-492b-8581-17e3cad01770   200Gi      RWX            azurefile-csi   64m

$ kubectl exec -it nginx-azurefile -- df -h /mnt/azurefile
Filesystem                                                                                Size  Used Avail Use% Mounted on
//f149b5a219bd34caeb07de9.file.core.windows.net/pvc-5e5d9980-da38-492b-8581-17e3cad01770  200G  128K  200G   1% /mnt/azurefile
```


## <a name="nfs-file-shares"></a>NFS 文件共享
[Azure 文件现在提供对 NFS v2.0 协议的支持](../storage/files/storage-files-how-to-create-nfs-shares.md)。 NFS 4.1 对 Azure 文件的支持为你提供了一个完全托管的 NFS 文件系统，它是基于高度可用且高度持久的分布式弹性存储平台构建的服务。

 此选项针对使用就地数据更新的随机访问工作负荷进行了优化，并提供完整的 POSIX 文件系统支持。 本部分介绍如何在 AKS 群集上通过 Azure 文件 CSI 驱动程序使用 NFS 共享。

请确保在预览阶段检查 [限制](../storage/files/storage-files-compare-protocols.md#limitations) 和 [区域可用性](../storage/files/storage-files-compare-protocols.md#regional-availability) 。

### <a name="register-the-allownfsfileshares-preview-feature"></a>注册 `AllowNfsFileShares` 预览功能

若要创建利用 NFS 4.1 的文件共享，必须 `AllowNfsFileShares` 在订阅上启用功能标志。

`AllowNfsFileShares`使用[az feature register][az-feature-register]命令注册功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.Storage" --name "AllowNfsFileShares"
```

状态显示为“已注册”需要几分钟时间**。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowNfsFileShares')].{Name:name,State:properties.state}"
```

准备就绪后，请使用[az provider register][az-provider-register]命令刷新*Microsoft 存储*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.Storage
```

### <a name="create-a-storage-account-for-the-nfs-file-share"></a>创建 NFS 文件共享的存储帐户

[创建一个 `Premium_LRS`](../storage/files/storage-how-to-create-premium-fileshare.md)具有以下配置的 Azure 存储帐户支持 NFS 共享：
- 帐户类型： FileStorage
- 需要安全传输 (仅启用 HTTPS 流量) ： false
- 选择防火墙和虚拟网络中代理节点的虚拟网络

### <a name="create-nfs-file-share-storage-class"></a>创建 NFS 文件共享存储类

在编辑相应的占位符后，保存 `nfs-sc.yaml` 包含清单的文件。

```yml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: azurefile-csi
provisioner: file.csi.azure.com
parameters:
  resourceGroup: EXISTING_RESOURCE_GROUP_NAME  # optional, required only when storage account is not in the same resource group as your agent nodes
  storageAccount: EXISTING_STORAGE_ACCOUNT_NAME
  protocol: nfs
```

编辑并保存文件后，使用 [kubectl apply][kubectl-apply] 命令创建存储类：

```console
$ kubectl apply -f nfs-sc.yaml

storageclass.storage.k8s.io/azurefile-csi created
```

### <a name="create-a-deployment-with-an-nfs-backed-file-share"></a>使用支持 NFS 的文件共享创建部署
可以[stateful set](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/statefulset.yaml) `data.txt` 通过使用[kubectl apply][kubectl-apply]命令部署以下命令，部署将时间戳保存到文件中的示例有状态集：

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/statefulset-azurefile created
```

通过运行以下内容来验证卷的内容：

```console
$ kubectl exec -it statefulset-azurefile-0 -- df -h

Filesystem      Size  Used Avail Use% Mounted on
...
/dev/sda1                                                                                 29G   11G   19G  37% /etc/hosts
accountname.file.core.windows.net:/accountname/pvc-fa72ec43-ae64-42e4-a8a2-556606f5da38  100G     0  100G   0% /mnt/azurefile
...
```

## <a name="windows-containers"></a>Windows 容器

Azure 文件 CSI 驱动程序还支持 Windows 节点和容器。 如果要使用 Windows 容器，请按照 [windows 容器教程](windows-container-cli.md) 添加 windows 节点池。

使用 Windows 节点池后，请使用内置的存储类，例如 `azurefile-csi` 或创建自定义的存储类。 你可以部署 [基于 Windows 的有状态集](https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/deploy/example/windows/statefulset.yaml) 示例，该程序集 `data.txt` 通过使用 [kubectl apply][kubectl-apply] 命令部署以下命令将时间戳保存到文件中：

 ```console
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/master/deploy/example/windows/statefulset.yaml

statefulset.apps/busybox-azurefile created
```

通过运行以下内容来验证卷的内容：

```console
$ kubectl exec -it busybox-azurefile-0 -- cat c:\\mnt\\azurefile\\data.txt # on Linux/MacOS Bash
$ kubectl exec -it busybox-azurefile-0 -- cat c:\mnt\azurefile\data.txt # on Windows Powershell/CMD

2020-08-27 22:11:01Z
2020-08-27 22:11:02Z
2020-08-27 22:11:04Z
(...)
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何将 CSI 驱动程序用于 Azure 磁盘，请参阅 [将 azure 磁盘与 csi 驱动程序配合使用](azure-disk-csi.md)。
- 有关存储最佳实践的详细信息，请参阅 [Azure Kubernetes 服务中存储和备份的最佳实践][operator-best-practices-storage]。


<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/
[smb-overview]: /windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview


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
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[storage-skus]: ../storage/common/storage-redundancy.md
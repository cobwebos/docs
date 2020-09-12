---
title: '在 Azure Kubernetes Service 上 (CSI) 驱动程序启用容器存储接口 (AKS) '
description: 了解如何在 Azure Kubernetes 服务 (AKS) 群集中，为 Azure 磁盘和 azure 文件 (CSI) 驱动程序启用容器存储接口。
services: container-service
ms.topic: article
ms.date: 08/27/2020
author: palma21
ms.openlocfilehash: bd5706d20496e1ff00843f761443d183cf7fcae3
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89422016"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks-preview"></a>为 azure Kubernetes Service 上的 Azure 磁盘和 Azure 文件 (CSI) 驱动程序启用容器存储接口 (AKS)  (预览版) 

 (CSI) 的容器存储接口是一种将任意块和文件存储系统公开给 Kubernetes 上容器化工作负荷的标准。 通过采用和使用 CSI，Azure Kubernetes Service (AKS) 可以在 Kubernetes 中编写、部署和循环访问现有存储系统，而无需接触核心 Kubernetes 代码并等待其发布周期。

AKS 上的 CSI 存储驱动程序支持使你能够以本机方式利用：
- [*Azure 磁盘*](azure-disk-csi.md) -可用于创建 Kubernetes *DataDisk* 资源。 磁盘可以使用 Azure 高级存储，支持高性能 Ssd 或 Azure 标准存储，由常规 Hdd 或标准 Ssd 支持。 对于大部分生产和开发工作负荷，请使用高级存储。 Azure 磁盘以 ReadWriteOnce 的形式装载，因此仅可用于单个 Pod。 对于可同时由多个 Pod 访问的存储卷，请使用 Azure 文件存储。
- 可以使用[*Azure 文件*](azure-files-csi.md)将 azure 存储帐户支持的 SMB 3.0 共享装载到 pod。 借助 Azure 文件,可跨多个节点和 Pod 共享数据。 文件可以使用由常规 HDD 支持的 Azure 标准存储，也可以使用由高性能 SSD 支持的Azure 高级存储。

> [!IMPORTANT]
> 从 Kubernetes 版本1.21 开始，Kubernetes 将仅使用 CSI 驱动程序和默认使用。 这些是 Kubernetes 中的存储支持。
>
> *"树内驱动程序"* 是指核心 kubernetes 代码与作为插件的新 CSI 驱动程序的一部分的当前存储驱动程序。

## <a name="limitations"></a>限制

- 此功能只能在创建群集时设置。
- 支持 CSI 驱动程序的最低 kubernetes 次版本为 v 1.17。
- 在预览期间，默认存储类仍将与 [树内存储类相同](concepts-storage.md#storage-classes)。 此功能正式发布后，默认存储类将为 `managed-csi` 存储类，并且将删除树状存储类。
- 在第一个预览阶段，仅支持 Azure CLI。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="register-the-enableazurediskfilecsidriver-preview-feature"></a>注册 `EnableAzureDiskFileCSIDriver` 预览功能

若要创建可利用适用于 Azure 磁盘和 Azure 文件的 CSI 驱动程序的 AKS 群集，必须 `EnableAzureDiskFileCSIDriver` 在订阅上启用功能标志。

`EnableAzureDiskFileCSIDriver`使用[az feature register][az-feature-register]命令注册功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAzureDiskFileCSIDriver"
```

状态显示为“已注册”需要几分钟时间**。 使用 [az feature list][az-feature-list] 命令验证注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAzureDiskFileCSIDriver')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建可使用 CSI 存储驱动程序的 AKS 群集或节点池，需要使用最新的 *AKS* CLI 扩展。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，或使用[az extension update][az-extension-update]命令安装任何可用更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 


## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>创建可使用 CSI 存储驱动程序的新群集

使用以下 CLI 命令创建一个新的群集，以便为 Azure 磁盘和 Azure 文件利用 CSI 存储驱动程序。 使用 `--aks-custom-headers` 标志设置 `EnableAzureDiskFileCSIDriver` 功能。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

创建支持 CSI 存储驱动程序的 AKS 群集。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure -k 1.17.9 --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

如果要创建群集的群集内存储驱动程序而不是 CSI 存储驱动程序，可以通过省略自定义参数来执行此操作 `--aks-custom-headers` 。


通过运行以下内容来检查可以附加到此节点的基于 Azure 磁盘的卷的数量：

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>后续步骤

- 将 CSI 驱动器用于 Azure 磁盘，请参阅 [将 azure 磁盘与 CSI 驱动程序配合使用](azure-disk-csi.md)。
- 将 CSI 驱动器用于 Azure 文件，请参阅 [将 azure 文件与 CSI 驱动程序配合使用](azure-files-csi.md)。
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
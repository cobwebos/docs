---
title: '启用 Azure Kubernetes Service 上的 Ultra 磁盘支持 (AKS) '
description: 了解如何启用和配置 Azure Kubernetes Service 中的 Ultra 磁盘 (AKS) 群集
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: 3f15f075604c104b467af289f6f5d4b92dc12659
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89420857"
---
# <a name="use-azure-ultra-disks-on-azure-kubernetes-service-preview"></a>使用 Azure 上的 Azure Kubernetes 服务 (预览) 

[Azure ultra 磁盘](../virtual-machines/disks-enable-ultra-ssd.md) 为有状态应用程序提供高吞吐量、高 IOPS 和一致的低延迟磁盘存储。 超磁盘的一个主要优点是能够在不重新启动代理节点的情况下动态更改 SSD 的性能和工作负荷。 超磁盘适用于数据密集型工作负荷。

## <a name="before-you-begin"></a>准备阶段

此功能只能在创建群集或创建节点池时设置。

> [!IMPORTANT]
> Azure ultra 磁盘要求在可用性区域中部署 nodepools，并在支持这些磁盘的区域以及仅特定的 VM 系列上部署。 请参阅 [**Ultra 磁盘 GA 范围和限制**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)。

### <a name="register-the-enableultrassd-preview-feature"></a>注册 `EnableUltraSSD` 预览功能

若要创建可利用超磁盘的 AKS 群集或节点池，必须 `EnableUltraSSD` 在订阅上启用功能标志。

`EnableUltraSSD`使用[az feature register][az-feature-register]命令注册功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableUltraSSD"
```

状态显示为“已注册”需要几分钟时间**。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableUltraSSD')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建可使用超磁盘的 AKS 群集或节点池，需要使用最新的 *AKS* CLI 扩展。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，或使用[az extension update][az-extension-update]命令安装任何可用更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="limitations"></a>限制
- 查看[ **ULTRA 磁盘 GA 范围和限制**](../virtual-machines/disks-enable-ultra-ssd.md#ga-scope-and-limitations)
- 超磁盘支持的大小范围为100到1500

## <a name="create-a-new-cluster-that-can-use-ultra-disks"></a>创建可以使用超磁盘的新群集

使用以下 CLI 命令创建能够利用超高磁盘的 AKS 群集。 使用 `--aks-custom-headers` 标志设置 `EnableUltraSSD` 功能。

创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

创建支持超磁盘的 AKS 群集。

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster -l westus2 --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

如果你想要创建不含 ultra 磁盘支持的群集，可以通过省略自定义参数来执行此操作 `--aks-custom-headers` 。

## <a name="enable-ultra-disks-on-an-existing-cluster"></a>启用现有群集上的 Ultra 磁盘

可以通过将新的节点池添加到支持超磁盘的群集上，在现有群集上启用 ultra 磁盘。 使用标志配置新的节点池以使用基于主机的加密 `--aks-custom-headers` 。

```azurecli
az aks nodepool add --name ultradisk --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_L8s_v2 --zones 1 2 --node-count 2 --aks-custom-headers EnableUltraSSD=true
```

如果你想要创建新的节点池而不支持 ultra 磁盘，可以通过省略自定义参数来执行此操作 `--aks-custom-headers` 。

## <a name="use-ultra-disks-dynamically-with-a-storage-class"></a>使用存储类动态地使用超级磁盘

若要在部署或有状态集中使用超磁盘，可以使用 [存储类进行动态设置](azure-disks-dynamic-pv.md)。

### <a name="create-the-storage-class"></a>创建存储类

存储类用于定义使用永久性卷动态创建存储单位的方式。 有关 Kubernetes 存储类的详细信息，请参阅 [Kubernetes 存储类][kubernetes-storage-classes]。

在这种情况下，我们将创建一个引用 ultra 磁盘的存储类。 创建名为 `azure-ultra-disk-sc.yaml` 的文件，并将其复制到以下清单中。

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: ultra-disk-sc
provisioner: kubernetes.io/azure-disk
volumeBindingMode: WaitForFirstConsumer # optional, but recommended if you want to wait until the pod that will use this disk is created 
parameters:
  skuname: UltraSSD_LRS
  kind: managed
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB 
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
```

使用 [kubectl 应用][kubectl-apply] 命令创建存储类，并指定 *azure yaml* 文件：

```console
$ kubectl apply -f azure-ultra-disk-sc.yaml


storageclass.storage.k8s.io/ultra-disk-sc created
```

## <a name="create-a-persistent-volume-claim"></a>创建永久性卷声明

永久卷声明 (PVC) 用于基于存储类自动预配存储。 在这种情况下，PVC 可以使用以前创建的存储类来创建超磁盘。

创建名为 `azure-ultra-disk-pvc.yaml` 的文件，并将其复制到以下清单中。 声明请求的磁盘 `ultra-disk` 的大小为 *1000 GB* ， *ReadWriteOnce* 访问。 将 *超小型* 存储类指定为存储类。

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ultra-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: ultra-disk-sc
  resources:
    requests:
      storage: 1000Gi
```

使用 [kubectl apply][kubectl-apply] 命令创建永久性卷声明，并指定 *azure yaml* 文件：

```console
$ kubectl apply -f azure-ultra-disk-pvc.yaml

persistentvolumeclaim/ultra-disk created
```

## <a name="use-the-persistent-volume"></a>使用永久性卷

创建永久性卷声明并成功预配磁盘以后，即可创建可以访问磁盘的 Pod。 下面的清单创建了一个基本的 NGINX pod，它使用名为 *超小型* 的永久性卷声明在路径上装载 Azure 磁盘 `/mnt/azure` 。

创建名为 `nginx-ultra.yaml` 的文件，并将其复制到以下清单中。

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-ultra
spec:
  containers:
  - name: nginx-ultra
    image: nginx
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: ultra-disk
```

使用 [kubectl apply][kubectl-apply] 命令创建 Pod，如以下示例所示：

```console
$ kubectl apply -f nginx-ultra.yaml

pod/nginx-ultra created
```

现在你有一个正在运行的 Pod，其中 Azure 磁盘被装载到 `/mnt/azure` 目录中。 通过 `kubectl describe pod nginx-ultra` 检查 Pod 时可以看到此配置，如以下精简示例所示：

```console
$ kubectl describe pod nginx-ultra

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
[...]
Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```


## <a name="next-steps"></a>后续步骤

- 有关 ultra 磁盘的详细信息，请参阅 [使用 Azure 超磁盘](../virtual-machines/disks-enable-ultra-ssd.md)。
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
[premium-storage]: ../virtual-machines/disks-types.md
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

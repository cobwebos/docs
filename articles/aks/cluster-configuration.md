---
title: Azure Kubernetes 服务 (AKS) 中的群集配置
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中配置群集
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 6446e138df1fe744d70be085d0aecac58e2c1c45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91255292"
---
# <a name="configure-an-aks-cluster"></a>配置 AKS 群集

在创建 AKS 群集的过程中，你可能需要自定义群集配置来满足你的需求。 本文介绍了几个用于自定义 AKS 群集的选项。

## <a name="os-configuration"></a>OS 配置

AKS 现在支持 Ubuntu 18.04 作为节点操作系统 (操作系统) 在 kubernetes 版本高于1.18.8 中的群集。 对于小于 1.18. x 的版本，AKS Ubuntu 16.04 仍是默认的基本映像。 从 kubernetes v 1.18. x 开始，默认基是 AKS Ubuntu 18.04。

> [!IMPORTANT]
> 在 Kubernetes v 1.18 或更高版本上创建的节点池 `AKS Ubuntu 18.04` 。 在支持的 Kubernetes 版本上，节点池小于1.18 的接收 `AKS Ubuntu 16.04` 节点映像，但在将 `AKS Ubuntu 18.04` 节点池 Kubernetes 版本更新为 v 1.18 或更高版本后，将更新为。
> 
> 强烈建议在1.18 或更高版本上使用群集之前，在 AKS Ubuntu 18.04 节点池上测试工作负荷。 了解如何 [测试 Ubuntu 18.04 节点池](#use-aks-ubuntu-1804-existing-clusters-preview)。

以下部分将介绍在尚未使用 kubernetes 版本 1.18. x 或更高版本的群集上使用和测试 AKS Ubuntu 18.04 的情况，或在此功能正式发布之前，通过使用 OS 配置预览进行创建。

必须已安装以下资源：

- [Azure CLI][azure-cli-install]2.2.0 或更高版本
- aks-preview 0.4.35 扩展

若要安装 aks-preview 0.4.35 扩展或更高版本，请运行以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

注册 `UseCustomizedUbuntuPreview` 功能：

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

状态可能需要几分钟才显示为“已注册”。 可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) 命令来检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

当状态显示为“已注册”时，使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>在新群集上使用 AKS Ubuntu 18.04 (预览) 

配置群集，以便在创建群集时使用 Ubuntu 18.04。 使用 `--aks-custom-headers` 标记将 Ubuntu 18.04 设置为默认 OS。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果要使用 AKS Ubuntu 16.04 映像创建群集，可以通过省略自定义标记来执行此操作 `--aks-custom-headers` 。

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a> (预览版使用 AKS Ubuntu 18.04 现有群集) 

配置新节点池，以使用 Ubuntu 18.04。 使用 `--aks-custom-headers` 标志将 Ubuntu 18.04 设置为该节点池的默认 OS。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果要使用 AKS Ubuntu 16.04 映像创建节点池，可以通过省略自定义标记来执行此操作 `--aks-custom-headers` 。


## <a name="container-runtime-configuration-preview"></a>容器运行时配置 (预览) 

容器运行时是在节点上执行容器和管理容器映像的软件。 运行时有助于抽象地消除 sys 调用或操作系统 (OS) 特定功能，以在 Linux 或 Windows 上运行容器。 如今，AKS 使用 [小鲸鱼](https://mobyproject.org/) (上游 docker) 作为其容器运行时。 
    
![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) 是 [OCI](https://opencontainers.org/) (开放容器计划) 兼容核心容器运行时，它提供执行容器和管理节点上的映像所需的最小功能集。 它在2017年3月 (CNCF) [捐赠](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) 了云本机计算基础。 目前，AKS 使用的当前小鲸鱼版本已经利用了，并构建在之上 `containerd` ，如上所述。 

对于基于 containerd 的节点和节点池， `dockershim` kubelet 将直接 `containerd` 通过 CRI (容器运行时) 接口与 Docker CRI 实现相比，在流上删除额外的跃点，而不是与通信。 因此，你将看到更好的 pod 启动延迟，更少的资源 (CPU 和内存) 使用量。

通过 `containerd` 将用于 AKS 节点，容器运行时可以降低 pod 启动延迟，并减少节点资源消耗。 这些改进是通过此新体系结构实现的，其中，kubelet 直接 `containerd` 通过 CRI 插件直接与插件进行交互，而在小鲸鱼/docker 体系结构中，kubelet 会在 `dockershim` 到达之前与 docker 引擎通信 `containerd` ，因此在流上具有额外的跃点。

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` 适用于 AKS 中的每个 GA 版本和 kubernetes 的每个上游 kubernetes 版本，并支持所有 kubernetes 和 AKS 功能。

> [!IMPORTANT]
> 在 `containerd` AKS 上公开发布后，它将成为新群集上的容器运行时默认和唯一的选项。 你仍可以在旧的受支持版本上使用小鲸鱼 nodepools 和群集，直到这些版本停止支持。 
> 
> 建议在 `containerd` 升级或创建具有此容器运行时的新群集之前，在节点池上测试工作负荷。

### <a name="use-containerd-as-your-container-runtime-preview"></a>使用 `containerd` 作为容器运行时 (预览) 

必须具备以下先决条件：

- 已安装[Azure CLI][azure-cli-install]版本2.8.0 或更高版本
- Aks 扩展版本0.4.53 或更高版本
- `UseCustomizedContainerRuntime`已注册功能标志
- `UseCustomizedUbuntuPreview`已注册功能标志

若要安装 aks-preview 0.4.53 扩展或更高版本，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

注册 `UseCustomizedContainerRuntime` 和 `UseCustomizedUbuntuPreview` 功能：

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

状态可能需要几分钟才显示为“已注册”。 可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) 命令来检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

当状态显示为“已注册”时，使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>`containerd`在新群集上使用 (预览) 

配置群集以在 `containerd` 创建群集时使用。 使用 `--aks-custom-headers` 标志设置 `containerd` 为容器运行时。

> [!NOTE]
> `containerd`仅使用 AKS Ubuntu 18.04 映像的节点和节点池支持运行时。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

如果要使用小鲸鱼 (docker) 运行时创建群集，则可以通过省略自定义标记来执行此操作 `--aks-custom-headers` 。

### <a name="use-containerd-on-existing-clusters-preview"></a>`containerd`在现有群集上使用 (预览) 

配置要使用的新节点池 `containerd` 。 使用 `--aks-custom-headers` 标志设置 `containerd` 为该节点池的运行时。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

如果要使用小鲸鱼 (docker) 运行时创建节点池，可以通过省略自定义标记来执行此操作 `--aks-custom-headers` 。


### <a name="containerd-limitationsdifferences"></a>`Containerd` 限制/差异

* 若要将 `containerd` 用作容器运行时，必须使用 AKS Ubuntu 18.04 作为基础操作系统映像。
* 尽管 docker 工具集仍然存在于节点上，Kubernetes 使用 `containerd` 作为容器运行时。 因此，由于小鲸鱼/Docker 不管理节点上的 Kubernetes 创建的容器，因此不能使用 Docker 命令 (如 `docker ps`) 或 DOCKER API 来查看容器或与之交互。
* 对于 `containerd` ，我们建议使用 [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) 作为替代 cli，而不是 Docker cli 来 **排查** Kubernetes 节点上的 pod、容器和容器映像 (例如 `crictl ps`) 。 
   * 它不提供 docker CLI 的完整功能。 它仅用于进行故障排除。
   * `crictl` 提供更 kubernetes 的容器视图，其中包含 pod 等概念等。
* `Containerd` 使用标准化 `cri` 日志格式 (设置日志记录格式，这不同于从 docker 的 json 驱动程序) 获取的内容。 日志记录解决方案需要支持 `cri` (如 [容器 Azure Monitor 的](../azure-monitor/insights/container-insights-enable-new-cluster.md) 日志记录格式) 
* 你不能再访问 docker 引擎， `/var/run/docker.sock` 或使用 docker (DinD) 。
  * 如果当前从 Docker 引擎提取应用程序日志或监视数据，请改用 [容器 Azure Monitor](../azure-monitor/insights/container-insights-enable-new-cluster.md) 。 此外，AKS 不支持在可能导致不稳定的代理节点上运行任何带外命令。
  * 即使使用小鲸鱼/docker，也强烈建议不要通过上述方法构建映像并直接利用 docker 引擎。 Kubernetes 并不完全了解那些已使用的资源，这些方法会提供 [本文](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) 详细介绍的许多 [问题，例如](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)。
* 生成图像-建议使用 [ACR 任务](../container-registry/container-registry-quickstart-task-cli.md)来生成映像。 另一种方法是使用 [docker buildx](https://github.com/docker/buildx)等更安全的群集内选项。

## <a name="generation-2-virtual-machines-preview"></a>第2代虚拟机 (预览) 

Azure 支持 [第2代 (Gen2) 虚拟机 (vm) ](../virtual-machines/windows/generation-2.md)。 第2代 Vm 支持第1代 Vm 不支持的关键功能 (Gen1) 。 这些特性包括更大的内存、Intel Software Guard Extensions (Intel SGX) 和虚拟化持久性内存 (vPMEM)。

第 2 代 VM 使用新的基于 UEFI 的启动体系结构，而不是使用第 1 代 VM 所用的基于 BIOS 的体系结构。
只有特定的 Sku 和大小支持 Gen2 Vm。 查看 [受支持的大小列表](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes)，查看你的 SKU 是否支持或需要 Gen2。

此外，并非所有 VM 映像都支持 Gen2，在 AKS Gen2 Vm 上，将使用新的 [AKS Ubuntu 18.04 映像](#os-configuration)。 此映像支持所有 Gen2 Sku 和大小。

若要在预览期间使用 Gen2 Vm，你将需要：
- `aks-preview`CLI 扩展已安装。
- `Gen2VMPreview`已注册功能标志。

注册 `Gen2VMPreview` 功能：

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

状态可能需要几分钟才显示为“已注册”。 可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) 命令来检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

当状态显示为“已注册”时，使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

若要安装 aks CLI 扩展，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
```

若要更新 aks-preview CLI 扩展，请使用以下 Azure CLI 命令：

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>在新群集上使用 Gen2 Vm (预览) 
配置群集以在创建群集时对所选 SKU 使用 Gen2 Vm。 使用 `--aks-custom-headers` 标志将 Gen2 设置为新群集上的 VM 生成。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

如果要使用第1代 (Gen1) Vm 创建常规群集，可以通过省略自定义标记来执行此操作 `--aks-custom-headers` 。 还可以选择添加更多 Gen1 或 Gen2 Vm，如下所示。

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>在现有群集上使用 Gen2 Vm (预览) 
配置新的节点池以使用 Gen2 Vm。 使用 `--aks-custom-headers` 标志将 Gen2 设置为该节点池的 VM 生成。

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

如果要创建常规 Gen1 节点池，可以通过省略自定义标记来执行此操作 `--aks-custom-headers` 。


## <a name="ephemeral-os-preview"></a>临时 OS (预览) 

默认情况下，Azure 虚拟机的操作系统磁盘会自动复制到 Azure 存储，以避免在 VM 需要重定位到另一台主机时丢失数据。 不过，由于容器不会保留本地状态，因此此行为在提供一些缺点的同时提供了有限的价值，其中包括速度较慢的节点预配和更高的读/写延迟。

与此相反，临时 OS 磁盘只存储在主机上，就像临时磁盘一样。 这提供了更低的读/写延迟，还提供了更快的节点缩放和群集升级。

与临时磁盘类似，临时 OS 磁盘包含在虚拟机的价格中，因此不会产生额外的存储成本。

注册 `EnableEphemeralOSDiskPreview` 功能：

```azurecli
az feature register --name EnableEphemeralOSDiskPreview --namespace Microsoft.ContainerService
```

状态可能需要几分钟才显示为“已注册”。 可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) 命令来检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEphemeralOSDiskPreview')].{Name:name,State:properties.state}"
```

当状态显示为“已注册”时，使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

临时操作系统至少需要0.4.63 版本的 aks CLI 扩展。

若要安装 aks CLI 扩展，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
```

若要更新 aks-preview CLI 扩展，请使用以下 Azure CLI 命令：

```azurecli
az extension update --name aks-preview
```

### <a name="use-ephemeral-os-on-new-clusters-preview"></a>在新群集上使用临时 OS (预览) 

将群集配置为在创建群集时使用临时 OS 磁盘。 使用 " `--node-osdisk-type` 标志" 将 "暂时操作系统" 设置为新群集的 os 磁盘类型。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

如果要使用网络连接的 OS 磁盘创建常规群集，可以通过省略自定义标记或指定来创建常规群集 `--node-osdisk-type` `--node-osdisk-type=Managed` 。 还可以选择添加更多的临时 OS 节点池，如下所示。

### <a name="use-ephemeral-os-on-existing-clusters-preview"></a>在现有群集上使用暂时 OS (预览) 
配置一个新的节点池以使用临时 OS 磁盘。 使用 `--node-osdisk-type` 标志设置作为该节点池的 os 磁盘类型。

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> 通过暂时操作系统，可将 VM 和实例映像部署到 VM 缓存大小。 在 AKS 情况下，默认节点 OS 磁盘配置使用100GiB，这意味着需要的 VM 大小的缓存大于 100 GiB。 默认 Standard_DS2_v2 的缓存大小为 86 GiB，这不太大。 Standard_DS3_v2 的缓存大小为 172 GiB，足够大。 还可以通过使用降低 OS 磁盘的默认大小 `--node-osdisk-size` 。 AKS 图像的最小大小为30GiB。 

如果要创建包含网络附加 OS 磁盘的节点池，可以通过省略自定义标记来执行此操作 `--node-osdisk-type` 。

## <a name="custom-resource-group-name"></a>自定义资源组名称

在 Azure 中部署 Azure Kubernetes 服务群集时，会为工作器节点创建第二个资源组。 默认情况下，AKS 会将节点资源组命名为 `MC_resourcegroupname_clustername_location`，但你也可以提供自己的名称。

若要指定自己的资源组名称，请安装 aks-preview Azure CLI 扩展版本 0.3.2 或更高版本。 使用 Azure CLI 时，通过 `az aks create` 命令的 `--node-resource-group` 参数为资源组指定自定义名称。 如果使用 Azure 资源管理器模板部署 AKS 群集，可以使用 `nodeResourceGroup` 属性定义资源组名称。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

第二个资源组由订阅中的 Azure 资源提供程序自动创建。 只有创建了群集，才能指定自定义资源组名称。 

请注意，对于节点资源组，不能执行以下操作：

- 不能为节点资源组指定现有资源组。
- 为节点资源组指定不同的订阅。
- 创建群集后更改节点资源组名称。
- 不能为节点资源组内的受管理资源指定名称。
- 不能修改或删除节点资源组内受管理资源中由 Azure 创建的标记。

## <a name="next-steps"></a>后续步骤

- 了解如何在群集中使用 `Kured` [将安全和内核更新应用到 Linux 节点](node-updates-kured.md)。
- 若要了解如何将群集升级到最高版本的 Kubernetes，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集](upgrade-cluster.md)。
- 详细了解[ `containerd` 和 Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- 若要查找有关一些常用 AKS 问题的答案，请参阅 [AKS 常见问题解答](faq.md)。
- 阅读有关 [临时 OS 磁盘](../virtual-machines/ephemeral-os-disks.md)的详细信息。


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

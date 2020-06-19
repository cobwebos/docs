---
title: Azure Kubernetes 服务 (AKS) 中的群集配置
description: 了解如何在 Azure Kubernetes 服务 (AKS) 中配置群集
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: fe5ce13d9db8f2bc2231f87de7e602e63d239bfa
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725140"
---
# <a name="configure-an-aks-cluster"></a>配置 AKS 群集

在创建 AKS 群集的过程中，你可能需要自定义群集配置来满足你的需求。 本文介绍了几个用于自定义 AKS 群集的选项。

## <a name="os-configuration-preview"></a>OS 配置（预览版）

AKS 现在支持 Ubuntu 18.04 作为预览版中的节点操作系统 (OS)。 在预览版期间，Ubuntu 16.04 和 Ubuntu 18.04 都可用。

必须已安装以下资源：

- Azure CLI，版本 2.2.0 或更高版本
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

状态可能需要几分钟才显示为“已注册”。 可以使用 [az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令来检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

当状态显示为“已注册”时，使用 [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="new-clusters"></a>新群集

配置群集，以便在创建群集时使用 Ubuntu 18.04。 使用 `--aks-custom-headers` 标记将 Ubuntu 18.04 设置为默认 OS。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

若要创建常规 Ubuntu 16.04 群集，可以通过省略自定义 `--aks-custom-headers` 标记来执行此操作。

### <a name="existing-clusters"></a>现有群集

配置新节点池，以使用 Ubuntu 18.04。 使用 `--aks-custom-headers` 标志将 Ubuntu 18.04 设置为该节点池的默认 OS。

```azure-cli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果要创建常规 16.04 节点池，可以通过省略自定义 `--aks-custom-headers` 标记来执行此操作。


## <a name="custom-resource-group-name"></a>自定义资源组名称

在 Azure 中部署 Azure Kubernetes 服务群集时，会为工作器节点创建第二个资源组。 默认情况下，AKS 会将节点资源组命名为 `MC_resourcegroupname_clustername_location`，但你也可以提供自己的名称。

若要指定自己的资源组名称，请安装 aks-preview Azure CLI 扩展版本 0.3.2 或更高版本。 使用 Azure CLI 时，通过 `az aks create` 命令的 `--node-resource-group` 参数为资源组指定自定义名称。 如果使用 Azure 资源管理器模板部署 AKS 群集，可以使用 `nodeResourceGroup` 属性定义资源组名称。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

第二个资源组由订阅中的 Azure 资源提供程序自动创建。 只有创建了群集，才能指定自定义资源组名称。 

请注意，对于节点资源组，不能执行以下操作：

- 不能为节点资源组指定现有资源组。
- 不能为节点资源组指定不同的订阅。
- 不能在节点资源组创建完成后更改其名称。
- 不能为节点资源组内的受管理资源指定名称。
- 不能修改或删除节点资源组内受管理资源中由 Azure 创建的标记。

## <a name="next-steps"></a>后续步骤

- 了解如何在群集中使用 `Kured` [将安全和内核更新应用到 Linux 节点](node-updates-kured.md)。
- 若要了解如何将群集升级到最高版本的 Kubernetes，请参阅[升级 Azure Kubernetes 服务 (AKS) 群集](upgrade-cluster.md)。
- 若要查找有关一些常用 AKS 问题的答案，请参阅 [AKS 常见问题解答](faq.md)。
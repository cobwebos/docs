---
title: Azure Kubernetes Services 中的群集配置（AKS）
description: 了解如何在 Azure Kubernetes Service （AKS）中配置群集
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79479158"
---
# <a name="configure-an-aks-cluster"></a>配置 AKS 群集

在创建 AKS 群集的过程中，你可能需要自定义群集配置以满足你的需求。 本文介绍了用于自定义 AKS 群集的几个选项。

## <a name="os-configuration-preview"></a>OS 配置（预览）

AKS 现在支持将 Ubuntu 18.04 作为预览中的节点操作系统（OS）。 预览期间，Ubuntu 16.04 和 Ubuntu 18.04 都可用。

必须安装下列资源：

- Azure CLI 2.2.0 或更高版本
- Aks-preview 0.4.35 扩展

若要安装 aks-preview 0.4.35 扩展或更高版本，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

注册`UseCustomizedUbuntuPreview`功能：

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

状态显示为 "**已注册**" 可能需要几分钟时间。 您可以使用[az feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

当状态显示为 "已注册" 时，使用`Microsoft.ContainerService` [az provider register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)命令刷新资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

将群集配置为在创建群集时使用 Ubuntu 18.04。 使用`--aks-custom-headers`标志将 Ubuntu 18.04 设置为默认操作系统。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

若要创建常规 Ubuntu 16.04 群集，可以通过省略自定义`--aks-custom-headers`标记来执行此操作。

## <a name="custom-resource-group-name"></a>自定义资源组名称

在 Azure 中部署 Azure Kubernetes Service 群集时，将为辅助角色节点创建另一个资源组。 默认情况下，AKS 将命名节点资源组`MC_resourcegroupname_clustername_location`，但也可以提供自己的名称。

若要指定自己的资源组名称，请安装 aks-preview Azure CLI 扩展版本 0.3.2 或更高版本。 使用 Azure CLI，使用`--node-resource-group` `az aks create`命令的参数为资源组指定自定义名称。 如果使用 Azure 资源管理器模板来部署 AKS 群集，则可以通过使用`nodeResourceGroup`属性来定义资源组名称。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Azure 资源提供程序会在你自己的订阅中自动创建辅助资源组。 请注意，在创建群集时，只能指定自定义资源组名称。 

使用节点资源组时，请记住，不能：

- 指定现有的资源组作为节点资源组。
- 为节点资源组指定不同的订阅。
- 创建群集后更改节点资源组名称。
- 为节点资源组中的托管资源指定名称。
- 修改或删除节点资源组中的托管资源的 Azure 创建的标记。

## <a name="next-steps"></a>后续步骤

- 了解如何使用`Kured`将[安全更新和内核更新应用到](node-updates-kured.md)群集中的 Linux 节点。
- 请参阅[升级 Azure Kubernetes Service （AKS）群集](upgrade-cluster.md)，了解如何将群集升级到最新版本的 Kubernetes。
- 请参阅[有关 AKS](faq.md)的常见问题解答列表，查找一些常见问题的答案。
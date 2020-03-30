---
title: Azure 库伯奈斯服务 （AKS） 中的群集配置
description: 了解如何在 Azure 库伯奈斯服务 （AKS） 中配置群集
services: container-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 94f84beee2d7a76e48ac1470a0ce0b387929cc08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479158"
---
# <a name="configure-an-aks-cluster"></a>配置 AKS 群集

作为创建 AKS 群集的一部分，您可能需要自定义群集配置以满足您的需要。 本文介绍了一些用于自定义 AKS 群集的选项。

## <a name="os-configuration-preview"></a>操作系统配置（预览版）

AKS 现在支持 Ubuntu 18.04 作为预览中的节点操作系统 （OS）。 在预览期间，Ubuntu 16.04 和 Ubuntu 18.04 均可用。

您必须安装以下资源：

- Azure CLI，版本 2.2.0 或更高版本
- aks 预览 0.4.35 扩展

要安装 aks 预览 0.4.35 扩展或更高版本，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

注册功能`UseCustomizedUbuntuPreview`：

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

状态可能需要几分钟才能显示为 **"已注册**"。 您可以使用[az 要素列表](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)命令检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

当状态显示为已注册时，请使用 az`Microsoft.ContainerService`提供程序寄存器命令刷新资源提供程序[的注册](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

将群集配置为在创建群集时使用 Ubuntu 18.04。 使用标志`--aks-custom-headers`将 Ubuntu 18.04 设置为默认操作系统。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果要创建常规 Ubuntu 16.04 群集，可以通过省略自定义`--aks-custom-headers`标记来执行此操作。

## <a name="custom-resource-group-name"></a>自定义资源组名称

在 Azure 中部署 Azure Kubernetes 服务群集时，将为辅助节点创建第二个资源组。 默认情况下，AKS 将命名节点资源组`MC_resourcegroupname_clustername_location`，但您也可以提供您自己的名称。

若要指定自己的资源组名称，请安装 aks-preview Azure CLI 扩展版本 0.3.2 或更高版本。 使用 Azure CLI，使用`--node-resource-group``az aks create`命令的参数为资源组指定自定义名称。 如果使用 Azure 资源管理器模板来部署 AKS 群集，则可以使用 属性`nodeResourceGroup`定义资源组名称。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Azure 资源提供程序会在你自己的订阅中自动创建辅助资源组。 请注意，只能在创建群集时指定自定义资源组名称。 

使用节点资源组时，请记住，不能：

- 指定现有的资源组作为节点资源组。
- 为节点资源组指定不同的订阅。
- 创建群集后更改节点资源组名称。
- 为节点资源组中的托管资源指定名称。
- 修改或删除节点资源组中的托管资源的 Azure 创建的标记。

## <a name="next-steps"></a>后续步骤

- 了解如何使用`Kured`将[安全和内核更新应用于](node-updates-kured.md)群集中的 Linux 节点。
- 请参阅[升级 Azure 库伯奈斯服务 （AKS） 群集](upgrade-cluster.md)，了解如何将群集升级到最新版本的 Kubernetes。
- 查看[有关 AKS](faq.md)的常见问题列表，以查找一些常见 AKS 问题的答案。
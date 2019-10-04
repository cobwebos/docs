---
title: 在 Azure Kubernetes 服务中使用托管标识
description: 了解如何使用 Azure Kubernetes Service （AKS）中的托管标识
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827547"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>预览-在 Azure Kubernetes 服务中使用托管标识

目前，用户必须提供一个服务主体，或 AKS 为 AKS 群集（特别是 Kubernetes 云提供商）创建一个服务主体，以便在 Azure 中创建负载均衡器和托管磁盘等其他资源。 通常，使用到期日期创建服务主体。 群集最终会到达需要续订服务主体的状态，否则群集将无法工作。 管理服务主体增加了复杂性。 托管标识本质上是服务主体的包装器，使其管理更为简单。 阅读有关[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的详细信息。

AKS 创建两个托管标识：一个系统分配的托管标识，另一个由用户分配的标识。 Kubernetes 云提供程序使用系统分配的托管标识来代表用户创建 Azure 资源。 此系统分配的托管标识的生命周期与群集的生命周期相关联，并在删除群集时被删除。 AKS 还会创建一个用户分配的托管标识，该标识在群集中用于授权 AKS 访问 Acr、kubelet 从 Azure 获取元数据等。

在此预览期间，仍需要服务主体。 它将用于授权加载项，例如监视、虚拟节点、azure 策略和 http 应用程序路由。 正在进行的工作是删除 SPN 上加载项的依赖项，并且 AKS 中最终的 SPN 要求将被完全删除。

> [!IMPORTANT]
> AKS 预览功能是自助式选择加入功能。 预览版“按原样”提供，并且仅在“可用情况下”提供，不包含在服务级别协议和有限保障中。 AKS 预览版的内容部分包含在客户支持中，我们只能尽力提供支持。 因此，这些功能不应用于生产。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略](support-policies.md)
> * [Azure 支持常见问题](faq.md)

## <a name="before-you-begin"></a>开始之前

必须满足以下条件：

* 还需要 Azure CLI 版本2.0.70 或更高版本以及 aks-preview 0.4.14 扩展

## <a name="install-latest-aks-cli-preview-extension"></a>安装最新的 AKS CLI 预览版扩展

需要**aks-preview 0.4.14**扩展或更高版本。

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> 在订阅上注册功能时, 当前无法注册该功能。 启用某些预览功能后, 默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

状态显示为 "*已注册*" 可能需要几分钟时间。 可以通过使用 [az feature list] [az] command 来检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

注册状态后，请使用 [az provider register] [az-provider-register] 命令刷新*ContainerService*资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>使用托管标识创建 AKS 群集

现在可以使用以下 CLI 命令创建具有托管标识的 AKS 群集
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>创建 AKS 群集
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>获取用于访问群集的凭据
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
在几分钟内创建群集后，你可以部署应用程序工作负载，并与之交互，就像在基于服务主体的 AKS 群集中一样。 

> [!IMPORTANT]
> * 只能在创建群集的过程中启用具有托管标识的 AKS 群集
> * 无法更新/升级现有的 AKS 群集以启用托管标识
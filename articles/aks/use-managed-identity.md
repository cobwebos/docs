---
title: 在 Azure Kubernetes 服务中使用托管标识
description: 了解如何使用 Azure Kubernetes Service （AKS）中的托管标识
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112960"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes 服务中使用托管标识

目前，Azure Kubernetes Service （AKS）群集（具体而言，是 Kubernetes 云提供商）需要一个标识，以在 Azure 中创建负载均衡器和托管磁盘等其他资源，此标识可以是*托管标识*，也可以是*服务主体*。 如果使用[服务主体](kubernetes-service-principal.md)，则必须提供一个或 AKS 代表您创建一个。 如果使用托管标识，将自动为您创建 AKS。 使用服务主体的群集最终会到达一种状态，在此状态中必须续订服务主体，以使群集保持正常运行。 管理服务主体增加了复杂性，这就是更易于使用托管标识的原因。 相同的权限要求适用于服务主体和托管标识。

*托管标识*本质上是服务主体的包装，使其管理更简单。 若要了解详细信息，请参阅[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

AKS 创建两个托管标识：

- **系统分配的托管标识**： Kubernetes 云提供程序用来代表用户创建 Azure 资源的标识。 系统分配的标识的生命周期绑定到群集的生命周期。 删除群集时，将删除该标识。
- **用户分配的托管标识**：用于在群集中进行身份验证的标识。 例如，用户分配的标识用于授权 AKS 使用 Azure 容器注册表（Acr），或授权 kubelet 从 Azure 获取元数据。

外接程序也使用托管标识进行身份验证。 对于每个外接程序，AKS 创建一个托管标识，并在外接程序的生存期内持续。 

## <a name="before-you-begin"></a>开始之前

必须安装了以下资源：

- Azure CLI 2.2.0 或更高版本

## <a name="create-an-aks-cluster-with-managed-identities"></a>使用托管标识创建 AKS 群集

现在可以使用以下 CLI 命令创建具有托管标识的 AKS 群集。

首先，创建一个 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

使用托管标识成功创建群集包含此服务主体配置文件信息：

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> 若要创建和使用自己的 VNet、静态 IP 地址或附加的 Azure 磁盘（其中的资源位于 MC_ * 资源组之外），请使用群集系统分配的托管标识的 PrincipalID 来执行角色分配。 有关角色分配的详细信息，请参阅[委派对其他 Azure 资源的访问权限](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。
>
> Azure 云提供程序使用的群集托管标识的权限授予可能需要60分钟才能填充。

最后，获取用于访问群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

将在几分钟内创建该群集。 然后，你可以将应用程序工作负荷部署到新群集，并与之进行交互，就像你对基于服务主体的 AKS 群集进行的一样。

> [!IMPORTANT]
>
> - 只能在创建群集的过程中启用具有托管标识的 AKS 群集。
> - 无法更新或升级现有的 AKS 群集以启用托管标识。

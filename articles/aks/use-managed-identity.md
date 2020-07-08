---
title: 在 Azure Kubernetes 服务中使用托管标识
description: 了解如何使用 Azure Kubernetes Service （AKS）中的托管标识
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/30/2020
ms.author: mlearned
ms.openlocfilehash: 30d1290f9eb7b2750f09e5e256d4dd212c7e4607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610279"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>在 Azure Kubernetes 服务中使用托管标识

目前，Azure Kubernetes Service （AKS）群集（具体而言，是 Kubernetes 云提供程序）需要一个标识，以在 Azure 中创建负载均衡器和托管磁盘等其他资源。 此标识可以是*托管标识*，也可以是*服务主体*。 如果使用[服务主体](kubernetes-service-principal.md)，则必须提供一个或 AKS 代表您创建一个。 如果使用托管标识，将自动为您创建 AKS。 使用服务主体的群集最终会到达一种状态，在此状态中必须续订服务主体，以使群集保持正常运行。 管理服务主体增加了复杂性，这就是更易于使用托管标识的原因。 相同的权限要求适用于服务主体和托管标识。

*托管标识*本质上是服务主体的包装，使其管理更简单。 根据 Azure Active Directory 默认值，MI 的凭据轮换每46天自动发生一次。 AKS 同时使用系统分配的和用户分配的托管标识类型。 这些标识目前是不可变的。 若要了解详细信息，请参阅[Azure 资源的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

## <a name="before-you-begin"></a>开始之前

必须安装了以下资源：

- Azure CLI，版本 2.2.0 或更高版本

## <a name="limitations"></a>限制

* 当前不支持自带托管标识。
* 只能在创建群集的过程中启用具有托管标识的 AKS 群集。
* 无法更新或升级现有的 AKS 群集以启用托管标识。
* 在群集**升级**操作期间，托管标识暂时不可用。

## <a name="summary-of-managed-identities"></a>托管标识摘要

AKS 对内置服务和加载项使用多个托管标识。

| 标识                       | “属性”    | 用例 | 默认权限 | 自带标识
|----------------------------|-----------|----------|
| 控制面板 | 不可见 | 由 AKS 用于管理网络资源，例如，为入口、公共 IP 等创建负载均衡器。| 节点资源组的参与者角色 | 目前不支持
| Kubelet | AKS 群集名称-agentpool | 用 Azure 容器注册表（ACR）进行身份验证 | 节点资源组的 "读取者" 角色 | 目前不支持
| 加载项 | AzureNPM | 无需标识 | 不可用 | 否
| 加载项 | AzureCNI 网络监视 | 无需标识 | 不可用 | 否
| 加载项 | azurepolicy （守卫） | 无需标识 | 不可用 | 否
| 加载项 | azurepolicy | 无需标识 | 不可用 | 否
| 加载项 | Calico | 无需标识 | 不可用 | 否
| 加载项 | 仪表板 | 无需标识 | 不可用 | 否
| 加载项 | HTTPApplicationRouting | 管理所需的网络资源 | 节点资源组的 "读取者" 角色，DNS 区域的参与者角色 | 否
| 加载项 | 入口应用程序网关 | 管理所需的网络资源| 节点资源组的参与者角色 | 否
| 加载项 | omsagent | 用于将 AKS 指标发送到 Azure Monitor | 监视指标发布者角色 | 否
| 加载项 | 虚拟节点（ACIConnector） | 管理 Azure 容器实例（ACI）所需的网络资源 | 节点资源组的参与者角色 | 否


## <a name="create-an-aks-cluster-with-managed-identities"></a>使用托管标识创建 AKS 群集

现在可以使用以下 CLI 命令创建具有托管标识的 AKS 群集。

首先，创建 Azure 资源组：

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

然后，创建 AKS 群集：

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

使用托管标识成功创建群集包含此服务主体配置文件信息：

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

使用以下命令查询控制平面托管标识的 objectid：

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

结果应该如下所示：

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> 若要创建和使用自己的 VNet、静态 IP 地址或附加到工作节点资源组之外的 Azure 磁盘，请使用群集系统分配的托管标识的 PrincipalID 来执行角色分配。 有关角色分配的详细信息，请参阅[委派对其他 Azure 资源的访问权限](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)。
>
> Azure 云提供程序使用的群集托管标识的权限授予可能需要60分钟才能填充。

最后，获取用于访问群集的凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

将在几分钟内创建该群集。 然后，你可以将应用程序工作负荷部署到新群集，并与之进行交互，就像你对基于服务主体的 AKS 群集进行的一样。

## <a name="next-steps"></a>后续步骤
* 使用[Azure 资源管理器（ARM）模板][aks-arm-template]创建启用了托管身份的群集。

<!-- LINKS - external -->
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

---
title: 在 Azure Kubernetes Service （AKS）上启用基于主机的加密
description: 了解如何在 Azure Kubernetes 服务（AKS）群集中配置基于主机的加密
services: container-service
ms.topic: article
ms.date: 07/10/2020
ms.openlocfilehash: d2b34d8c3090eb6ae3f1445ff1fc663d90367977
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517716"
---
# <a name="host-based-encryption-on-azure-kubernetes-service-aks-preview"></a>Azure Kubernetes Service （AKS）上基于主机的加密（预览版）

通过基于主机的加密，存储在 AKS 代理节点 Vm 的 VM 主机上的数据将静态加密，并加密为存储服务。 这意味着临时磁盘会以平台管理的密钥加密。 操作系统和数据磁盘的缓存会以平台管理的密钥或客户托管的密钥进行加密，具体取决于在这些磁盘上设置的加密类型。 默认情况下，使用 AKS 时，操作系统和数据磁盘使用平台管理的密钥进行静态加密，这意味着这些磁盘的缓存也会默认使用平台管理的密钥进行静态加密。  可以[在 Azure Kubernetes 服务中的 azure 磁盘后，按 "自带密钥（BYOK）"](azure-disk-customer-managed-keys.md)指定自己的托管密钥。 这些磁盘的缓存还将使用您在此步骤中指定的密钥进行加密。


## <a name="before-you-begin"></a>开始之前

此功能只能在创建群集或创建节点池时设置。

> [!NOTE]
> 基于主机的加密在支持 Azure 托管磁盘的服务器端加密的[azure 区域][supported-regions]中提供，并且仅具有特定[支持的 VM 大小][supported-sizes]。

### <a name="prerequisites"></a>先决条件

- 确保已 `aks-preview` 安装 CLI extension v 0.4.55 或更高版本
- 确保 `EncryptionAtHost` 功能标志处于 `Microsoft.Compute` 启用状态。
- 确保 `EnableEncryptionAtHostPreview` 功能标志处于 `Microsoft.ContainerService` 启用状态。

### <a name="register-encryptionathost--preview-features"></a>注册 `EncryptionAtHost` 预览功能

若要创建使用基于主机的加密的 AKS 群集，必须 `EnableEncryptionAtHostPreview` `EncryptionAtHost` 在订阅上启用和功能标志。

`EncryptionAtHost`使用[az feature register][az-feature-register]命令注册功能标志，如以下示例中所示：

```azurecli-interactive
az feature register --namespace "Microsoft.Compute" --name "EncryptionAtHost"

az feature register --namespace "Microsoft.ContainerService"  --name "EnableEncryptionAtHostPreview"
```

状态显示为“已注册”需要几分钟时间**。 可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.Compute/EncryptionAtHost')].{Name:name,State:properties.state}"

az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEncryptionAtHostPreview')].{Name:name,State:properties.state}"
```

准备就绪后， `Microsoft.ContainerService` `Microsoft.Compute` 使用[az provider register][az-provider-register]命令刷新和资源提供程序的注册：

```azurecli-interactive
az provider register --namespace Microsoft.Compute

az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> AKS 预览功能是自助式选择加入功能。 预览版“按原样”提供，并且仅在“可用情况下”提供，不包含在服务级别协议和有限保障中。 AKS 预览版的内容部分包含在客户支持中，我们只能尽力提供支持。 因此，这些功能不应用于生产。 有关其他信息，请参阅以下支持文章：
>
> - [AKS 支持策略](support-policies.md)
> - [Azure 支持常见问题](faq.md)

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要创建基于主机的加密的 AKS 群集，需要使用最新的*AKS* CLI 扩展。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展，或使用[az extension update][az-extension-update]命令检查是否有任何可用更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>限制

- 只能在新的节点池或新群集上启用。
- 只能在支持 Azure 托管磁盘的服务器端加密且仅支持特定[支持的 VM 大小][supported-sizes]的[azure 区域][supported-regions]中启用。
- 需要一个基于虚拟机规模集（VMSS）的 AKS 群集和节点池作为*VM 集类型*。

## <a name="use-host-based-encryption-on-new-clusters-preview"></a>在新群集上使用基于主机的加密（预览版）

将群集代理节点配置为在创建群集时使用基于主机的加密。 使用 `--aks-custom-headers` 标志设置 `EnableEncryptionAtHost` 标头。

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

如果要创建不包含基于主机的加密的群集，可以通过省略自定义参数来执行此操作 `--aks-custom-headers` 。

## <a name="use-host-based-encryption-on-existing-clusters-preview"></a>在现有群集上使用基于主机的加密（预览版）

通过向群集中添加新的节点池，可以在现有群集上启用基于主机的加密。 使用标志配置新的节点池以使用基于主机的加密 `--aks-custom-headers` 。

```azurecli
az aks nodepool add --name hostencrypt --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS2_v2 -l westus2 --aks-custom-headers EnableEncryptionAtHost=true
```

如果要在没有基于主机的加密功能的情况下创建新的节点池，可以通过省略自定义参数来执行此操作 `--aks-custom-headers` 。

## <a name="next-steps"></a>后续步骤

查看[AKS 群集安全性的最佳实践][best-practices-security]了解有关[基于主机的加密](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)的详细信息。


<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[supported-regions]: ../virtual-machines/linux/disk-encryption.md#supported-regions
[supported-sizes]: ../virtual-machines/linux/disk-encryption.md#supported-vm-sizes
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

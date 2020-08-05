---
title: 升级 Azure Kubernetes Service (AKS) 节点映像
description: 了解如何升级 AKS 群集节点和节点池上的映像。
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 040f4378e01c3696b9a74bfcc27230503828f19a
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562781"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>预览版-Azure Kubernetes Service (AKS) 节点映像升级

AKS 支持升级节点上的映像，因此你可以使用最新的 OS 和运行时更新。 AKS 每周提供一个新的映像，并提供最新更新，因此，定期升级节点以获取最新功能（包括 Linux 或 Windows 修补程序）是有益的。 本文介绍如何升级 AKS 群集节点映像以及如何更新节点池映像，而无需升级 Kubernetes 版本。

如果你有兴趣了解 AKS 提供的最新映像，请参阅[AKS 发行说明](https://github.com/Azure/AKS/releases)，了解详细信息。

有关升级群集的 Kubernetes 版本的信息，请参阅[Upgrade a AKS cluster][upgrade-cluster]。

## <a name="register-the-node-image-upgrade-preview-feature"></a>注册节点映像升级预览功能

若要在预览期间使用节点映像升级功能，需要注册该功能。

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

完成注册需要几分钟时间。 使用以下命令验证该功能是否已注册：

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

在预览期间，你需要*aks* CLI 扩展才能使用节点映像升级。 使用[az extension add][az-extension-add]命令，然后使用[az extension update][az-extension-update]命令检查是否有任何可用的更新：

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

当状态显示为“已注册”时，使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令来刷新 `Microsoft.ContainerService` 资源提供程序的注册：

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

## <a name="upgrade-all-nodes-in-all-node-pools"></a>升级所有节点池中的所有节点

升级节点映像是通过来完成的 `az aks upgrade` 。 若要升级节点映像，请使用以下命令：

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

在升级过程中，请通过以下命令检查节点映像的状态， `kubectl` 以获取标签并筛选出当前节点图像信息：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升级完成后，请使用 `az aks show` 获取更新的节点池详细信息。 当前节点图像显示在 `nodeImageVersion` 属性中。

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>升级特定节点池

升级节点池上的映像类似于升级群集上的映像。

若要更新节点池的 OS 映像而不执行 Kubernetes 群集升级，请使用 `--node-image-only` 以下示例中的选项：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

在升级过程中，请通过以下命令检查节点映像的状态， `kubectl` 以获取标签并筛选出当前节点图像信息：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升级完成后，请使用 `az aks nodepool show` 获取更新的节点池详细信息。 当前节点图像显示在 `nodeImageVersion` 属性中。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>通过节点冲击升级节点映像

若要加快节点映像升级过程，可以使用可自定义的节点浪涌值升级节点映像。 默认情况下，AKS 使用另一个节点来配置升级。

如果要提高升级速度，请使用 `--max-surge` 值来配置要用于升级的节点数，使其更快完成。 若要详细了解各种设置的权衡 `--max-surge` ，请参阅[自定义节点浪涌升级][max-surge]。

以下命令设置用于执行节点映像升级的最大冲击值：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

在升级过程中，请通过以下命令检查节点映像的状态， `kubectl` 以获取标签并筛选出当前节点图像信息：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

使用 `az aks nodepool show` 获取更新的节点池详细信息。 当前节点图像显示在 `nodeImageVersion` 属性中。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>后续步骤

- 有关最新节点映像的信息，请参阅[AKS 发行说明](https://github.com/Azure/AKS/releases)。
- 了解如何升级[AKS 群集][upgrade-cluster]的 Kubernetes 版本。
- [将安全更新和内核更新应用于 Azure Kubernetes 服务 (AKS) 中的 Linux 节点][security-update]
- 了解多个节点池的详细信息，以及如何通过[创建和管理多个节点池][use-multiple-node-pools]升级节点池。

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

---
title: 迁移到 Azure Kubernetes 服务（AKS）
description: 迁移到 Azure Kubernetes 服务（AKS）。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/07/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: c4ed78dc9fd277fc61a923364519e338a9f720c2
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290387"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>迁移到 Azure Kubernetes 服务（AKS）

本文可帮助你规划和执行成功迁移到 Azure Kubernetes Service （AKS）的功能。 为帮助您做出关键决策，本指南提供了 AKS 的当前推荐配置的详细信息。 本文不介绍每种情况，在适当的情况下，本文包含了有关规划成功迁移的更详细信息的链接。

本文档可用于帮助支持以下方案：

* 将[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)支持的 AKS 群集迁移到[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* 迁移 AKS 群集以使用[标准 SKU 负载均衡器](https://docs.microsoft.com/azure/aks/load-balancer-standard)
* 从[Azure 容器服务（ACS）迁移-从2020年1月31日停](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/)用到 AKS
* 从[AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)迁移到 AKS
* 从非 Azure 基于 Azure 的 Kubernetes 群集迁移到 AKS

迁移时，请确保目标 Kubernetes 版本位于 AKS 支持的窗口中。 如果使用较旧的版本，它可能不在支持的范围内，并且需要升级 AKS 支持的版本。 有关详细信息，请参阅[AKS 支持的 Kubernetes 版本](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)。

如果要迁移到较新版本的 Kubernetes，请查看[Kubernetes 版本和版本偏差支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)。

根据你的方案，多个开源工具可帮助你进行迁移：

* [Velero](https://velero.io/) （需要 Kubernetes 1.7 +）
* [Azure Kube CLI 扩展](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

在本文中，我们将概括说明的迁移详细信息：

> [!div class="checklist"]
> * 具有标准负载均衡器和虚拟机规模集的 AKS
> * 现有附加的 Azure 服务
> * 确保有效配额
> * 高可用性和业务连续性
> * 无状态应用程序的注意事项
> * 有状态应用程序的注意事项
> * 部署群集配置

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>具有标准负载均衡器和虚拟机规模集的 AKS

AKS 是一种托管服务，提供了具有较低管理开销的独特功能。 作为托管服务的结果，你必须从 AKS 支持的一组[区域](https://docs.microsoft.com/azure/aks/quotas-skus-regions)中进行选择。 从现有群集到 AKS 的转换可能需要修改现有应用程序，使其在 AKS 托管控制平面上保持正常运行。

建议使用[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets)和[Azure 标准负载均衡器](https://docs.microsoft.com/azure/aks/load-balancer-standard)支持的 AKS 群集，以确保获得多种功能，例如[多个节点池](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)、[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)、[授权 IP 范围](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges)、[群集自动缩放程序](https://docs.microsoft.com/azure/aks/cluster-autoscaler)、[用于 AKS 的 Azure 策略](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)和发布时的其他新功能。   

[虚拟机可用性集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets)支持的 AKS 群集对其中许多功能缺乏支持。

以下示例创建一个具有虚拟机规模集支持的单节点池的 AKS 群集。 它使用标准负载均衡器。 它还会在群集的节点池中启用群集自动缩放程序，并将最小值设置为*1* ，最大值为*3*个节点：

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>现有附加的 Azure 服务

迁移群集时，你可能已附加外部 Azure 服务。 这些操作不需要重新执行资源，但需要更新从以前到新群集的连接以维护功能。

* Azure 容器注册表
* Log Analytics
* Application Insights
* 流量管理器
* 存储器帐户
* 外部数据库

## <a name="ensure-valid-quotas"></a>确保有效配额

由于在迁移期间要将其他虚拟机部署到订阅中，因此，应该检查配额和限制是否足以应对这些资源。 可能需要请求增加[vCPU 配额](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)。

可能需要请求增加[网络配额](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests)，以确保不会耗尽 ip。 有关其他信息，请参阅[AKS 的网络和 IP 范围](https://docs.microsoft.com/azure/aks/configure-kubenet)。

有关详细信息，请参阅[Azure 订阅和服务限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。 若要检查当前配额，请在 Azure 门户中，选择 "[订阅" 边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，选择你的订阅，然后选择 "**使用情况 + 配额**"。

## <a name="high-availability-and-business-continuity"></a>高可用性和业务连续性

如果你的应用程序无法处理停机时间，你将需要遵循高可用性迁移方案的最佳实践。  对于复杂的业务连续性规划、灾难恢复和最大化运行时间，最佳做法超出了本文档的讨论范围。  有关详细信息，请参阅[Azure Kubernetes Service （AKS）中有关业务连续性和灾难恢复的最佳实践](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)。

对于复杂的应用程序，我们通常会分阶段迁移，而不是一次性整个迁移。 这意味着新旧环境可能需要通过网络进行通信。 以前使用 `ClusterIP` 服务进行通信的应用程序可能需要公开为类型 `LoadBalancer` 并适当地进行保护。

若要完成迁移，你需要将客户端指向在 AKS 上运行的新服务。 建议通过更新 DNS 来重定向流量，使之指向位于 AKS 群集前面的负载均衡器。

[Azure 流量管理器](https://docs.microsoft.com/azure/traffic-manager/)可以将客户定向到所需的 Kubernetes 群集和应用程序实例。  流量管理器是一种基于 DNS 的流量负载均衡器，可以在区域之间分配网络流量。  为了获得最佳性能和冗余，请在应用程序流量进入 AKS 群集之前，直接通过流量管理器进行所有应用程序流量。  在 multicluster 部署中，客户应连接到流量管理器 DNS 名称，该名称指向每个 AKS 群集上的服务。 使用流量管理器终结点定义这些服务。 每个终结点都是*服务负载均衡器 IP*。 使用此配置可将网络流量从一个区域中的流量管理器终结点定向到不同区域中的终结点。

![与流量管理器 AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure 前门服务](https://docs.microsoft.com/azure/frontdoor/front-door-overview)是路由 AKS 群集流量的另一种选择。  在 Azure Front Door 服务中可以进行优化以实现最佳性能以及进行即时全球故障转移以实现高可用性，并以此定义、管理和监视 Web 流量的全局路由。 

### <a name="considerations-for-stateless-applications"></a>无状态应用程序的注意事项 

无状态应用程序的迁移最直截了当。 将资源定义（YAML 或 Helm）应用到新群集，确保一切按预期运行，然后重定向流量以激活新群集。

### <a name="considerations-for-stateful-applications"></a>有状态应用程序的注意事项

仔细规划有状态应用程序的迁移，以避免数据丢失或意外停机。

如果使用 Azure 文件，可以将文件共享作为卷装载到新群集中：
* [装载静态 Azure 文件作为卷](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

如果使用 Azure 托管磁盘，则只有在附加到任何 VM 时才能装载磁盘：
* [装载静态 Azure 磁盘作为卷](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

如果这两种方法都不起作用，则可以使用备份和还原选项：
* [Azure 上的 Velero](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Azure 文件

与磁盘不同，Azure 文件可同时装载到多个主机。 在 AKS 群集中，Azure 和 Kubernetes 不会阻止你创建 ACS 群集仍然使用的 pod。 若要防止数据丢失和意外行为，请确保群集不会同时写入同一文件。

如果你的应用程序可以承载指向同一文件共享的多个副本，请执行无状态迁移步骤，并将 YAML 定义部署到新群集。 否则，可以采用包括以下步骤的可行迁移方法：

* 验证应用程序是否正常工作。
* 将实时流量指向新的 AKS 群集。
* 断开旧群集的连接。

如果要从空共享开始，并复制源数据，可以使用[`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest)命令来迁移数据。


#### <a name="migrating-persistent-volumes"></a>迁移永久性卷

如果要将现有的永久性卷迁移到 AKS，通常需要执行以下步骤：

* 向应用程序停止写入。 （此步骤是可选的，需要停机。）
* 拍摄磁盘快照。
* 从快照创建新的托管磁盘。
* 在 AKS 中创建永久性卷。
* 更新 pod 规范以[使用现有卷](https://docs.microsoft.com/azure/aks/azure-disk-volume)，而不是 PersistentVolumeClaims （静态预配）。
* 将应用程序部署到 AKS。
* 验证应用程序是否正常工作。
* 将实时流量指向新的 AKS 群集。

> [!IMPORTANT]
> 如果选择不停止写入，则需要将数据复制到新部署。 否则，你将会错过拍摄磁盘快照后写入的数据。

某些开源工具可帮助你创建托管磁盘并在 Kubernetes 群集之间迁移卷：

* [Azure CLI 磁盘复制扩展](https://github.com/noelbundick/azure-cli-disk-copy-extension)跨资源组和 Azure 区域复制和转换磁盘。
* [Azure KUBE CLI 扩展](https://github.com/yaron2/azure-kube-cli)枚举 ACS Kubernetes 卷，并将其迁移到 AKS 群集。


### <a name="deployment-of-your-cluster-configuration"></a>部署群集配置

建议使用现有的持续集成（CI）和持续交付（CD）管道将已知良好的配置部署到 AKS。 你可以使用 Azure Pipelines 来[生成应用程序并将其部署到 AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops)。 克隆现有的部署任务，并确保 `kubeconfig` 指向新的 AKS 群集。

如果无法做到这一点，请从现有 Kubernetes 群集导出资源定义，并将其应用于 AKS。 可以使用 `kubectl` 导出对象。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

本文总结了的迁移详细信息：

> [!div class="checklist"]
> * 具有标准负载均衡器和虚拟机规模集的 AKS
> * 现有附加的 Azure 服务
> * 确保有效配额
> * 高可用性和业务连续性
> * 无状态应用程序的注意事项
> * 有状态应用程序的注意事项
> * 部署群集配置

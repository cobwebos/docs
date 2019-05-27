---
title: 从 Azure 容器服务 (ACS) 迁移到 Azure Kubernetes 服务 (AKS)
description: 从 Azure 容器服务 (ACS) 迁移到 Azure Kubernetes 服务 (AKS)。
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977714"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>从 Azure 容器服务 (ACS) 迁移到 Azure Kubernetes 服务 (AKS)

本文可帮助您规划和执行成功迁移与 kubernetes 配合使用的 Azure 容器服务 (ACS) 和 Azure Kubernetes 服务 (AKS) 之间。 为了帮助您做出关键决策，本指南详细介绍了 ACS 与 AKS 之间的差异，并提供迁移过程的概述。

## <a name="differences-between-acs-and-aks"></a>ACS 与 AKS 之间的差异

ACS 和 AKS 中对迁移造成影响一些关键部分不同。 任何迁移之前，应查看并计划解决以下差异：

* AKS 节点使用[托管磁盘](../virtual-machines/windows/managed-disks-overview.md)。
    * 必须将非托管的磁盘转换之前可以将它们附加到 AKS 节点。
    * 自定义`StorageClass`对象必须从更改 Azure 磁盘`unmanaged`到`managed`。
    * 任何`PersistentVolumes`应使用`kind: Managed`。
* AKS 支持[多个节点池](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)（目前处于预览状态）。
* 基于 Windows Server 上的节点是当前处于[在 AKS 中的预览](https://azure.microsoft.com/blog/kubernetes-on-azure/)。
* AKS 支持的一组有限[区域](https://docs.microsoft.com/azure/aks/quotas-skus-regions)。
* AKS 是带有托管 Kubernetes 控制平面的托管服务。 您可能需要修改您的应用程序，如果以前已修改的配置 ACS 主机。

## <a name="differences-between-kubernetes-versions"></a>Kubernetes 版本之间的差异

如果要 （例如，从到 1.9.x 允许在 1.7.x) 迁移到较新版本的 Kubernetes，请查看以下资源，以了解 Kubernetes API 的几个更改：

* [迁移到 CustomResourceDefinition ThirdPartyResource](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [1.8 和 1.9 版中的工作负荷 API 更改](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>迁移注意事项

### <a name="agent-pools"></a>代理池

虽然 AKS 管理 Kubernetes 控制平面，但仍可以定义的大小和要包含在新群集中的节点数。 假设你要在 ACS 与 AKS 之间设置 1 对 1 的映射，则需要捕获现有的 ACS 节点信息。 创建新的 AKS 群集时，请使用此数据。

示例：

| 名称 | Count | VM 大小 | 操作系统 |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 第 | Standard_D2_v2 | Windows |

由于在迁移期间要将其他虚拟机部署到订阅中，因此，应该检查配额和限制是否足以应对这些资源。 

有关详细信息，请参阅[Azure 订阅和服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits)。 若要查看当前的配额，在 Azure 门户中，请转到[订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，选择你的订阅，并选择**使用情况 + 配额**。

### <a name="networking"></a>网络

对于复杂的应用程序，我们通常会分阶段迁移，而不是一次性整个迁移。 这意味着，旧的和新环境可能需要通过网络进行通信。 以前使用过的应用程序`ClusterIP`服务进行通信，可能需要公开类型为`LoadBalancer`并进行适当地保护。

若要完成迁移，你将想要使客户端指向在 AKS 运行的新服务。 我们建议通过更新 DNS 以指向前面在 AKS 群集位于负载均衡器流量重定向。

### <a name="stateless-applications"></a>无状态应用程序

无状态应用程序的迁移最直截了当。 将将 YAML 定义应用到新群集，确保一切按预期运行，并将激活新群集的流量重定向。

### <a name="stateful-applications"></a>有状态应用程序

请仔细计划的有状态应用程序以避免数据丢失或意外停机时间的迁移。

#### <a name="highly-available-applications"></a>高度可用的应用程序

你可以部署在高可用性配置某些有状态应用程序。 这些应用程序可以在副本之间复制数据。 如果您当前使用这种部署，你可能能够在新的 AKS 群集上创建新的成员，然后再迁移与下游调用方对影响最小。 通常情况下，对于此方案的迁移步骤如下：

1. 在 AKS 上创建新的辅助副本。
2. 等待数据的复制。
3. 超过无法进行新的主副本的辅助副本。
4. 流量指向 AKS 群集。

#### <a name="migrating-persistent-volumes"></a>永久性卷迁移

如果将现有的永久性卷迁移到 AKS，将通常遵循以下步骤：

1. 停止写入应用程序。 （此步骤是可选的需要的停机时间。)
2. 创建磁盘的快照。
3. 从快照创建新的托管的磁盘。
4. 在 AKS 中创建永久性卷。
5. 更新到 pod 规范[使用现有卷](https://docs.microsoft.com/azure/aks/azure-disk-volume)而不是 PersistentVolumeClaims （静态预配）。
6. 应用程序部署到 AKS。
7. 验证。
8. 流量指向 AKS 群集。

> [!IMPORTANT]
> 如果你不愿意停止写入，您需要将数据复制到新的部署。 否则将丢失已写入您拍摄磁盘快照后的数据。

一些开放源代码工具可以帮助您创建的托管的磁盘和卷 Kubernetes 群集之间进行迁移：

* [Azure CLI 磁盘复制扩展](https://github.com/noelbundick/azure-cli-disk-copy-extension)复制并将磁盘转换在资源组和 Azure 区域。
* [Azure Kube CLI 扩展](https://github.com/yaron2/azure-kube-cli)枚举 ACS Kubernetes 卷，并将其迁移到 AKS 群集。

#### <a name="azure-files"></a>Azure 文件

与磁盘不同，Azure 文件可同时装载到多个主机。 在 AKS 群集，Azure 和 Kubernetes 不会阻止你创建的 pod 仍使用 ACS 群集。 若要防止数据丢失和意外的行为，请确保群集未在同一时间写入到相同的文件。

如果你的应用程序可以承载指向相同的文件共享的多个副本，请执行无状态的迁移步骤和部署到新群集的 YAML 定义。 否则，可以采用包括以下步骤的可行迁移方法：

1. 应用程序部署到 AKS 副本计数为 0。
2. 缩放 ACS 上为 0 的应用程序。 （此步骤需要停机时间。）
3. 缩放 AKS 上的应用程序，最大 1。
4. 验证。
5. 流量指向 AKS 群集。

如果你想要使用的空共享并使源数据的副本启动，则可以使用[ `az storage file copy` ](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest)命令将数据迁移。

### <a name="deployment-strategy"></a>部署策略

我们建议使用现有的 CI/CD 管道将部署到 AKS 已知的正确配置。 克隆现有的部署任务，并确保`kubeconfig`指向新的 AKS 群集。

如果这不可能，从 ACS 中导出的资源定义，然后将它们应用于 AKS。 可以使用 `kubectl` 导出对象。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

多个开放源代码工具可帮助，具体取决于您的部署需要：

* [Velero](https://github.com/heptio/ark) （此工具需要 Kubernetes 1.7）。
* [Azure Kube CLI 扩展](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>迁移步骤

1. [创建 AKS 群集](https://docs.microsoft.com/azure/aks/create-cluster)通过 Azure 门户、 Azure CLI 或 Azure 资源管理器模板。

   > [!NOTE]
   > 适用于在 AKS 中找到的示例 Azure 资源管理器模板[Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) GitHub 上的存储库。

2. 对您的 YAML 定义进行任何必要的更改。 例如，替换`apps/v1beta1`与`apps/v1`为`Deployments`。

3. [将卷迁移](#migrating-persistent-volumes)（可选） 从 ACS 群集到 AKS 群集。

4. 使用 CI/CD 系统应用程序部署到 AKS。 或使用 kubectl 应用 YAML 定义。

5. 验证。 请确保你的应用程序按预期方式工作，并且通过复制了迁移的所有数据。

6. 将流量重定向。 更新 DNS，使客户端指向 AKS 部署。

7. 完成迁移后任务。 如果你迁移的卷，不选择停止写入，则将该数据复制到新群集。

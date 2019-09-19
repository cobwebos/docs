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
ms.openlocfilehash: 66f76a8a706f60df786786cbd1ce00b7eafd8d7e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097885"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>从 Azure 容器服务 (ACS) 迁移到 Azure Kubernetes 服务 (AKS)

本文帮助你规划并成功执行 Azure 容器服务 (ACS) 与 Kubernetes 和 Azure Kubernetes 服务 (AKS) 之间的迁移。 为帮助你做出关键决策，本指南详细描述了 ACS 与 AKS 之间的差异并概述了迁移过程。

## <a name="differences-between-acs-and-aks"></a>ACS 与 AKS 之间的差异

ACS 与 AKS 之间存在一些会影响迁移的重要差异。 在执行任何迁移之前，应查看以下差异并做出相应的规划：

* AKS 节点使用[托管磁盘](../virtual-machines/windows/managed-disks-overview.md)。
    * 非托管磁盘必须在转换之后才可附加到 AKS 节点。
    * Azure 磁盘的自定义 `StorageClass` 对象必须从 `unmanaged` 更改为 `managed`。
    * 任何 `PersistentVolumes` 应使用 `kind: Managed`。
* AKS 支持[多个节点池](https://docs.microsoft.com/azure/aks/use-multiple-node-pools)（当前为预览版）。
* 基于 Windows Server 的节点当前在 AKS 中为[预览版](https://azure.microsoft.com/blog/kubernetes-on-azure/)。
* AKS 支持有限的一组[区域](https://docs.microsoft.com/azure/aks/quotas-skus-regions)。
* AKS 是带有托管 Kubernetes 控制平面的托管服务。 如果以前修改了 ACS 主节点的配置，则现在可能需要修改应用程序。

## <a name="differences-between-kubernetes-versions"></a>Kubernetes 版本之间的差异

如果要迁移到较新版本的 Kubernetes，请查看以下资源以了解 Kubernetes 版本控制策略：

* [Kubernetes 版本和版本歪斜支持策略](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions)

## <a name="migration-considerations"></a>迁移注意事项

### <a name="agent-pools"></a>代理池

尽管 AKS 会管理 Kubernetes 控制平面，但你仍需定义要包含在新群集中的节点的大小和数目。 假设你要在 ACS 与 AKS 之间设置 1 对 1 的映射，则需要捕获现有的 ACS 节点信息。 新建 AKS 群集时将要使用此数据。

例如：

| 姓名 | Count | VM 大小 | 操作系统 |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

由于在迁移期间要将其他虚拟机部署到订阅中，因此，应该检查配额和限制是否足以应对这些资源。 

有关详细信息，请参阅 [Azure 订阅和服务限制](https://docs.microsoft.com/azure/azure-subscription-service-limits)。 若要查看当前配额，请在 Azure 门户中转到[订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，选择自己的订阅，然后选择“用量 + 配额”。

### <a name="networking"></a>网络

对于复杂的应用程序，我们通常会分阶段迁移，而不是一次性整个迁移。 这意味着，新旧环境可能需要通过网络进行通信。 以前能够使用 `ClusterIP` 服务进行通信的应用程序可能需要公开为 `LoadBalancer` 类型，并得到相应的保护。

若要完成迁移，需将客户端指向 AKS 上运行的新服务。 建议通过将 DNS 更新为指向 AKS 群集前面的负载均衡器，来重定向流量。

### <a name="stateless-applications"></a>无状态应用程序

无状态应用程序的迁移最直截了当。 将 YAML 定义应用到新群集，确保一切按预期进行，然后重定向流量以激活新群集。

### <a name="stateful-applications"></a>有状态应用程序

精心规划有状态应用程序的迁移，以避免数据丢失或意外停机。

#### <a name="highly-available-applications"></a>高可用性应用程序

可在高可用性配置中部署某些有状态应用程序。 这些应用程序可跨副本复制数据。 如果你当前使用此类部署，可以在新 AKS 群集上创建新成员，然后在尽量减小对下游调用方造成影响的情况下执行迁移。 此方案的迁移步骤通常包括：

1. 在 AKS 上创建新的次要副本。
2. 等待数据复制完成。
3. 故障转移，使次要副本成为主要副本。
4. 将流量指向 AKS 群集。

#### <a name="migrating-persistent-volumes"></a>迁移永久性卷。

将现有的永久性卷迁移到 AKS 时，通常需要遵循以下步骤：

1. 暂停写入到应用程序。 （此步骤是可选的，需要停机。）
2. 创建磁盘的快照。
3. 从快照创建新的托管磁盘。
4. 在 AKS 中创建永久性卷。
5. 将 Pod 规范更新为[使用现有卷](https://docs.microsoft.com/azure/aks/azure-disk-volume)而不是 PersistentVolumeClaims（静态预配）。
6. 将应用程序部署到 AKS。
7. 验证。
8. 将流量指向 AKS 群集。

> [!IMPORTANT]
> 如果不暂停写入，则需要将数据复制到新部署。 否则在创建磁盘快照后，写入的数据将会丢失。

可以借助一些开源工具来创建托管磁盘，并在 Kubernetes 群集之间迁移卷：

* [Azure CLI 磁盘复制扩展](https://github.com/noelbundick/azure-cli-disk-copy-extension)可跨资源组和 Azure 区域复制并转换磁盘。
* [Azure Kube CLI 扩展](https://github.com/yaron2/azure-kube-cli)可枚举 ACS Kubernetes 卷并将其迁移到 AKS 群集。

#### <a name="azure-files"></a>Azure 文件

与磁盘不同，Azure 文件可同时装载到多个主机。 在 AKS 群集中，Azure 和 Kubernetes 都不会阻止你创建 ACS 群集仍在使用的 Pod。 若要防止数据丢失和意外行为，请确保这两个群集不会同时向相同的文件写入数据。

如果应用程序可以托管指向同一文件共享的多个副本，请遵循无状态迁移步骤，将 YAML 定义部署到新群集。 否则，可以采用包括以下步骤的可行迁移方法：

1. 将应用程序部署到副本计数为 0 的 AKS。
2. 将 ACS 上的应用程序数缩减为 0。 （此步骤需要停机。）
3. 将 AKS 上的应用程序数最大扩展为 1。
4. 验证。
5. 将流量指向 AKS 群集。

若要从空共享开始，然后创建源数据的副本，可以使用 [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) 命令迁移数据。

### <a name="deployment-strategy"></a>部署策略

建议使用现有 CI/CD 管道将已知正常的配置部署到 AKS。 克隆现有的部署任务，并确保 `kubeconfig` 指向新的 AKS 群集。

如果这种做法不可行，请从 ACS 导出资源定义，然后将其应用到 AKS。 可以使用 `kubectl` 导出对象。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

可根据部署需要借助多种开源工具：

* [Velero](https://github.com/heptio/ark)（此工具需要 Kubernetes 1.7）。
* [Azure Kube CLI 扩展](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>迁移步骤

1. 通过 Azure 门户、Azure CLI 或 Azure 资源管理器模板[创建 AKS 群集](https://docs.microsoft.com/azure/aks/create-cluster)。

   > [!NOTE]
   > 在 GitHub 上的 [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) 存储库中找到 AKS 的示例 Azure 资源管理器模板。

2. 对 YAML 定义进行任何必要的更改。 例如，将 `apps/v1beta1` 替换为 `Deployments` 的 `apps/v1`。

3. 将卷从 ACS 群集[迁移](#migrating-persistent-volumes)到 AKS 群集（可选步骤）。

4. 使用 CI/CD 系统将应用程序部署到 AKS。 或使用 kubectl 来应用 YAML 定义。

5. 验证。 确保应用程序按预期运行，并且已复制所有已迁移的数据。

6. 重定向流量。 更新 DNS，使客户端指向 AKS 部署。

7. 完成迁移后的任务。 如果迁移了卷但未暂停写入，请将这些数据复制到新群集。

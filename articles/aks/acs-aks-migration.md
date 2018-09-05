---
title: 从 Azure 容器服务 (ACS) 迁移到 Azure Kubernetes 服务 (AKS)
description: 从 Azure 容器服务 (ACS) 迁移到 Azure Kubernetes 服务 (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: cb143998ac46f7f86b2dbf47b69cee7843418f5d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191401"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>从 Azure 容器服务 (ACS) 迁移到 Azure Kubernetes 服务 (AKS)

本文档旨在帮助你规划并成功执行 Azure 容器服务与 Kubernetes (ACS) 和 Azure Kubernetes 服务 (AKS) 之间的迁移。 本指南详细描述了 ACS 与 AKS 之间的差异并概述了迁移过程，对你的关键决策应有所帮助。

## <a name="differences-between-acs-and-aks"></a>ACS 与 AKS 之间的差异

ACS 与 AKS 之间存在一些会影响迁移的重要差异。 在执行任何迁移之前，应查看以下差异并做出相应的规划。

* AKS 节点使用[托管磁盘](../virtual-machines/windows/managed-disks-overview.md)
    * 非托管磁盘需在转换之后才可附加到 AKS 节点
    * Azure 磁盘的自定义 `StorageClass` 对象需从 `unmanaged` 更改为 `managed`
    * 任何 `PersistentVolumes` 都需要使用 `kind: Managed`
* AKS 目前仅支持一个代理池
* 基于 Windows Server 的节点目前以[个人预览版](https://azure.microsoft.com/en-us/blog/kubernetes-on-azure/)提供。
* 查看 AKS [支持的区域](https://docs.microsoft.com/azure/aks/container-service-quotas)列表
* AKS 是带有托管 Kubernetes 控制平面的托管服务。 如果以前修改了 ACS 主节点的配置，则现在可能需要修改应用程序

### <a name="differences-between-kubernetes-versions"></a>Kubernetes 版本之间的差异

若要迁移到较新版本的 Kubernetes（例如，从 1.7.x 迁移到 1.9.x），需要关注 k8s API 发生的几处更改。

* [将 ThirdPartyResource 迁移到 CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [版本 1.8 和 1.9 中的工作负荷 API 更改](https://kubernetes.io/docs/reference/workloads-18-19/)。

## <a name="migration-considerations"></a>迁移注意事项

### <a name="agent-pools"></a>代理池

尽管 AKS 会管理 Kubernetes 控制平面，但你仍需定义要包含在新群集中的节点的大小和数目。 假设你要在 ACS 与 AKS 之间设置 1 对 1 的映射，则需要捕获现有的 ACS 节点信息。 新建 AKS 群集时将要使用此数据。

示例：

| 名称 | Count | VM 大小 | 操作系统 |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

由于在迁移期间要将其他虚拟机部署到订阅中，因此，应该检查配额和限制是否足以应对这些资源。 可以查看 [Azure 订阅和服务限制](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits)来了解详细信息。 若要查看当前配额，请在 Azure 门户中转到[订阅边栏选项卡](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)，选择自己的订阅，然后选择 `Usage + quotas`。

### <a name="networking"></a>网络

对于复杂的应用程序，我们通常会分阶段迁移，而不是一次性整个迁移。 这意味着，新旧环境可能需要通过网络进行通信。 以前能够使用 `ClusterIP` 服务进行通信的应用程序可能需要公开为 `LoadBalancer` 类型，并得到相应的保护。

若要完成迁移，需将客户端指向 AKS 上运行的新服务。 重定向流量的建议方法是将 DNS 更新为指向 AKS 群集前面的负载均衡器。

### <a name="stateless-applications"></a>无状态应用程序

无状态应用程序的迁移最直截了当。 将 YAML 定义应用到新群集、验证一切是否按预期进行，然后重定向流量，即可让新群集进入活动状态。

### <a name="stateful-applications"></a>有状态应用程序

有状态应用程序的迁移需要经过精心的规划，以避免数据丢失或意外停机。

#### <a name="highly-available-applications"></a>高可用性应用程序

某些有状态应用程序可部署在高可用性配置中，并可跨副本复制数据。 如果上面的描述符合当前部署，则可以在新 AKS 群集上创建新成员，然后在尽量减小对下游调用方造成影响的情况下执行迁移。 此方案的迁移步骤通常包括：

1. 在 AKS 上创建新的次要副本
2. 等待数据复制完成
3. 故障转移，使次要副本成为主要副本
4. 将流量指向 AKS 群集

#### <a name="migrating-persistent-volumes"></a>迁移永久性卷

将现有的永久性卷迁移到 AKS 时，需要考虑多种因素。 一般而言，涉及的步骤包括：

1. （可选）暂停写入到应用程序（需要停机）
2. 创建磁盘快照
3. 从快照创建新的托管磁盘
4. 在 AKS 中创建永久性卷
5. 将 Pod 规范更新为[使用现有卷](https://docs.microsoft.com/en-us/azure/aks/azure-disk-volume)而不是 PersistentVolumeClaims（静态预配）
6. 将应用程序部署到 AKS
7. 验证
8. 将流量指向 AKS 群集

> **重要说明**：如果不暂停写入，则需要将数据复制到新部署，因为自创建磁盘快照后写入的数据将会丢失

可以借助一些开源工具来创建托管磁盘，并在 Kubernetes 群集之间迁移卷。

* [noelbundick/azure-cli-disk-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) - 跨资源组和 Azure 区域复制并转换磁盘
* [yaron2/azure-kube cli](https://github.com/yaron2/azure-kube-cli) - 枚举 ACS Kubernetes 卷并将其迁移到 AKS 群集

#### <a name="azure-files"></a>Azure 文件

与磁盘不同，Azure 文件可同时装载到多个主机。 Azure 和 Kubernetes 都不会阻止你在 ACS 群集仍在使用的 AKS 群集中创建 Pod。 若要防止数据丢失和意外行为，应确保这两个群集不会同时在相同的文件中写入数据。

如果应用程序可以托管指向同一文件共享的多个副本，则你可以遵循无状态迁移步骤，将 YAML 定义部署到新群集。

否则，可以采用包括以下步骤的可行迁移方法：

1. 将应用程序部署到副本计数为 0 的 AKS
2. 将 ACS 上的应用程序数缩减为 0（需要停机）
3. 将 AKS 上的应用程序数最大扩展为 1
4. 验证
5. 将流量指向 AKS 群集

若要从空共享开始，然后创建源数据的副本，可以使用 [`az storage file copy`](https://docs.microsoft.com/en-us/cli/azure/storage/file/copy?view=azure-cli-latest) 命令迁移数据。

### <a name="deployment-strategy"></a>部署策略

建议的方法是使用现有 CI/CD 管道将已知正常的配置部署到 AKS。 克隆现有的部署任务，并确保 `kubeconfig` 指向新的 AKS 群集。

如果这种做法不可行，则需要从 ACS 导出资源定义，然后将其应用到 AKS。 可以使用 `kubectl` 导出对象。

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

此外，可以根据需要借助多个开源工具：

* [heptio/ark](https://github.com/heptio/ark) - 需要 k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>迁移步骤

### <a name="1-create-an-aks-cluster"></a>1.创建 AKS 群集

可以参考文档通过 Azure 门户、Azure CLI 或资源管理器模板[创建 AKS 群集](https://docs.microsoft.com/en-us/azure/aks/create-cluster)。

> 在 GitHub 上的 [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) 存储库中，可以找到 AKS 的示例 Azure 资源管理器模板

### <a name="2-modify-applications"></a>2.修改应用程序

对 YAML 定义进行任何必要的修改。 例如：将 `apps/v1beta1` 替换为 `Deployments` 的 `apps/v1`

### <a name="3-optional-migrate-volumes"></a>3.（可选）迁移卷

将卷从 ACS 群集迁移到 AKS 群集。 在[迁移永久性卷](#Migrating-Persistent-Volumes)部分可以找到更多详细信息。

### <a name="4-deploy-applications"></a>4.部署应用程序

使用 CI/CD 系统将应用程序部署到 AKS，或使用 kubectl 来应用 YAML 定义。

### <a name="5-validate"></a>5.验证

验证应用程序是否按预期运行，并且已复制所有已迁移的数据。

### <a name="6-redirect-traffic"></a>6.重定向流量

更新 DNS，使客户端指向 AKS 部署。

### <a name="7-post-migration-tasks"></a>7.迁移后的任务

如果迁移了卷但未暂停写入，则需要将这些数据复制到新群集。

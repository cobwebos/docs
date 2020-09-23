---
title: 存储配置
description: 介绍启用了 Azure Arc 的数据服务存储配置选项
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: 782a046b92c9d6cf755bfea0551d7f8153faa859
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934352"
---
# <a name="storage-configuration"></a>存储配置

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes 存储概念

Kubernetes 提供基础虚拟化技术堆栈上的基础结构抽象层， (可选) 和硬件。 Kubernetes 将存储提取到存储 **[类](https://kubernetes.io/docs/concepts/storage/storage-classes/)** 的方式。 预配 pod 时，可以指定用于每个卷的存储类。 预配 pod 时，将调用存储类 **[配置程序](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** 来预配存储，然后在该预配的存储上创建一个 **[永久卷](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** ，然后使用 **[永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** 将 pod 装载到永久性卷。

Kubernetes 提供了一种方法，用于存储基础结构提供程序 (也称为 "加载项" ) 扩展 Kubernetes。 存储加载项必须符合 **[容器存储接口标准](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)**。 在此非最终的 **[CSI 驱动程序列表](https://kubernetes-csi.github.io/docs/drivers.html)** 中，可以找到几十个加载项。 使用哪种 CSI 驱动程序将取决于多种因素，例如你是在云托管的托管 Kubernetes 服务中运行，还是在你的硬件上使用哪个 OEM 提供商。

可以通过运行以下命令来查看在 Kubernetes 群集中配置的存储类：

``` terminal
kubectl get storageclass
```

Azure Kubernetes 服务的示例输出 (AKS) 群集：

``` terminal
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

可以通过运行以下命令获取有关存储类的详细信息：

``` terminal
kubectl describe storageclass\<storage class name>
```

示例：

``` terminal
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

可以通过运行以下命令来查看当前预配的持久卷和永久性卷声明：

``` terminal
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

显示永久性卷的示例：

``` terminal

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

显示永久性卷声明的示例：

``` terminal

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>选择存储配置时要考虑的因素

选择正确的存储类对于数据恢复能力和性能非常重要。 如果选择了错误的存储类，会使数据面临出现硬件故障时数据丢失的风险，也可能导致性能不佳的情况。

通常有两种类型的存储：

- **本地存储** -在给定节点上的本地硬盘驱动器上预配的存储。 这种类型的存储在性能方面可能是理想的选择，但需要通过将数据复制到多个节点来专门设计数据冗余。
- **远程共享存储** -在某些远程存储设备上预配的存储-例如，SAN、NAS 或云存储服务（如 EBS 或 Azure 文件）。 此类存储通常自动为数据冗余提供，但通常不像本地存储那样快。

> [!NOTE]
> 现在，如果你使用的是 NFS，则需要在部署 Azure Arc 数据控制器之前在部署配置文件中将 allowRunAsRoot 设置为 true。

### <a name="data-controller-storage-configuration"></a>数据控制器存储配置

适用于数据服务的 Azure Arc 中的某些服务依赖于配置为使用远程共享存储，因为服务不能复制数据。 这些服务位于数据控制器箱的集合中：

|**服务**|**永久性卷声明**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**控制器 SQL 实例**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**控制器 API 服务**|`<namespace>/data-controller`|

预配数据控制器时，将通过传递--存储类来指定要用于其中每个永久性卷的存储类 |-sc 参数设置为， `azdata arc dc create` 或在所使用的部署模板文件的 control.js中设置存储类。

现成提供的部署模板具有指定的、适用于目标环境的默认存储类，但它可以在部署时进行重写。 请参阅用于更改 [部署配置文件](create-data-controller.md) 以在部署时更改数据控制器 pod 的存储类配置的详细步骤。

如果使用--storage 类设置存储类 |-sc 参数存储类将用于日志和数据存储类。 如果在部署模板文件中设置存储类，则可以为日志和数据指定不同的存储类。

为数据控制器盒选择存储类时要考虑的重要因素：

- **必须**使用远程的共享存储类才能确保数据持续性，因此，如果在安装了 pod 后，pod 或节点出现故障，则可以再次连接到永久性卷。
- 写入控制器 SQL 实例、指标 DB 和日志数据库的数据通常非常低，对延迟不敏感，因此，超高性能存储并不重要。 如果用户使用的是 Grafana 和 Kibana 接口，并且有大量的数据库实例，则用户可以更快地执行存储。
- 所需的存储容量是可变的，因为已部署的数据库实例数是为每个数据库实例收集的日志和指标。 数据会在日志和指标 DB 中保留2周后才会被清除。 TODO：每个数据库实例需要多少存储空间？
- 更改存储类后期部署非常困难，未记录并且不受支持。 请确保在部署时正确选择存储类。

> **注意：** 如果未指定存储类，将使用默认的存储类。 每个 Kubernetes 群集只能有一个默认的存储类。 您可以 [更改默认的存储类](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)。

### <a name="database-instance-storage-configuration"></a>数据库实例存储配置

每个数据库实例都包含数据、日志和备份永久性卷。 可以在部署时指定这些永久性卷的存储类。 如果未指定存储类，将使用默认的存储类。

使用或命令创建实例 `azdata arc sql mi create` 时 `azdata arc postgres server create` ，可以使用两个参数来设置存储类：

> **注意：** 其中一些参数正在开发中，将在 `azdata arc sql mi create` 和 `azdata arc postgres server create` 即将发布的版本中使用。

|参数名称，短名称|用途|
|---|---|
|`--storage-class-data`, `-scd`|用于指定所有数据文件（包括事务日志文件）的存储类|
|`--storage-class-logs`, `-scl`|用于指定所有日志文件的存储类|
|`--storage-class-data-logs`, `-scdl`|用于指定数据库事务日志文件的存储类。 **注意：尚不可用。**|
|`--storage-class-backups`, `-scb`|用于指定所有备份文件的存储类。 **注意：尚不可用。**|

下表列出了 Azure SQL 托管实例容器中映射到数据和日志的持久卷的路径：

|参数名称，短名称|Mssql miaa 容器中的路径|说明|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|包含 mssql 安装和其他系统进程的目录。 Mssql 目录包含默认数据 (包括事务日志) 、错误日志 & 备份目录|
|`--storage-class-logs`, `-scl`|/var/log|包含存储控制台输出 (stderr、stdout) 的目录，以及容器内进程的其他日志记录信息|

下表列出了 PostgreSQL 实例容器中的路径，该容器映射到数据和日志的持久卷：

|参数名称，短名称|Postgres 容器中的路径|说明|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|包含 postgres 安装的数据和日志目录|
|`--storage-class-logs`, `-scl`|/var/log|包含存储控制台输出 (stderr、stdout) 的目录，以及容器内进程的其他日志记录信息|

对于数据文件、日志和备份，每个数据库实例都有一个单独的永久性卷。 这意味着，对于这两种类型的文件中的每种类型的 i/o，都将会分离出来，因为卷配置程序将如何预配存储。 每个数据库实例都有其自己的永久性卷声明和永久性卷。

如果给定数据库实例上有多个数据库，则所有数据库都将使用相同的永久卷声明、永久性卷和存储类。 所有备份-差异日志备份和完整备份都将使用相同的永久卷声明和永久性卷。 数据库实例盒的永久性卷声明如下所示：

|**实例**|**永久性卷声明**|
|---|---|
|**Azure SQL 托管实例**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure database for PostgreSQL 实例**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL 超大规模**|`<namespace>/logs-<instance namme>-<ordinal>``<namespace>/data-<instance namme>-<ordinal>` * (序数范围为0到 W，其中 W 为辅助角色的数目) *|

为数据库实例盒选择存储类时要考虑的重要因素：

- 可以在单个 pod 模式或多个 pod 模式中部署数据库实例。 一个 pod 模式的示例是 Azure SQL 托管实例的开发人员实例或通用定价层 Azure SQL 托管实例。 多个 pod 模式的一个示例是高度可用的业务关键定价层 Azure SQL 托管实例。  (注意：定价层正在开发中，但目前不可供客户使用。使用单盒模式部署 ) 的数据库实例 **必须** 使用远程共享的存储类，才能确保数据持续性，并使在安装 pod 后，如果 pod 或节点出现故障，则可以再次连接到永久性卷。 与此相反，高可用 Azure SQL 托管实例使用 Always On 可用性组，以同步或异步方式将数据从一个实例复制到另一个实例。 尤其是在同步复制数据的情况下，始终有多个数据副本（通常为3个副本）。 因此，可以为数据和日志文件使用本地存储或远程共享存储类。 如果使用本地存储，即使盒箱、节点或存储硬件发生故障，仍会保留数据。 由于这种灵活性，你可以选择使用本地存储来提高性能。
- 数据库性能很大程度上是给定存储设备的 i/o 吞吐量。 如果您的数据库是大量读取或大量写入，则应该选择一个存储类，该存储类的下面的硬件是为该类型的工作负荷设计的。 例如，如果数据库主要用于写入，则可以选择 "使用 RAID 0 的本地存储"。 如果数据库主要用于读取少量的 "热数据"，但有大量的冷数据存储量，则可以选择支持分层存储的 SAN 设备。 选择正确的存储类与选择用于任何数据库的存储类型确实不同。
- 如果你使用的是本地存储卷配置程序，则应确保为数据、日志和备份设置的本地卷都登录到不同的基础存储设备上，以避免磁盘 i/o 上的争用。 操作系统还应位于装载到单独磁盘 () 的卷上。 这实质上与物理硬件上的数据库实例遵循相同的指导原则。
- 由于给定实例上的所有数据库共享持久卷声明和永久卷，因此请确保不在同一数据库实例上归置繁忙的数据库实例。 如果可能，请将繁忙数据库单独置于自己的数据库实例，以避免 i/o 争用。 此外，使用节点标签目标为将数据库实例置于不同的节点上，以便在多个节点之间分配总体 i/o 流量。 如果你使用的是虚拟化，则一定要考虑不只是在节点级别分配 i/o 流量，还应考虑给定物理主机上的所有节点 Vm 所发生的组合 i/o 活动。

## <a name="estimating-storage-requirements"></a>估计存储要求
每个包含有状态数据的盒都在此版本中使用两个永久性卷-一个持久卷用于数据，另一个持久卷用于日志。 下表列出了单一数据控制器、Azure SQL 托管实例、Azure Database for PostgreSQL 实例和 Azure PostgreSQL 超大规模实例所需的持久卷数量：

|资源类型|有状态箱数|所需的持久卷数量|
|---|---|---|
|数据控制器|4 (`control` 、 `controldb` 、 `logsdb` 、 `metricsdb`) |4 * 2 = 8|
|Azure SQL 托管实例|1|2|
|Azure Database for PostgreSQL 实例|1| 2|
|Azure PostgreSQL 超大规模|1 + W (W = 辅助角色数量) |2 * (1 + W) |

下表显示了部署示例所需的永久性卷的总数：

|资源类型|实例数|所需的持久卷数量|
|---|---|---|
|数据控制器|1|4 * 2 = 8|
|Azure SQL 托管实例|5|5 * 2 = 10|
|Azure Database for PostgreSQL 实例|5| 5 * 2 = 10|
|Azure PostgreSQL 超大规模|2 (辅助角色数 = 每个实例4个) |2 * 2 * (1 + 4) = 20|
|***永久性卷的总数***||8 + 10 + 10 + 20 = 48|

此计算可用于基于存储配置程序或环境规划 Kubernetes 群集的存储。 例如，如果本地存储配置程序用于具有5个节点的 Kubernetes 群集，则每个节点上的示例部署至少需要10个持久卷的存储。 同样，在预配 Azure Kubernetes Service 时 (AKS) 群集，其中5个节点为节点池选取合适的 VM 大小，以便可以附加10个数据磁盘。 可在 [此处](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)找到有关如何为 AKS 节点的存储需求调整节点大小的详细信息。

## <a name="choosing-the-right-storage-class"></a>选择适当的存储类

### <a name="on-premises-and-edge-sites"></a>本地和边缘站点

Microsoft 及其 OEM、OS 和 Kubernetes 合作伙伴正在使用 Azure Arc 数据服务的认证计划。 此程序将向客户提供与认证测试工具包类似的测试结果。 这些测试将评估功能兼容性、压力测试结果以及性能和可伸缩性。 其中每个测试结果将指示所使用的操作系统、使用的 Kubernetes 分发、使用的硬件、所使用的 CSI 外接程序以及使用的存储类。 这将帮助客户选择最佳存储类、OS、Kubernetes 分发和硬件以满足其要求。 有关此计划和初始测试结果的详细信息，请详细了解 Azure Arc 数据服务的公开上市。

#### <a name="public-cloud-managed-kubernetes-services"></a>公有云，托管 Kubernetes 服务

对于基于云的公共、托管的 Kubernetes 服务，我们可以提出以下建议：

|公有云服务|建议|
|---|---|
|**Azure Kubernetes 服务 (AKS)**|Azure Kubernetes Service (AKS) 有两种类型的存储-Azure 文件和 Azure 磁盘。 每种类型的存储有两个定价/性能层-标准 (HDD) 和高级 (SSD) 。 因此，在 (AKS 中提供的四个存储类 `azurefile`) ， `azurefile-premium` (azure 文件高级层) ， `default` (azure 磁盘标准层) 和 `managed-premium` (azure 磁盘高级层) 。 默认存储类 `default` (Azure 磁盘标准层) 。 在您的决策中应考虑的类型和层之间存在重大的 **[定价差异](https://azure.microsoft.com/en-us/pricing/details/storage/)** 。 对于具有高性能要求的生产工作负荷，我们建议 `managed-premium` 对所有存储类使用。 对于开发/测试工作负荷、概念证明等，其中的成本是一个考虑因素，就 `azurefile` 是成本最低的选项。 所有这四种选项都可用于需要远程共享存储的情况，因为它们是 Azure 中所有网络连接的存储设备。 阅读有关 [AKS 存储](../../aks/concepts-storage.md)的详细信息。|
|**AWS 弹性 Kubernetes 服务 (EKS)**| Amazon 的弹性 Kubernetes 服务有一个基于 [EBS CSI 存储驱动程序](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)的主存储类。 建议用于生产工作负荷。 有一个新的存储驱动程序- [EFS CSI 存储驱动程序](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) -可以添加到 EKS 群集，但它目前处于 beta 阶段，可能会有所更改。 尽管 AWS 指出此存储驱动程序支持用于生产，但我们不建议使用它，因为它仍处于测试阶段并且可能会有所更改。 EBS 存储类为默认值，并调用 `gp2` 。 阅读有关 [EKS 存储](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)的详细信息。|
|**Google Kubernetes 引擎 (GKE)**|Google Kubernetes 引擎 (GKE) 只包含一个 `standard` 用于 [GCE 持久性磁盘](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)的存储类。 这也是默认值。 虽然可与直接连接 Ssd 一起使用的 GKE 有一个 [本地静态卷配置程序](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) ，但不建议使用它，因为它不是由 Google 维护或支持的。 阅读有关 [GKE 存储](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)的详细信息。

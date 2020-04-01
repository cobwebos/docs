---
title: 规划 Azure Service Fabric 群集部署
description: 了解如何规划和准备 Azure 中的生产 Service Fabric 群集部署。
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: ad6a7a6ea9a90bea4a3b6bc553da67a46144dc03
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422284"
---
# <a name="plan-and-prepare-for-a-cluster-deployment"></a>规划和准备群集部署

生产群集部署的规划和准备非常重要。  需要考虑到许多因素。  本文将引导你完成准备群集部署的步骤。

## <a name="read-the-best-practices-information"></a>阅读最佳做法信息
若要成功管理 Azure Service Fabric 应用程序和群集，我们强烈建议你执行某些操作，以优化生产环境的可靠性。  有关详细信息，请参阅 [Azure Service Fabric 应用程序和群集最佳做法](service-fabric-best-practices-overview.md)。

## <a name="select-the-os-for-the-cluster"></a>选择群集的 OS
使用 Service Fabric 可在运行 Windows Server 或 Linux 的任何 VM 或计算机上创建 Service Fabric 群集。  在部署群集之前，必须选择操作系统：Windows 或 Linux。  群集中的每个节点（虚拟机）运行相同的 OS，不能在同一群集中混用 Windows 和 Linux VM。

## <a name="capacity-planning"></a>容量计划
对于任何生产部署，容量规划都是一个重要的步骤。 下面是在规划过程中必须注意的一些事项。

* 群集的初始节点类型数目 
* 每个节点类型的属性（大小、实例数目、是否为主节点、是否面向 Internet、VM 数目，等等）
* 群集的可靠性和耐久性特征

### <a name="select-the-initial-number-of-node-types"></a>选择初始节点类型数目
首先，需要确定要创建的群集用于什么目的， 以及打算要将哪些类型的应用程序部署到此群集中。 应用程序是否有多个服务，其中是否有任何服务需是面向公众或面向 Internet 的服务？ （构成应用程序）的服务是否有不同的基础结构要求，例如，更多的 RAM 或更高的 CPU 周期？ Service Fabric 群集可以包括多个节点类型：主节点类型，以及一个或多个非主节点类型。 每个节点类型将映射到虚拟机规模集。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 可以设置[节点属性和放置约束][placementconstraints]，以将特定服务限制为特定节点类型。  有关详细信息，请参阅[群集一开始需要的节点类型数目](service-fabric-cluster-capacity.md#the-number-of-node-types-your-cluster-needs-to-start-out-with)。

### <a name="select-node-properties-for-each-node-type"></a>选择每个节点类型的节点属性
节点类型定义关联规模集中 VM 的 VM SKU、数目和属性。

每个节点类型的 VM 大小下限取决于为节点类型选择的[持久性层][durability]。

主节点类型的最小 VM 数由您选择的[可靠性层][reliability]决定。

请参阅[主节点类型](service-fabric-cluster-capacity.md#primary-node-type---capacity-guidance)、[非主节点类型上的有状态工作负荷](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateful-workloads)和[非主节点类型上的无状态工作负荷](service-fabric-cluster-capacity.md#non-primary-node-type---capacity-guidance-for-stateless-workloads)的最低建议要求。

如果节点数目超过最小数目，应根据想要在此节点类型中运行的应用程序/服务的副本数目确定数目。  [Service Fabric 应用程序的容量规划](service-fabric-capacity-planning.md)可帮助你估算运行应用程序所需的资源。 以后始终可以纵向扩展或缩减群集，以根据不断变化的应用程序工作负荷做出调整。 

#### <a name="use-ephemeral-os-disks-for-virtual-machine-scale-sets"></a>对虚拟机规模集使用临时 OS 磁盘

*临时 OS 磁盘*是在本地虚拟机 （VM） 上创建的存储，而不是保存到远程 Azure 存储。 建议所有 Service Fabric 节点类型（主节点和辅助节点）使用它们，因为与传统持久性 OS 磁盘相比，临时 OS 磁盘：

* 减少操作系统磁盘的读取/写入延迟
* 实现更快的重置/重新映像节点管理操作
* 降低总体成本（磁盘是免费的，不会产生额外的存储成本）

临时 OS 磁盘不是特定的 Service Fabric 功能，而是映射到 Service Fabric 节点类型的 Azure*虚拟机缩放集*的一项功能。 将它们与 Service Fabric 一起使用需要在群集 Azure 资源管理器模板中执行以下操作：

1. 确保节点类型为临时 OS 磁盘指定[受支持的 Azure VM 大小](../virtual-machines/windows/ephemeral-os-disks.md)，并确保 VM 大小具有足够的缓存大小以支持其 OS 磁盘大小（请参阅下面的*注释*）。例如：

    ```xml
    "vmNodeType1Size": {
        "type": "string",
        "defaultValue": "Standard_DS3_v2"
    ```

    > [!NOTE]
    > 请务必选择缓存大小等于或大于 VM 本身的 OS 磁盘大小的 VM 大小，否则 Azure 部署可能会导致错误（即使最初接受）。

2. 指定虚拟机缩放集版本 （`vmssApiVersion`）`2018-06-01`或更高版本：

    ```xml
    "variables": {
        "vmssApiVersion": "2018-06-01",
    ```

3. 在部署模板的虚拟机缩放集部分中，指定`Local`选项： `diffDiskSettings`

    ```xml
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
        "virtualMachineProfile": {
            "storageProfile": {
                "osDisk": {
                        "caching": "ReadOnly",
                        "createOption": "FromImage",
                        "diffDiskSettings": {
                            "option": "Local"
                        },
                }
            }
        }
    ```

> [!NOTE]
> 用户应用程序不应在操作系统磁盘上具有任何依赖项/文件/项目，因为操作系统磁盘将在操作系统升级时丢失。
> 因此，不建议将[修补程序业务流程应用程序](https://github.com/microsoft/Service-Fabric-POA)与临时磁盘一起使用。
>

> [!NOTE]
> 现有非临时 VMSS 无法就地升级以使用临时磁盘。
> 要迁移，用户必须[添加](./virtual-machine-scale-set-scale-node-type-scale-out.md)具有临时磁盘的新节点类型，将工作负载移动到新节点类型&[删除](./service-fabric-how-to-remove-node-type.md)现有节点类型。
>

有关详细信息和进一步配置选项，请参阅[Azure VM 的临时操作系统磁盘](../virtual-machines/windows/ephemeral-os-disks.md) 


### <a name="select-the-durability-and-reliability-levels-for-the-cluster"></a>选择群集的持续性和可靠性级别
耐久性层用于向系统指示 VM 对于基本 Azure 基础结构拥有的权限。 在主节点类型中，此权限可让 Service Fabric 暂停影响系统服务及有状态服务的仲裁要求的任何 VM 级别基础结构请求（例如，VM 重新启动、VM 重置映像或 VM 迁移）。 在非主节点类型中，此特权可让 Service Fabric 暂停影响其中运行的有状态服务的仲裁要求的任何 VM 级别基础结构请求，例如，VM 重新启动、VM 重置映像、VM 迁移，等等。  有关不同级别的优势、要使用哪种级别以及何时使用的建议，请参阅[群集的持久性特征][durability]。

可靠性层用于设置要在此群集中的主节点类型上运行的系统服务副本数。 副本数越大，群集中的系统服务越可靠。  有关不同级别的优势、要使用哪种级别以及何时使用的建议，请参阅[群集的可靠性特征][reliability]。 

## <a name="enable-reverse-proxy-andor-dns"></a>启用反向代理和/或 DNS
在群集内相互连接的服务通常可以直接访问其他服务的终结点，因为群集中的节点处于相同的本地网络上。 为了更轻松地在服务之间连接，Service Fabric 提供了其他服务[：DNS 服务和](service-fabric-dnsservice.md)[反向代理服务](service-fabric-reverseproxy.md)。  部署群集时，可以启用这两个服务。

由于许多服务（特别是容器化服务）可以拥有一个现有的 URL 名称，能够使用标准 DNS 协议（而不是命名服务协议）解析这些名称会十分方便，尤其是在应用程序“直接迁移”方案中。 这正是 DNS 服务能够发挥作用的地方。 借助 DNS 服务，用户能够将 DNS 名称映射到服务名称，进而解析终结点 IP 地址。

反向代理处理群集中的服务，群集公开 HTTP 终结点（包括 HTTPS）。 反向代理提供特定的 URI 格式，可以极大地简化对其他服务的调用。  反向代理还可以处理服务间相互通信所需的解析、连接和重试步骤。

## <a name="prepare-for-disaster-recovery"></a>为灾难恢复做准备
提供高可用性的关键一环是确保服务能够经受各种不同类型的故障。 对于计划外和不受控制的故障，这一点尤其重要。 [准备灾难恢复](service-fabric-disaster-recovery.md)介绍了一些常见的故障模式，如果未正确建模和管理，这些故障可能成为灾难。 此文还介绍发生灾难时应采取的缓解措施和行动。

## <a name="production-readiness-checklist"></a>生产就绪情况核对清单
应用程序和群集是否准备好接收生产流量？ 在将群集部署到生产环境之前，请运行整个[生产就绪性核对清单](service-fabric-production-readiness-checklist.md)。 通过检查此核对清单中的各个项，使应用程序和群集保持平稳运行。 我们强烈建议在转移到生产环境之前检查所有这些项。

## <a name="next-steps"></a>后续步骤
* [创建运行 Windows 的 Service Fabric 群集](service-fabric-best-practices-overview.md)
* [创建运行 Linux 的 Service Fabric 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

[placementconstraints]: service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints
[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[reliability]: service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster
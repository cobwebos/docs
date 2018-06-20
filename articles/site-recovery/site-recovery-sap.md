---
title: 使用 Azure Site Recovery 保护多层 SAP NetWeaver 应用程序部署 | Microsoft Docs
description: 本文介绍如何使用 Azure Site Recovery 保护 SAP NetWeaver 应用程序部署。
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2018
ms.author: asgang
ms.openlocfilehash: 27dfdec4e833a2f30963157ba2f4d95232e21270
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267326"
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>使用 Site Recovery 保护多层 SAP NetWeaver 应用程序部署

大多数大型和中型 SAP 部署具有某种形式的灾难恢复解决方案。 随着更多核心业务流程迁移到 SAP 等应用程序中，可靠和可测试的灾难恢复解决方案越来越重要。 Azure Site Recovery 已经过测试并与 SAP 应用程序集成。 Site Recovery 超过大多数本地灾难恢复解决方案的功能，并且总拥有成本 (TCO) 要低于竞争对手解决方案。

使用 Site Recovery 可以：
* 将组件复制到 Azure，以便**保护在本地运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序**。
* 将组件复制到其他 Azure 数据中心，以便**保护在 Azure 中运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序**。
* 使用 Site Recovery 将 SAP 部署迁移到 Azure，从而**简化云迁移**。
* 通过按需创建生产克隆来测试 SAP 应用程序，**简化 SAP 项目的升级、测试和原型制作**。

本文介绍如何使用 [Azure Site Recovery](site-recovery-overview.md) 保护 SAP NetWeaver 应用程序部署。 本文讨论如何按照一些最佳做法，使用 Site Recovery 将 Azure 上的三层 SAP NetWeaver 部署复制到其他 Azure 数据中心，从而对其进行保护。 本文介绍了支持的方案和配置，以及如何执行测试故障转移（灾难恢复演练）和实际故障转移。

## <a name="prerequisites"></a>先决条件
开始之前，请确保自己知道如何执行以下任务：

* [将虚拟机复制到 Azure](azure-to-azure-walkthrough-enable-replication.md)
* [设计恢复网络](site-recovery-azure-to-azure-networking-guidance.md)
* [执行到 Azure 的测试故障转移](azure-to-azure-walkthrough-test-failover.md)
* [执行到 Azure 的故障转移](site-recovery-failover.md)
* [复制域控制器](site-recovery-active-directory.md)
* [复制 SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支持的方案
使用 Azure Site Recovery 可以为以下方案实现灾难恢复解决方案：
* Azure 数据中心内运行的 SAP 系统复制到其他 Azure 数据中心（Azure 到 Azure 灾难恢复）。 有关详细信息，请参阅 [Azure 到 Azure 复制体系结构](https://aka.ms/asr-a2a-architecture)。
* 本地 VMware（或物理）服务器上运行的 SAP 系统复制到 Azure 数据中心内的灾难恢复站点（VMware 到 Azure 灾难恢复）。 此方案需要一些附加的组件。 有关详细信息，请参阅 [VMware 到 Azure 复制体系结构](https://aka.ms/asr-v2a-architecture)。
* 在本地 Hyper-V 上运行的 SAP 系统复制到 Azure 数据中心内的灾难恢复站点（Hyper-V 到 Azure 灾难恢复）。 此方案需要一些附加的组件。 有关详细信息，请参阅 [Hyper-V 到 Azure 复制体系结构](https://aka.ms/asr-h2a-architecture)。

本文中使用 **Azure 到 Azure** 灾难恢复方案演示 Site Recovery 的 SAP 灾难恢复功能。 由于 Site Recovery 复制过程不特定于应用程序，因此介绍的过程应该也适用于其他方案。

### <a name="required-foundation-services"></a>所需基础服务
在本文所述的方案中，已部署以下基础服务：
* Azure ExpressRoute 或 Azure VPN 网关
* 至少有一个在 Azure 中运行的 Active Directory 域控制器和 DNS 服务器

建议在部署 Site Recovery 之前建立此基础结构。

## <a name="reference-sap-application-deployment"></a>参考 SAP 应用程序部署

此参考体系结构说明如何在具有高可用性的 Azure 上的 Windows 环境中运行 SAP NetWeaver。  此体系结构是使用可根据组织需求更改的特定虚拟机 (VM) 大小来部署的。

![典型 SAP 部署模式的示意图](./media/site-recovery-sap/reference_sap.png)

## <a name="disaster-recovery-considerations"></a>灾难恢复注意事项

对于灾难恢复 (DR)，必须能够故障转移到次要区域。 每个层使用不同的策略提供灾难恢复 (DR) 保护。

#### <a name="vms-running-sap-web-dispatcher-pool"></a>运行 SAP Web 调度程序池的 VM 
Web 调度程序组件用作 SAP 应用程序服务器之间的 SAP 流量的负载均衡器。 为了实现 Web 调度程序组件的高可用性，在轮循机制配置中使用了 Azure 负载均衡器来实施并行 Web 调度程序设置，以便在均衡器池中的可用 Web 调度程序之间分配 HTTP(S) 流量。 这将会使用 Azure Site Recovery(ASR) 进行复制，并且自动化脚本将用于在灾难恢复区域上配置负载均衡器。 

####<a name="vms-running-application-servers-pool"></a>运行应用程序服务器池的 VM
若要管理 ABAP 应用程序服务器的登录组，需使用 SMLG 事务。 该事务使用 Central Services 的消息服务器中的负载均衡功能，在 SAPGUI 的 SAP 应用程序服务器池之间分配工作负荷，以及分配 RFC 流量。 这将会使用 Azure Site Recovery 进行复制 

####<a name="vms-running-sap-central-services-cluster"></a>运行 SAP Central Services 群集的 VM
此参考体系结构在应用层中的 VM 上运行 Central Services。 如果将 Central Services 部署到单个 VM（高可用性不是一项要求时，通常采用这种部署方式），则它可能会成为潜在的单一故障点 (SPOF)。<br>

若要实现高可用性解决方案，可以使用共享磁盘群集或文件共享群集。若要为共享磁盘群集配置 VM，请使用 Windows Server 故障转移群集。 建议将云见证用作仲裁见证。 
 > [!NOTE]
 > Azure Site Recovery 不会复制云见证，因此建议将云见证部署在灾难恢复区域中。

为了支持故障转移群集环境，[SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) 会通过复制群集节点拥有的独立磁盘来执行群集共享卷功能。 Azure 原生并不支持共享磁盘，因此需要 SIOS 提供的解决方案。 

处理群集的另一种方法是实现文件共享群集。 [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) 最近修改了 Central Services 部署模式，以允许通过 UNC 路径访问 /sapmnt 全局目录。 此项更改消除了 Central Services VM 上对 SIOS 或其他共享磁盘解决方案的需求。 我们仍然建议确保 /sapmnt UNC 共享具有高可用性。 这可在 Central Services 实例上实现：将 Windows Server 故障转移群集与 Windows Server 2016 中的横向扩展文件服务器 (SOFS) 和存储空间直通 (S2D) 功能配合使用。 
 > [!NOTE]
 > 当前，Azure Site Recovery 仅支持使用存储空间直通进行虚拟机的崩溃一致点复制 


## <a name="disaster-recovery-considerations"></a>灾难恢复注意事项

可以使用 Azure Site Recovery 跨 Azure 区域安排整个 SAP 部署的故障转移。
下面是设置灾难恢复的步骤 

1. 复制虚拟机 
2. 设计恢复网络
3.  复制域控制器
4.  复制数据库层 
5.  执行测试故障转移 
6.  执行故障转移 

下面是此示例中使用的每个层的灾难恢复建议。 

 **SAP 层** | **建议**
 --- | ---
**SAP Web 调度程序池** |  使用 Site Recovery 进行复制 
**SAP Application 服务器池** |  使用 Site Recovery 进行复制 
**SAP Central Services 群集** |  使用 Site Recovery 进行复制 
**Active Directory 虚拟机** |  Active Directory 复制 
**SQL 数据库服务器** |  SQL Always On 复制

##<a name="replicate-virtual-machines"></a>复制虚拟机

若要开始将所有 SAP 应用程序虚拟机复制到 Azure 灾难恢复数据中心，请遵照[将虚拟机复制到 Azure](azure-to-azure-walkthrough-enable-replication.md) 中的指导。


* 有关保护 Active Directory 和 DNS 的指导，请参阅[保护 Active Directory 和 DNS](site-recovery-active-directory.md) 文档。

* 有关保护 SQL 服务器上运行的数据库层的指导，请参阅[保护 SQL Server](site-recovery-active-directory.md) 文档。

## <a name="networking-configuration"></a>网络配置

如果使用的是静态 IP，可以指定希望虚拟机采用的 IP 地址。 若要设置 IP 地址，请转到“计算和网络设置” > “网络接口卡”。

![演示如何在 Site Recovery 的“网络接口卡”窗格中设置专用 IP 地址的屏幕截图](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>创建恢复计划
恢复计划支持在故障转移期间将多层应用程序中的各个层排序。 排序有助于保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请完成[使用 Site Recovery 创建恢复计划](site-recovery-create-recovery-plans.md)中所述的步骤。

### <a name="adding-virtual-machines-to-failover-groups"></a>将虚拟机添加到故障转移组

1.  通过添加应用程序服务器、Web 调度程序和 SAP Central services VM 创建恢复计划。
2.  单击“自定义”将 VM 分组。 默认情况下，所有 VM 属于“组 1”。



### <a name="add-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划
在故障转移后或测试故障转移期间，可能需要在 Azure 虚拟机上执行一些操作才能让应用程序正常工作。 可将某些故障转移后的操作自动化。 例如，可在恢复计划中添加相应的脚本，来更新 DNS 条目，以及更改绑定和连接。


可以单击下面的“部署到 Azure”按钮，将最常用的 Azure Site Recovery 脚本部署到自动化帐户。 使用任何已发布的脚本时，请确保遵循脚本中的指导。

[![部署到 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 将操作前脚本添加到“组 1”，以故障转移 SQL 可用性组。 使用示例脚本中发布的“ASR-SQL-FailoverAG”脚本。 确保遵循脚本中的指导，并相应地在脚本中进行所需的更改。
2. 添加一个操作后脚本，以便在 Web 层的已故障转移虚拟机（组 1）上附加负载均衡器。 使用示例脚本中发布的“ASR-AddSingleLoadBalancer”脚本。 确保遵循脚本中的指导，并相应地在脚本中进行所需的更改。

![SAP 恢复计划](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>运行测试故障转移

1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择针对 SAP 应用程序创建的恢复计划。
3.  选择“测试故障转移”。
4.  若要启动测试故障转移过程，请选择恢复点和 Azure 虚拟网络。
5.  当辅助环境启动时，执行验证。
6.  完成验证后，选择“清理测试故障转移”清理测试故障转移环境。

有关详细信息，请参阅[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>运行故障转移

1.  在 Azure 门户中，选择恢复服务保管库。
2.  选择针对 SAP 应用程序创建的恢复计划。
3.  选择“故障转移”。
4.  若要启动故障转移过程，请选择恢复点。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。

## <a name="next-steps"></a>后续步骤
* 若要详细了解如何使用 Site Recovery 为 SAP NetWeaver 部署构建灾难恢复解决方案，请参阅可下载的白皮书 [SAP NetWeaver：使用 Azure Site Recovery 构建灾难恢复解决方案](http://aka.ms/asr-sap)。 本白皮书介绍了针对各种 SAP 体系结构的建议，列出了 Azure 上的 SAP 所支持的应用程序和 VM 类型，并介绍了用于灾难恢复解决方案的测试计划选项。
* 详细了解如何使用 Site Recovery [复制其他工作负荷](site-recovery-workload.md)。

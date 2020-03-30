---
title: 使用 Azure 站点恢复设置 SAP NetWeaver 灾难恢复
description: 了解如何使用 Azure 站点恢复为 SAP NetWeaver 设置灾难恢复。
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190797"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>为多层 SAP NetWeaver 应用部署设置灾难恢复

大多数大型和中型 SAP 部署具有某种形式的灾难恢复解决方案。 随着更多核心业务流程迁移到 SAP 等应用程序中，可靠和可测试的灾难恢复解决方案越来越重要。 Azure Site Recovery 已经过测试并与 SAP 应用程序集成。 站点恢复超出了大多数本地灾难恢复解决方案的功能，总拥有成本低于竞争解决方案。

使用 Site Recovery 可以：
* 将组件复制到 Azure，以便保护在本地运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
* 将组件复制到其他 Azure 数据中心，以便保护在 Azure 中运行的 SAP NetWeaver 和非 NetWeaver 生产应用程序。
* 使用 Site Recovery 将 SAP 部署迁移到 Azure，从而简化云迁移。
* 通过按需创建生产克隆来测试 SAP 应用程序，简化 SAP 项目的升级、测试和原型制作。

您可以使用[Azure 站点恢复](site-recovery-overview.md)来保护 SAP NetWeaver 应用程序部署。 本文介绍使用站点恢复复制到另一个 Azure 数据中心时，在 Azure 上保护三层 SAP NetWeaver 部署的最佳做法。 本文介绍了受支持的方案和配置，以及如何执行故障转移（灾难恢复演练）和实际故障转移。

## <a name="prerequisites"></a>先决条件

开始之前，请确保自己知道如何执行以下任务：

* [将虚拟机复制到 Azure](azure-to-azure-walkthrough-enable-replication.md)
* [设计恢复网络](site-recovery-azure-to-azure-networking-guidance.md)
* [执行到 Azure 的测试故障转移](azure-to-azure-walkthrough-test-failover.md)
* [执行到 Azure 的故障转移](site-recovery-failover.md)
* [复制域控制器](site-recovery-active-directory.md)
* [复制 SQL Server 实例](site-recovery-sql.md)

## <a name="supported-scenarios"></a>支持的方案

使用 Azure Site Recovery 可以为以下方案实现灾难恢复解决方案：
* SAP 系统在一个 Azure 数据中心中运行，并且要将它们复制到另一个 Azure 数据中心（Azure 到 Azure 灾难恢复）。 
   有关详细信息，请参阅 [Azure 到 Azure 复制体系结构](https://aka.ms/asr-a2a-architecture)。
* 本地的 VMware（或物理）服务器上运行 SAP 系统。 此外，您还将 SAP 系统复制到 Azure 数据中心中的灾难恢复站点（VMware 到 Azure 灾难恢复）。 
   此方案需要一些附加的组件。 有关详细信息，请参阅 [VMware 到 Azure 复制体系结构](https://aka.ms/asr-v2a-architecture)。
* 在超 V 本地运行 SAP 系统。 此外，您还将 SAP 系统复制到 Azure 数据中心中的灾难恢复站点（Hyper-V 到 Azure 灾难恢复）。
   此方案需要一些附加的组件。 有关详细信息，请参阅 [Hyper-V 到 Azure 复制体系结构](https://aka.ms/asr-h2a-architecture)。

在本文中，我们使用**Azure 到 Azure**灾难恢复方案。 该方案显示站点恢复的 SAP 灾难恢复功能。 由于 Site Recovery 复制过程不特定于应用程序，因此介绍的过程应该也适用于其他方案。

### <a name="required-foundation-services"></a>所需基础服务
在本文所述的方案中，已部署以下基础服务：
* Azure ExpressRoute 或 Azure VPN 网关
* 在 Azure 中运行的至少一个 Azure 活动目录域控制器和 DNS 服务器

建议在部署 Site Recovery 之前建立此基础结构。

## <a name="reference-sap-application-deployment"></a>参考 SAP 应用程序部署

此参考体系结构在 Azure 上的 Windows 环境中运行 SAP NetWeaver，并且具有高可用性。 此体系结构部署时，具有特定的虚拟机 （VM） 大小，您可以更改这些大小以满足组织的需求。

![典型 SAP 部署模式的示意图](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>灾难恢复注意事项

对于灾难恢复，您必须能够故障转移到辅助区域。 每个层使用不同的策略来提供灾难恢复保护。

#### <a name="vms-running-sap-web-dispatcher-pools"></a>运行 SAP Web 调度程序池的 VM

Web 调度程序组件充当 SAP 应用程序服务器之间 SAP 流量的负载均衡器。 为了实现 Web 调度程序组件的高可用性，Azure 负载均衡器实现了并行 Web 调度程序设置。 Web 调度程序使用循环配置在平衡器池中的可用 Web 调度器之间分配 HTTP（S） 流量。

#### <a name="vms-running-application-servers-pools"></a>运行应用程序服务器池的 VM
SMLG 事务管理 ABAP 应用程序服务器的登录组。 它使用中央服务的消息服务器中的负载平衡功能在 SAP 应用程序服务器池之间分配工作负载，用于 SAPGUI 和 RFC 流量。 您可以使用站点恢复来复制此管理。

#### <a name="vms-running-sap-central-services-clusters"></a>运行 SAP 中央服务群集的 VM
此参考体系结构在应用层中的 VM 上运行 Central Services。 在单个 VM 中，中央服务是潜在的单点故障。 典型的部署和高可用性不是要求。

要实现高可用性解决方案，可以使用共享磁盘群集或文件共享群集。 若要为共享磁盘群集配置 VM，请使用 Windows Server 故障转移群集。 我们建议您使用云见证作为仲裁见证。

 > [!NOTE]
 > 由于站点恢复不会复制云见证，因此我们建议您在灾难恢复区域部署云见证。

为了支持故障转移群集环境[，SIOS 数据保持群集版本](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8)执行群集共享卷功能。 在函数中，SIOS 数据保持群集复制群集节点拥有的独立磁盘。 由于 Azure 不支持本机共享磁盘，因此它需要 SIOS 提供的解决方案。

还可以通过实现文件共享群集来处理群集。 [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) 最近修改了 Central Services 部署模式，以允许通过 UNC 路径访问 /sapmnt 全局目录。 我们仍然建议您确保 /sapmnt UNC 共享高度可用。 您可以检查中央服务实例。 使用 Windows 服务器故障转移群集与横向扩展文件服务器 （SOFS） 和 Windows Server 2016 中的存储空间直接 （S2D） 功能。

 > [!NOTE]
 > 站点恢复目前仅支持直接使用存储空间的虚拟机和 SIOS 数据保管人的无源节点的崩溃一致性点复制。


## <a name="more-disaster-recovery-considerations"></a>更多灾难恢复注意事项

可以使用站点恢复来协调跨 Azure 区域的完整 SAP 部署的故障转移。
以下是设置灾难恢复的步骤：

1. 复制虚拟机
1. 设计恢复网络
1. 复制域控制器
1. 复制数据库层
1. 执行测试故障转移
1. 执行故障转移

以下是本示例中使用的每一层的灾难恢复建议。

 **SAP 层** | **建议**
 --- | ---
**SAP Web 调度程序池** |  使用站点恢复进行复制 
**SAP Application 服务器池** |  使用站点恢复进行复制 
**SAP Central Services 群集** |  使用站点恢复进行复制 
**Active Directory 虚拟机** |  使用活动目录复制 
**SQL 数据库服务器** |  始终在复制时使用 SQL 服务器

## <a name="replicate-virtual-machines"></a>复制虚拟机

若要开始将所有 SAP 应用程序虚拟机复制到 Azure 灾难恢复数据中心，请遵照[将虚拟机复制到 Azure](azure-to-azure-walkthrough-enable-replication.md) 中的指导。

* 有关保护活动目录和 DNS 的指导，[请参阅如何保护活动目录和 DNS](site-recovery-active-directory.md)。

* 有关保护在 SQL Server 上运行的数据库层的指导，了解如何[保护 SQL Server](site-recovery-sql.md)。

## <a name="networking-configuration"></a>网络配置

如果使用的是静态 IP，可以指定希望虚拟机采用的 IP 地址。 要设置 IP 地址，请访问**计算和网络设置** > **网络接口卡**。

![演示如何在 Site Recovery 的“网络接口卡”窗格中设置专用 IP 地址的屏幕截图](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>创建恢复计划

恢复计划支持在故障转移期间将多层应用程序中的各个层排序。 排序有助于保持应用程序一致性。 为多层 Web 应用程序创建恢复计划时，请完成[使用 Site Recovery 创建恢复计划](site-recovery-create-recovery-plans.md)中所述的步骤。

### <a name="add-virtual-machines-to-failover-groups"></a>将虚拟机添加到故障转移组

1. 通过添加应用程序服务器、Web 调度程序和 SAP 中央服务 VM 来创建恢复计划。
1. 选择 **"自定义"** 以对 VM 进行分组。 默认情况下，所有 VM 都是组 1 的一部分。

### <a name="add-scripts-to-the-recovery-plan"></a>将脚本添加到恢复计划
要使应用程序正常运行，可能需要在 Azure 虚拟机上执行一些操作。 在故障转移后或测试故障转移期间执行这些操作。 您还可以自动执行一些故障转移后操作。 例如，更新 DNS 条目，并通过向恢复计划添加相应的脚本来更改绑定和连接。

您可以通过选择 **"部署到 Azure"** 将最常用的站点恢复脚本部署到 Azure 自动化帐户中。 使用任何已发布的脚本时，请按照脚本中的指南操作。

[![部署到 Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. 将操作前脚本添加到组 1 以故障转移 SQL Server 可用性组。 使用示例脚本中发布的 ASR-SQL-FailoverAG 脚本。 按照脚本中的指南操作，并在脚本中适当地进行所需的更改。
1. 添加操作后脚本，将负载均衡器附加到 Web 层（组 1）的失败虚拟机。 使用示例脚本中发布的 ASR-AddSingleLoad平衡器脚本。 按照脚本中的指南操作，并根据需要在脚本中进行必要的更改。

![SAP 恢复计划](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>运行测试故障转移

1. 在 Azure 门户中，选择恢复服务保管库。
1. 选择针对 SAP 应用程序创建的恢复计划。
1. 选择“测试故障转移”****。
1. 若要启动测试故障转移过程，请选择恢复点和 Azure 虚拟网络。
1. 当辅助环境启动时，执行验证。
1. 验证完成后，通过选择 **"清理测试故障转移"** 来清理故障转移环境。

有关详细信息，请参阅[在 Site Recovery 中执行到 Azure 的测试故障转移](site-recovery-test-failover-to-azure.md)。

## <a name="run-a-failover"></a>运行故障转移

1. 在 Azure 门户中，选择恢复服务保管库。
1. 选择针对 SAP 应用程序创建的恢复计划。
1. 选择“故障转移”。****
1. 若要启动故障转移过程，请选择恢复点。

有关详细信息，请参阅 [Site Recovery 中的故障转移](site-recovery-failover.md)。

## <a name="next-steps"></a>后续步骤
* 详细了解如何使用站点恢复为 SAP NetWeaver 部署构建灾难恢复解决方案。 请参阅可下载的白皮书[SAP NetWeaver：使用站点恢复构建灾难恢复解决方案](https://aka.ms/asr_sap)。 白皮书讨论了各种 SAP 体系结构的建议。 您可以在 Azure 上查看 SAP 受支持的应用程序和 VM 类型。 还有用于测试灾难恢复解决方案的计划选项。
* 详细了解如何使用 Site Recovery [复制其他工作负荷](site-recovery-workload.md)。

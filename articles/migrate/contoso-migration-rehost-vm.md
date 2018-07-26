---
title: 使用 Azure Site Recovery 通过迁移将 Contoso 应用重新托管到 Azure VM | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 服务通过直接迁移将本地应用重新托管到 Azure（实现本地计算机的迁移）。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: e152a95a392fd503e027591a4930fbeef744d6f4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39012275"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms"></a>Contoso 迁移：将本地应用重新托管到 Azure VM


本文演示了 Contoso 如何通过将应用 VM 迁移到 Azure VM 来在 Azure 中重新托管本地 SmartHotel 应用。


本文档是系列文章中的其中一篇，目的是展示虚拟公司 Contoso 如何将本地资源迁移到 Microsoft Azure 云。 该系列介绍了背景信息，同时提供了很多应用场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移以及如何运行不同类型的迁移。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。


**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有迁移文章共用同一个基础结构。 | 可用
[文章 3：评估要迁移到 Azure 的本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 Contoso 使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将本地 SmartHotel 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 SQL 托管实例。 | 可用
文章 5：在 Azure VM 上重新托管应用 | 演示 Contoso 如何使用 Site Recovery 服务将 SmartHotel 应用 VM 迁移到 Azure VM。 | 本文。
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组上重新托管应用](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 Contoso 使用 Site Recovery 来迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 可用
[文章 8：在 Azure VM 和 Azure MySQL 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，以及如何使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure Web 应用，并将应用数据库迁移到 Azure SQL Server 实例 | 可用
[文章 10：基于 Azure Web 应用和 Azure MySQL 重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | 演示 Contoso 如何将 Linux osTicket 应用迁移到多个站点中的 Azure Web 应用，并与 GitHub 集成以便持续交付。 他们将应用数据库迁移到 Azure MySQL 实例。 | 可用
[文章 11：基于 VSTS 重构 TFS](contoso-migration-tfs-vsts.md) | 展示 Contoso 如何通过将本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Visual Studio Team Services (VSTS) 来迁移该部署。 | 可用
[文章 12：基于 Azure 容器和 Azure SQL 数据库重构应用](contoso-migration-rearchitect-container-sql.md) | 展示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器，将应用数据库重新架构到 Azure SQL 数据库中。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | 展示 Contoso 如何使用一系列 Azure 功能和服务（包括应用服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成其 SmartHotel 应用。 | 可用



在本文中，Contoso 将迁移 VMware VM 上运行的双层 Windows. NET SmartHotel 应用迁移到 Azure。 此应用作为开源应用提供，可在 [github](https://github.com/Microsoft/SmartHotel360) 上下载。



## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **控制风险**：SmartHotel 应用对 Contoso 业务而言至关重要。 公司希望在不遭受任何风险的情况下将其迁移到 Azure。
- **扩展**：Contoso 不想修改应用， 只想确保应用性能稳定。


## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的目标。 这些目标用于确定最佳迁移方式：

- 迁移后，Azure 中应用的功能应当能够与其当前在 VMware 中的功能媲美。  无论在云端还是在本地，应用同样重要。 
- Contoso 不打算投资此应用。  该用对业务至关重要，但公司目前只希望将其安全地迁移到云端。
- Contoso 不想更改此应用的 OPS 模型， 而是希望按当前的方式在云端与应用交互。
- Contoso 不想更改应用的任何功能， 而只想更改它的位置。

## <a name="proposed-architecture"></a>建议的体系结构

当前环境如下

- 该应用跨两个 VM（WEBVM 和 SQLVM）进行分层。
- 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com（6.5 版）上。
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。

![方案体系结构](./media/contoso-migration-rehost-vm/architecture.png) 

## <a name="migration-process"></a>迁移过程

Contoso 计划使用 Site Recovery 将应用前端和数据库 VM 迁移到 Azure VM：

- 首先是为 Site Recovery 准备和设置 Azure 组件，并准备本地 VMware 基础结构。
- 公司已部署有 [Azure 基础结构](contoso-migration-infrastructure.md)，因此只需专门为 Site Recovery 添加几个 Azure 组件即可。
- 待一切准备就绪后，即可开始复制 VM。
在复制操作启用并运行之后，通过将 VM 故障转移到 Azure 来实现迁移。

![迁移过程](./media/contoso-migration-rehost-vm/migraton-process.png) 



### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 该服务协调和管理 Azure VM、本地 VM 与物理服务器的迁移和灾难恢复。  | 在复制到 Azure 的过程中，会产生 Azure 存储费用。  故障转移时会创建 Azure VM，因此会产生费用。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/)费用和定价。


## <a name="prerequisites"></a>先决条件

要运行此方案，你（和 Contoso）需具备以下项。

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在学习本系列前几篇文章期间，应已创建一个订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如需更加细化的权限，请查看[此文](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Azure 基础结构** | [了解](contoso-migration-infrastructure.md) Contoso 如何设置 Azure 基础结构。<br/><br/> 详细了解有关 Site Recovery 的具体[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)要求。
**本地服务器** | 本地 vCenter Server 应运行版本 5.5、6.0 或 6.5<br/><br/> ESXi 主机应运行版本 5.5、6.0 或 6.5<br/><br/> ESXi 主机上应运行有一个或多个 VMware VM。
**本地 VM** | VM 必须符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。


## <a name="scenario-steps"></a>方案步骤

Contoso 按如下方式运行迁移：

> [!div class="checklist"]
> * **步骤 1：为 Site Recovery 准备 Azure**：创建恢复服务保管库以及用于保存复制数据的 Azure 存储帐户。
> * **步骤 2：为 Site Recovery 准备本地 VMware**：准备用于发现 VM 和安装代理的帐户，并准备好在故障转移后连接到 Azure VM。
> * **步骤 3：复制 VM**：设置复制并开始将 VM 复制到 Azure 存储。
> * **步骤 4：使用 Site Recovery 迁移 VM**：运行测试故障转移，确保一切正常工作，然后运行完整故障转移将 VM 迁移到 Azure。




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>步骤 1：为 Site Recovery 服务准备 Azure

Contoso 需具备以下 Azure 组件才能将 VM 迁移到 Azure：

- Azure VM 在故障转移期间创建时所位于的 VNet。
- 一个 Azure 存储帐户，用于保留复制的数据。 
- Azure 中的恢复服务保管库。

上述项配置如下：

1. Contoso 已设置可在[部署 Azure 基础结构](contoso-migration-infrastructure.md)时对 Site Recovery 使用的网络

    - SmartHotel 应用是一个生产应用，而 VM 将迁移到美国东部 2 主区的 Azure 生产网络 (VNET-PROD-EUS2) 中。
    - 这两个 VM 都将置于供生产资源使用的 ContosoRG 资源组中。
    - 应用前端 VM (WEBVM) 将迁移到生产网络中的前端子网 (PROD-FE-EUS2)。
    - 应用数据库 VM (SQLVM) 将迁移到生产网络中的数据库子网 (PROD-DB-EUS2)。

2. Contoso 在主要区域中创建了一个 Azure 存储帐户 (contosovmsacc20180528)。
    - 存储帐户必须位于与恢复服务保管库相同的区域。
    - 公司使用具有标准存储空间的常规用途帐户，并使用 LRS 复制。 

    ![Site Recovery 存储](./media/contoso-migration-rehost-vm/asr-storage.png)

3. 网络和存储帐户就绪后，Contoso 现可创建一个恢复服务保管库 (ContosoMigrationVault)，并将其置于美国东部 2 主区的 ContosoFailoverRG 资源组中。

    ![恢复服务保管库](./media/contoso-migration-rehost-vm/asr-vault.png)

**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)如何为 Site Recovery 设置 Azure。


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>步骤 2：为 Site Recovery 准备本地 VMware

Contoso 在本地准备以下项目：

- vCenter 服务器或 vSphere ESXi 主机上的一个帐户，用于自动发现 VM。
- 一个可用于在 VMware VM 上自动安装移动服务的帐户。 
- 本地 VM 设置，以便 Contoso 能够在故障转移后连接到 Azure VM 副本。


### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 
- 安排 VM 的复制、故障转移和故障回复操作。
- 至少需要一个只读帐户。 此帐户应当能够运行诸如创建和删除磁盘以及打开 VM 等操作。

Contoso 如下设置帐户：

1. 在 vCenter 级别创建一个角色。
2. 然后向该角色分配所需权限。



### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

每个 VM 上都必须安装有移动服务。

- 启用 VM 复制后，Site Recovery 可自动推送安装移动服务。
- 需具备一个帐户，然后 Site Recovery 才能访问 VM 实现推送安装。 可在设置复制时指定此帐户。
- 帐户可为域，也可为本地帐户，但必须具有在 VM 上进行安装的权限。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

故障转移后，Contoso 想要连接到 Azure VM。 为此，公司在迁移之前执行以下步骤：

1. 为了通过 Internet 进行访问：

 - 故障转移前在本地 VM 上启用 RDP
 - 确保为“公共”配置文件添加了 TCP 和 UDP 规则。
 - 检查“Windows 防火墙” > “允许的应用”中是否同意所有配置文件使用 RDP。
 
2. 为了通过站点到站点 VPN 进行访问：

 - 在本地计算机上启用 RDP。
 - 在“Windows 防火墙” -> “允许的应用和功能”中同意域和专用网络使用 RDP。
 - 将本地 VM 上操作系统的 SAN 策略设置为“OnlineAll”。

此外，在运行故障转移时，公司需要检查以下项目：

- 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果存在，则在更新完成之前无法登录到 VM。
- 故障转移后，可选择“启动诊断”来查看 VM 的屏幕截图。 如果此操作无效，应验证确保 VM 正在运行，并查看[疑难解答提示](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


**需要更多帮助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)如何创建和分配用于自动发现的角色。
- [了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)如何创建用于推送安装移动服务安装的帐户。


## <a name="step-3-replicate-the-on-premises-vms"></a>步骤 3：复制本地 VM

Contoso 需要设置和启用复制，然后才可运行到 Azure 的迁移。

### <a name="set-a-replication-goal"></a>设置复制目标

1. 在保管库的保管库名称 (ContosoVMVault) 下，选择复制目标（“入门” > “Site Recovery” > “准备基础结构”）。
2. 指定计算机位于本地、在 VMware 上运行且计划复制到 Azure。

    ![复制目标](./media/contoso-migration-rehost-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>确认部署规划

通过选择“是，我已完成”确认已完成部署计划后，才能继续操作。 在此方案中，Contoso 仅计划迁移两个 VM，因此无需部署计划。


### <a name="set-up-the-source-environment"></a>设置源环境

Contoso 需要配置其源环境。 为此，下载 OVF 模板，然后使用它将 Site Recovery 配置服务器部署为可用性高的本地 VMware VM。 设置并运行配置服务器后，在保管库中进行注册。

配置服务器运行多个组件：

- 用于协调本地与 Azure 之间的通信并管理数据复制的配置服务器组件。
- 充当复制网关的进程服务器。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。
- 进程服务器还会将移动服务安装在要复制的 VM 上，并执行针对本地 VMware VM 的自动发现。



Contoso 执行以下步骤：

1. 在保管库中，通过“准备基础结构” > “源” > “配置服务器”下载 OVF 模板。
    
    ![下载 OVF](./media/contoso-migration-rehost-vm/add-cs.png)

2. 将模板导入 VMware 以创建和部署 VM。

    ![OVF 模板](./media/contoso-migration-rehost-vm/vcenter-wizard.png)

3.  首次打开 VM 时，系统将进入 Windows Server 2016 安装体验。 此时接受许可协议并输入管理员密码。
4. 安装完成后，以管理员身份登录到 VM。 首次登录时，系统默认运行 Azure Site Recovery 配置工具。
5. 在此工具中，指定用于在保管库中注册配置服务器的名称。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器要注册到的保管库。

    ![注册配置服务器](./media/contoso-migration-rehost-vm/config-server-register2.png)

7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机之后，配置服务器管理向导会自动启动。
9. 在向导中，选择要接收复制流量的 NIC。 此设置一经配置便无法更改。
10. 选择订阅、资源组以及配置服务器要注册到的保管库。
        ![保管库](./media/contoso-migration-rehost-vm/cswiz1.png) 

10. 下载并安装 MySQL 服务器和 VMWare PowerCLI。 
11. 验证后，指定 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 保留默认端口，并在 Azure 中指定服务器的友好名称。
12. 指定创建用于自动发现的帐户，同时指定用于自动安装移动服务的凭据。 对于 Windows 计算机，帐户需在 VM 上具备本地管理员特权。

    ![vCenter](./media/contoso-migration-rehost-vm/cswiz2.png)

7. 在 Azure 门户中注册完成后，Contoso 仔细检查保管库的“源”页面上是否列出配置服务器和 VMware 服务器。 发现可能需要 15 分钟或更长时间。 
8. 然后，Site Recovery 通过指定的设置连接到 VMware 服务器并发现 VM。

### <a name="set-up-the-target"></a>设置目标

现在，Contoso 指定目标复制设置。

1. 在“准备基础结构” > “目标”中，选择目标设置。
2. Site Recovery 检查指定的目标位置是否具有 Azure 存储帐户和网络。

### <a name="create-a-replication-policy"></a>创建复制策略

Contoso 现可创建复制策略。

1. 在“准备基础结构” > “复制设置” > “复制策略” >  “创建和关联”中，创建策略“ContosoMigrationPolicy”。
2. 使用默认设置：
    - **RPO 阈值**：默认为 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
    - **恢复点保留期**。 默认为 24 小时。 此值指定每个恢复点的保留时长。 可以将复制的虚拟机恢复到窗口中的任何点。
    - **应用一致性快照频率**。 默认为 1 小时。 此值指定应用程序一致性快照的创建频率。

        ![创建复制策略](./media/contoso-migration-rehost-vm/replication-policy.png)

5. 此策略自动与配置服务器关联。 

    ![关联复制策略](./media/contoso-migration-rehost-vm/replication-policy2.png)

### <a name="enable-replication-for-webvm"></a>为 WEBVM 启用复制

一切准备就绪，Contoso 现可为 VM 启用复制。 先从 WebVM 开始。

1. 在“复制应用程序” > “源” > “+复制”中，选择源设置。
2. 公司指出希望启用 VM 并选择 vCenter 服务器和配置服务器。

    ![启用复制](./media/contoso-migration-rehost-vm/enable-replication1.png)

3. 选择目标设置（例如资源组和 Azure 网络）和存储帐户。

     ![启用复制](./media/contoso-migration-rehost-vm/enable-replication2.png)

4. Contoso 选择 WebVM 进行复制、检查复制策略并启用复制操作。

    - 在本阶段，Contoso 仅选择 WEBVM，理由是 必须选中 VNet 和子网，且 Contoso 计划替换其他子网中的应用 VM。
    - 启用复制后，Site Recovery 会在 VM 上自动安装移动服务。

    ![启用复制](./media/contoso-migration-rehost-vm/enable-replication3.png)

5. 在“作业”中跟踪复制进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。
6. 在 Azure 门户的“基本信息”中，Contoso 可查看复制到 Azure 的 VM 的结构。


### <a name="enable-replication-for-sqlvm"></a>为 SQLVM 启用复制

Contoso 现可开始按上述相同的流程复制 SQLVM 计算机。

1. 选择源设置。

    ![启用复制](./media/contoso-migration-rehost-vm/enable-replication1.png)

2. 然后指定目标设置。

     ![启用复制](./media/contoso-migration-rehost-vm/enable-replication2-sqlvm.png)

3. 选择 SQLVM 进行复制。 

    ![启用复制](./media/contoso-migration-rehost-vm/enable-replication3-sqlvm.png)

4. 与 WEBVM 应用相同的复制策略，然后启用复制。

    ![基础结构视图](./media/contoso-migration-rehost-vm/essentials.png)

**需要更多帮助？**

- 有关上述所有步骤的完整演练，可参阅 [针对本地 VMware VM 设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)。
- 还可查看详细说明，了解如何[设置源环境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[配置复制设置](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。
- 有关详细详细，可参阅[启用复制](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)。


## <a name="step-4-migrate-the-vms"></a>步骤 4：迁移 VM 

Contoso 运行快速测试故障转移，然后运行完整故障转移来迁移 VM。

### <a name="run-a-test-failover"></a>运行测试故障转移

测试故障转移可帮助确保一切如预期正常工作。 

1. Contoso 运行测试故障转移，将内容移动到最近可用的时间点（“最新处理”）。
2. 选择“在开始故障转移前关闭计算机”，以便 Site Recovery 在触发故障转移之前尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 
3. 运行测试故障转移： 

    - 运行先决条件检查，确保满足迁移所需的所有条件。
    - 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
    - 使用上一步中处理的数据创建 Azure VM。
    
3. 故障转移完成后，Azure VM 副本将在 Azure 门户中显示。 Contoso 会检查 VM 是否大小适当、已连接到正确的网络且正在运行。 
4. 验证测试故障转移之后，清理故障转移，记录并保存所有观察结果。 

### <a name="create-and-customize-a-recovery-plan"></a>创建和自定义恢复计划

 验证测试故障转移如期工作后，Contoso 会创建迁移恢复计划， 

- 恢复计划指定故障转移发生的顺序，并指出 Azure VM 将如何在 Azure 中实现联机。
- 由于应用是两层的，因此 Contoso 自定义了恢复计划，使系统先处理数据 VM (SQLVM)，再处理前端 (WEBVM)。

1. 在“恢复计划(Site Recovery)” > “+恢复计划”中，创建一个计划并将其添加 VM。

    ![恢复计划](./media/contoso-migration-rehost-vm/recovery-plan.png)

2. 创建计划后，进行自定义（“恢复计划” > “SmartHotelMigrationPlan” > “自定义”）。
2.  从“组 1: 启动”中删除 WEBVM。  这可确保第一次启动操作只影响 SQLVM。
3.  在“+组” > “添加受保护的项”中，向“组 2: 启动”添加 WEBVM。  VM 需要位于两个不同的组中。


### <a name="migrate-the-vms"></a>迁移 VM


Contoso 现可运行完整故障转移来完成迁移。

1. 依次选择“恢复计划”和“故障转移”。
2. 选择故障转移到最近的恢复点后，Site Recovery 应会在触发故障转移之前先尝试关闭本地 VM。 可在“作业”页面上跟进故障转移进度。

    ![故障转移](./media/contoso-migration-rehost-vm/failover1.png)


3. 故障转移后，验证 Azure VM 是否按预期显示在 Azure 门户中。

    ![故障转移](./media/contoso-migration-rehost-vm/failover2.png)  

3. 验证后，完成每个 VM 的迁移操作。 此时停止复制 VM，且停止就此对 Site Recovery 计费。

    ![故障转移](./media/contoso-migration-rehost-vm/failover3.png)

**需要更多帮助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)如何运行测试故障转移。 
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)如何创建恢复计划。
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)如何故障转移到 Azure。

## <a name="clean-up-after-migration"></a>迁移后清理

迁移完成后，SmartHotel 应用层即在 Azure VM 上运行。

接下来，Contoso 需要完成以下清理步骤：  

- 从 vCenter 清单中删除 WEBVM 计算机。
- 从 vCenter 清单中删除 SQLVM 计算机。
- 从本地备份作业中删除 WEBVM 和 SQLVM。
- 更新内部文档，从而显示新位置以及 WEBVM 的 IP 地址。
- 查看与 VM 交互的所有资源，并更新所有相关设置或文档以反映新配置。

## <a name="review-the-deployment"></a>查看部署

应用现已运行，Contoso 需要积极行动，在 Azure 中全面保护此应用。

### <a name="security"></a>“安全”

Contoso 安全团队会审查 Azure VM，确定是否存在任何安全问题。

- 他们会审查用于 VM 的网络安全组 (NSG)，进而控制访问权限。 NSG 用于确保只有允许的流量可到达应用。
- 他们还考虑使用 Azure 磁盘加密服务和 KeyVault 保护磁盘上的数据。

[详细了解](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) VM 的安全做法。

### <a name="backups"></a>备份

Contoso 计划使用 Azure 备份服务备份 VM 上的数据。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

1. Contoso 目前拥有 VM 许可证并将享有 Azure 混合权益。  公司将对现有的 Azure VM 进行转换，以利用此定价的优势。
2. Contoso 将启用由 Microsoft 子公司 Cloudyn 授权的 Azure 成本管理。 它是一个多云成本管理解决方案，可优化 Azure 和其他云资源的使用和管理。 [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。 

## <a name="conclusion"></a>结束语

在本文中，Contoso 通过使用 Site Recovery 服务将应用 VM 迁移到 Azure VM，在 Azure 中重新托管了 SmartHotel 应用。 


## <a name="next-steps"></a>后续步骤

在本系列的[下一篇文章](contoso-migration-rehost-vm-sql-ag.md)中，将展示 Contoso 如何在 Azure VM 中重新托管 SmartHotel 应用前端 VM，以及如何将数据库迁移到 Azure 中的 SQL Server AlwaysOn 可用性组。


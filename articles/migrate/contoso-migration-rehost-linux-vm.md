---
title: 将本地 Linux 应用迁移和重新托管到 Azure VM | Microsoft Docs
description: 了解 Contoso 如何通过将本地 Linux 应用迁移到 Azure VM 来重新托管该应用。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 200f9c5df0d4165341e38ca9d4dd85ad75c8403c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60675563"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Contoso 迁移：将本地 Linux 应用重新托管到 Azure VM

本文介绍 Contoso 如何将基于 Linux 的本地服务支持应用 (**osTicket**) 重新托管到 Azure IaaS VM。

我们提供一系列的文章展示虚拟公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云中，而本文档是该系列中的一篇文章。 本系列教程介绍了背景信息，同时提供一系列应用场景来描述如何设置迁移基础结构和运行不同类型的迁移。 应用场景越来越复杂。 我们将不断添加更多的文章。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 概述文章系列、Contoso 的迁移策略和该系列所用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | Contoso 准备其本地和 Azure 基础结构进行迁移。 该系列的所有迁移文章共用同一个基础结构。 | 可用
[文章 3：评估要迁移到 Azure 的本地资源](contoso-migration-assessment.md)  | Contoso 评估 VMware 上运行的本地 SmartHotel360 应用。 Contoso 使用 Azure Migrate 服务评估应用 VM，并使用数据迁移助手评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 数据库托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso 将其本地 SmartHotel360 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM。 Contoso 使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 Azure SQL 数据库托管实例。 | 可用   
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | Contoso 使用 Site Recovery 服务将其 SmartHotel360 应用 VM 迁移到 Azure VM。 | 可用
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](contoso-migration-rehost-vm-sql-ag.md) | Contoso 迁移 SmartHotel360 应用。 Contoso 使用 Site Recovery 迁移应用 VM。 它使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用 
文章 7：在 Azure VM 上重新托管 Linux 应用 | Contoso 使用 Azure Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 本文
[文章 8：在 Azure VM 和 Azure MySQL 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | Contoso 使用 Azure Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，并使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | Contoso 将 SmartHotel360 应用迁移到 Azure Web 应用，并使用数据库迁移助手将应用数据库迁移到 Azure SQL Server 实例 | 可用
[文章 10：基于 Azure Web 应用和 Azure MySQL 重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso 使用 Azure 流量管理器将其 Linux osTicket 应用迁移到多个 Azure 区域上的 Azure Web 应用，并与 GitHub 集成以实现持续交付。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 | 可用 
[文章 11：在 Azure DevOps Services 上重构 TFS](contoso-migration-tfs-vsts.md) | Contoso 将其本地 Team Foundation Server 部署迁移到 Azure 中的 Azure DevOps Services。 | 可用
[文章 12：在 Azure 容器和 Azure SQL 数据库上重塑应用架构](contoso-migration-rearchitect-container-sql.md) | Contoso 将其 SmartHotel 应用迁移到 Azure。 然后，它将应用 Web 层重新架构为 Azure Service Fabric 中运行的 Windows 容器，以及具有 Azure SQL 数据库的数据库。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | Contoso 使用一系列 Azure 功能和服务（包括 Azure 应用服务、Azure Kubernetes 服务 (AKS)、Azure Functions、Azure 认知服务和 Azure Cosmos DB）重新生成其 SmartHotel 应用。 | 可用
[文章 14：到 Azure 的大规模迁移](contoso-migration-scale.md) | 尝试过组合迁移后，Contoso 准备大规模整体迁移到 Azure。 | 可用



在本文中，Contoso 将 Linux Apache MySQL PHP (LAMP) 上运行的双层 **osTicket** 应用迁移到 Azure。 将使用 Azure Site Recovery 服务迁移应用 VM。 如果想要使用此开放源代码应用，可从 [GitHub](https://github.com/osTicket/osTicket) 下载。

## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **控制风险**：服务支持应用对于 Contoso 业务至关重要。 Contoso 希望在不遭受任何风险的情况下将其迁移到 Azure。
- **扩展**：Contoso 不希望立即更改应用。 他们只想确保应用的稳定。


## <a name="migration-goals"></a>迁移目标

Contoso 云团队已确定此迁移的目标，以确定最佳迁移方法：

- 迁移后，Azure 中应用的功能应当能够与其当前在本地 VMWare 环境中的功能媲美。  无论在云端还是在本地，应用同样重要。 
- Contoso 不打算投资此应用。  该应用对业务至关重要，但 Contoso 目前只希望将其安全地迁移到云端。
- Contoso 不想更改此应用的 OPS 模型， 而是希望按当前的方式与云中的应用交互。
- Contoso 不想更改应用的功能， 而只想更改它的位置。
- 完成几个 Windows 应用迁移后，Contoso 想要了解如何在 Azure 中使用基于 Linux 的基础结构。


## <a name="solution-design"></a>解决方案设计

在确定目标和要求后，Contoso 设计并审查部署解决方案，确定迁移流程，包括 Contoso 将用于迁移的 Azure 服务。

### <a name="current-app"></a>当前应用

- OSTicket 应用跨两个 VM（**OSTICKETWEB** 和 **OSTICKETMYSQL**）分层。
- 这两个 VM 位于 VMware ESXi 主机 **contosohost1.contoso.com**（6.5 版）上。
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (**contoso-datacenter**)，其中包含一个本地域控制器 (**contosodc1**)

### <a name="proposed-architecture"></a>建议的体系结构

- 由于应用是生产工作负荷，Azure 中的 VM 将驻留在生产资源组 **ContosoRG** 中。
- VM 将迁移到主要区域（美国东部 2），并置于生产网络 (VNET-PROD-EUS2) 中：
    - Web VM 将驻留在前端子网 (PROD-FE-EUS2) 中。
    - 数据库 VM 将驻留在数据库子网 (PROD-DB-EUS2) 中。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。

![方案体系结构](./media/contoso-migration-rehost-linux-vm/architecture.png) 

### <a name="solution-review"></a>解决方案评审

Contoso 通过将利弊清单放置在一起来评估其建议的设计。

**注意事项** | **详细信息**
--- | ---
**优点** | 无需更改这两个应用 VM 即可将它移到 Azure，因此迁移过程非常简单。<br/><br/> 由于 Contoso 对这两个应用 VM 使用直接迁移，因此，无需对应用数据库使用特殊的配置或迁移工具。<br/><br/> Contoso 将保留 Azure 中应用 VM 的完全控制权。 </br>/ b > 应用 Vm 运行 Ubuntu 16.04-TLS，这是认可的 Linux 分发版。 [了解详细信息](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。
**缺点** | 应用的 Web 层和数据层保留为单一故障转移点。 <br/><br/> Contoso 需要继续支持用作 Azure VM 的应用，而不是迁移到 Azure 应用服务和 Azure Database for MySQL 等托管服务。<br/><br/> Contoso 已注意到，如何使用直接迁移 VM 的方法来简化操作，则无法充分利用 [Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/overview) 提供的功能（内置的高可用性、可预测的性能、简单缩放、自动备份和内置安全性）。

### <a name="migration-process"></a>迁移过程

Contoso 将按如下所述完成迁移：

1. 第一步，Contoso 将设置部署 Site Recovery 所需的 Azure 和本地基础结构。
2. 准备 Azure 和本地组件之后，Contoso 为 VM 设置并启用复制。
3. 在复制运行之后，Contoso 通过将 VM 故障转移到 Azure 来实现迁移。

![迁移过程](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 该服务协调和管理 Azure VM、本地 VM 与物理服务器的迁移和灾难恢复。  | 在复制到 Azure 的过程中，会产生 Azure 存储费用。  故障转移时会创建 Azure VM，因此会产生费用。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/)费用和定价。

 
## <a name="prerequisites"></a>必备组件

对于此方案，Contoso 需具备以下项。

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在前面的系列文章中，Contoso 已创建订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如需更加细化的权限，请查看[此文](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Azure 基础结构** | Contoso 按照[用于迁移的 Azure 基础结构](contoso-migration-infrastructure.md)中所述设置其 Azure 基础结构。<br/><br/> 详细了解有关 Site Recovery 的具体[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)要求。
**本地服务器** | 本地 vCenter Server 应运行版本 5.5、6.0 或 6.5<br/><br/> 运行版本 5.5、6.0 或 6.5 的 ESXi 主机<br/><br/> ESXi 主机上运行的一个或多个 VMware VM。
**本地 VM** | 审查支持使用 Site Recovery 迁移的 [Linux 计算机](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)。<br/><br/> 验证支持的 [Linux 文件和存储系统](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage)。<br/><br/> VM 必须符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。


## <a name="scenario-steps"></a>方案步骤

下面是 Contoso 完成迁移的步骤：

> [!div class="checklist"]
> * **步骤 1：为 Site Recovery 准备 Azure**：Contoso 创建用于保存复制数据的 Azure 存储帐户，并创建恢复服务保管库。
> * **步骤 2：为 Site Recovery 准备本地 VMware**：Contoso 准备用于发现 VM 和安装代理的帐户，并准备好在故障转移后连接到 Azure VM。
> * **步骤 3：复制 VM**：Contoso 设置源和目标迁移环境、创建复制策略，并开始将 VM 复制到 Azure 存储。
> * **步骤 4：使用 Site Recovery 迁移 VM**：Contoso 运行测试故障转移，确保一切正常工作，然后运行完整故障转移将 VM 迁移到 Azure。


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>步骤 1：为 Site Recovery 服务准备 Azure

Contoso 需要几个用于 Site Recovery 的 Azure 组件：

- 用于保留复制的数据的新 Azure 存储帐户。 
- Azure 中的恢复服务保管库。
- 一个 VNet，用于放置故障转移后的资源。 Contoso 已在 [Azure 基础结构部署](contoso-migration-infrastructure.md)期间创建 VNet，因此，他们只需创建存储帐户和保管库。

1. Contoso 管理员在“美国东部 2”区域中创建了一个 Azure 存储帐户 (contosovmsacc20180528)。

    - 存储帐户必须位于与恢复服务保管库相同的区域。
    - 他们使用具有标准存储空间的常规用途帐户，并使用 LRS 复制。

      ![Site Recovery 存储](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. 网络和存储帐户就绪后，创建一个保管库 (ContosoMigrationVault)，并将其置于美国东部 2 主要区域的 **ContosoFailoverRG** 资源组中。

    ![恢复服务保管库](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)如何为 Site Recovery 设置 Azure。


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>步骤 2：为 Site Recovery 准备本地 VMware

Contoso 管理员按如下所述准备本地 VMware 基础结构：

- 在 vCenter 服务器或 vSphere ESXi 主机上创建一个帐户，用于自动发现 VM。
- 创建一个帐户，用于在要复制的 VMware VM 上自动安装移动服务。
- 准备本地 VM，使其可在迁移后创建时连接到 Azure VM。


### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 至少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

Contoso 管理员按如下所述设置帐户：

1. 在 vCenter 级别创建一个角色。
2. 然后向该角色分配所需权限。



### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要迁移的 Linux VM 上安装移动服务。

- 启用 VM 复制后，Site Recovery 可自动推送安装此组件。
- 若要执行自动推送安装，需要准备一个由 Site Recovery 用于访问 VM 的帐户。
- 在复制安装过程中输入帐户详细信息。 
- 帐户可为域帐户，也可为本地帐户，但必须具有在 VM 上进行安装的权限。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

故障转移到 Azure 之后，Contoso 希望能够连接到 Azure 中复制的 VM。 为此，Contoso 管理员需要执行几项操作：

- 为了通过 Internet 访问 Azure VM，需在迁移之前在本地 Linux VM 上启用 SSH。  对于 Ubuntu，可使用以下命令完成此操作：**Sudo apt-get ssh install -y**。
- 运行迁移（故障转移）后，可以选中“启动诊断”查看 VM 的屏幕截图。
- 如果此操作无效，则需要检查 VM 是否正在运行，并查看[故障排除提示](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


**需要更多帮助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)如何创建和分配用于自动发现的角色。
- [了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)如何创建用于推送安装移动服务安装的帐户。


## <a name="step-3-replicate-the-on-premises-vms"></a>步骤 3：复制本地 VM

在将 Web VM 迁移到 Azure 之前，Contoso 管理员需要设置并启用复制。

### <a name="set-a-protection-goal"></a>设置保护目标

1. 在保管库中，在保管库名称 (ContosoVMVault) 下，设置复制目标（“入门” > “Site Recovery” > “准备基础结构”。
2. 指定计算机位于本地、是 VMware VM，且计划复制到 Azure。
    ![复制目标](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>确认部署规划

接下来，选择“是的，已完成”确认已完成部署计划。 Contoso 在此方案中仅迁移单个 VM，无需部署计划。

### <a name="set-up-the-source-environment"></a>设置源环境

Contoso 管理员现在需要配置源环境。 为此，下载 OVF 模板，然后使用它将 Site Recovery 配置服务器部署为可用性高的本地 VMware VM。 设置并运行配置服务器后，在保管库中进行注册。

配置服务器运行多个组件：

- 用于协调本地与 Azure 之间的通信并管理数据复制的配置服务器组件。
- 充当复制网关的进程服务器。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。
- 进程服务器还会将移动服务安装在要复制的 VM 上，并执行针对本地 VMware VM 的自动发现。

Contoso 管理员执行以下步骤：

1. 从“准备基础结构” > “源” > “配置服务器”下载 OVF 模板。
    
    ![下载 OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. 将此模板导入 VMware 以创建和部署 VM。

    ![OVF 模板](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. 首次打开 VM 时，系统将进入 Windows Server 2016 安装体验。 此时接受许可协议并输入管理员密码。
4. 安装完成后，以管理员身份登录到 VM。 首次登录时，系统默认运行 Azure Site Recovery 配置工具。
5. 在此工具中，指定在保管库中注册配置服务器时要使用的名称。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器所要注册到的保管库。

    ![注册配置服务器](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机之后，配置服务器管理向导会自动启动。
9. 在向导中，选择要接收复制流量的 NIC。 此设置一经配置便无法更改。
10. 选择订阅、资源组以及配置服务器要注册到的保管库。

    ![保管库](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. 然后下载并安装 MySQL 服务器和 VMWare PowerCLI。 
12. 验证后，指定 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 保留默认端口，并指定 vCenter 服务器的友好名称。
13. 指定创建用于自动发现的帐户，同时指定用于自动安装移动服务的凭据。

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. 完成注册后，在 Azure 门户中检查保管库的“源”页上是否列出了配置服务器和 VMware 服务器。 发现可能需要 15 分钟或更长时间。 
15. 然后，Site Recovery 连接到 VMware 服务器并发现 VM。

### <a name="set-up-the-target"></a>设置目标

Contoso 管理员现在配置目标复制设置。

1. 在“准备基础结构” > “目标”中，选择目标设置。
2. Site Recovery 检查指定目标中是否具有 Azure 存储帐户和网络。

### <a name="create-a-replication-policy"></a>创建复制策略

设置源和目标后，可以创建复制策略。

1. 在“准备基础结构” > “复制设置” > “复制策略” >  “创建和关联”中，创建策略“ContosoMigrationPolicy”。
2. 使用默认设置：
    - **RPO 阈值**：默认为 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
    - **恢复点保留期**。 默认为 24 小时。 此值指定每个恢复点的保留时长。 可以将复制的虚拟机恢复到窗口中的任何点。
    - **应用一致性快照频率**。 默认为 1 小时。 此值指定应用程序一致性快照的创建频率。
 
        ![创建复制策略](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. 此策略自动与配置服务器关联。 

    ![关联复制策略](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**需要更多帮助？**

- 有关上述所有步骤的完整演练，可参阅 [针对本地 VMware VM 设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)。
- 还可查看详细说明，了解如何[设置源环境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[配置复制设置](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。
- [详细了解](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)适用于 Linux 的 Azure 来宾代理。

**需要更多帮助？**

- 有关上述所有步骤的完整演练，可参阅 [针对本地 VMware VM 设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)。
- 还可查看详细说明，了解如何[设置源环境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[配置复制设置](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。
- [详细了解](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)适用于 Linux 的 Azure 来宾代理。



### <a name="enable-replication-for-osticketweb"></a>为 OSTICKETWEB 启用复制

现在，Contoso 管理员可以开始复制 **OSTICKETWEB** VM。

1. 在“复制应用程序” > “源” > “+复制”中，选择源设置。
2. 他们选择要启用虚拟机并选择源设置，包括 vCenter 服务器和配置服务器。

    ![启用复制](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. 指定目标设置，包括 Azure VM 在故障转移后所属的资源组和 VNet，以及复制数据要存储到的存储帐户。

     ![启用复制](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. 选择“OSTICKETWEB”VM 进行复制。 

   - 在此阶段，他们仅选择了“OSTICKETWEB”，因为必须选择 VNet 和子网，而 VM 不在同一子网中。
   - 为 VM 启用复制后，Site Recovery 会自动安装移动服务。

     ![启用复制](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. 在 VM 属性中，他们选择了进程服务器在计算机上自动安装移动服务时使用的帐户。

     ![移动服务](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. 在“复制设置” > “配置复制设置”中，检查是否已应用正确的复制策略，然后选择“启用复制”。
6. 在“作业”中跟踪复制进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。



### <a name="enable-replication-for-osticketmysql"></a>为 OSTICKETMYSQL 启用复制

Contoso 管理员现在可以开始复制 **OSTICKETMYSQL**。

1. 在“复制应用程序” > “源” > “+复制”中，选择源和目标设置。
2. 选择“OSTICKETMYSQL”VM 进行复制，并选择用于安装移动服务的帐户。

    ![启用复制](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. 应用 OSTICKETWEB 所用的同一复制策略，然后启用复制。  

**需要更多帮助？**

有关上述所有步骤的完整演练，可参阅[启用复制](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)。


## <a name="step-4-migrate-the-vms"></a>步骤 4：迁移 VM 

Contoso 管理员运行快速测试故障转移，然后迁移 VM。

### <a name="run-a-test-failover"></a>运行测试故障转移

迁移之前，可运行测试故障转移，帮助保证一切如期正常工作。 

1. 运行测试故障转移，将内容移动到最近可用的时间点（“最新处理”）。
2. 选择“在开始故障转移前关闭计算机”，以便 Site Recovery 在触发故障转移之前尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 
3. 运行测试故障转移： 
    - 运行先决条件检查，确保满足迁移所需的所有条件。
    - 故障转移处理数据，以便创建 Azure VM。 如果选择了最新恢复点，则基于该数据创建恢复点。
    - 使用上一步中处理的数据创建 Azure VM。
3. 故障转移完成后，Azure VM 副本将在 Azure 门户中显示。 检查 VM 是否大小适当、已连接到正确的网络，并且正在运行。 
4. 验证后，清理故障转移，记录并保存任何观察结果。

### <a name="create-and-customize-a-recovery-plan"></a>创建和自定义恢复计划

 验证测试故障转移如期工作后，Contoso 管理员为迁移创建恢复计划。 

- 恢复计划指定故障转移的顺序，以及 Azure VM 如何在 Azure 中联机。
- 由于 Contoso 想要迁移双层应用，因此将自定义恢复计划，使数据 VM (SQLVM) 在前端 (WEBVM) 之前启动。


1. 在“恢复计划(Site Recovery)” > “+恢复计划”中，创建一个计划并将其添加 VM。

    ![恢复计划](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. 创建计划后，选择该计划进行自定义（“恢复计划” > “OsTicketMigrationPlan” > “自定义”）。
3.  从“组 1: 启动”中删除“OSTICKETWEB”。  这可以确保第一个启动操作只会影响 **OSTICKETMYSQL**。

    ![恢复组](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  在“+组” > “添加受保护的项”中，将“OSTICKETWEB”添加到“组 2:启动”。  需在两个不同的组中添加这些项。

    ![恢复组](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>迁移 VM


Contoso 管理员现已准备好根据恢复计划运行故障转移，以迁移 VM。

1. 选择计划 >“故障转移”。
2.  选择故障转移到最新的恢复点，并指定 Site Recovery 应在触发故障转移之前尝试关闭本地 VM。 可在“作业”页面上跟进故障转移进度。

    ![故障转移](./media/contoso-migration-rehost-vm/failover1.png)

3. 在故障转移期间，vCenter Server 发出命令，以停止在 ESXi 主机上运行的两个 VM。

    ![故障转移](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. 故障转移后，验证 Azure VM 是否按预期显示在 Azure 门户中。

    ![故障转移](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. 在 Azure 中验证 VM 后完成迁移后，以完成每个 VM 的迁移过程。 这会停止 VM 的复制，并停止对 VM 的 Site Recovery 计费。

    ![故障转移](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>将 VM 连接到数据库

作为迁移过程的最后一步，Contoso 管理员更新为指向上运行的应用程序数据库的应用程序的连接字符串**OSTICKETMYSQL** VM。 

1. Contoso 使用 Putty 或其他 SSH 客户端来与 **OSTICKETWEB** VM 建立 SSH 连接。 由于 VM 为专用，因此，他们使用专用 IP 地址进行连接。

    ![连接到数据库](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![连接到数据库](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. 需确保 **OSTICKETWEB** VM 能够与 **OSTICKETMYSQL** VM 通信。 当前，已使用本地 IP 地址 172.16.0.43 对配置进行硬编码。

    **更新之前**
    
    ![更新 IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **更新之后**
    
    ![更新 IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. 使用 **systemctl restart apache2** 重启服务。

    ![重新启动](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. 最后，在某个 Contoso 域控制器上更新 **OSTICKETWEB** 和 **OSTICKETMYSQL** 的 DNS 记录。

    ![更新 DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![更新 DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**需要更多帮助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)如何运行测试故障转移。 
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)如何创建恢复计划。
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)如何故障转移到 Azure。

## <a name="clean-up-after-migration"></a>迁移后的清理

迁移完成后，osTicket 应用层将在 Azure VM 上运行。

现在，Contoso 需按如下所述进行清理： 

- 从 vCenter 清单中删除本地 VM。
- 从本地备份作业中删除本地 VM。
- 更新内部文档，以显示新位置以及 OSTICKETWEB 和 OSTICKETMYSQL 的 IP 地址。
- 查看与 VM 交互的所有资源，并更新所有相关设置或文档以反映新配置。
- Contoso 结合使用 Azure Migrate 服务与依赖项映射来评估用于迁移的 VM。 管理员应从 VM 中删除出于此目的而安装的 Microsoft Monitoring Agent 和依赖项代理。

## <a name="review-the-deployment"></a>查看部署

在应用正在运行的情况下，Contoso 需要完全操作和保护其新的基础结构。

### <a name="security"></a>安全

Contoso 安全团队审查 OSTICKETWEB 和 OSTICKETMYSQL VM，以确定任何安全问题。

- 团队需审查 VM 的网络安全组 (NSG)，以控制访问。 NSG 用于确保仅允许到应用程序的流量可以通过。
- 团队还应考虑使用 Azure 磁盘加密服务和 Azure KeyVault 保护 VM 磁盘上的数据。

[详细了解](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms) VM 的安全做法。

### <a name="bcdr"></a>BCDR

对于业务连续性和灾难恢复，Contoso 采取了以下措施：

- **确保数据安全**：Contoso 使用 Azure 备份服务备份 VM 上的数据。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- **使应用保持正常运行**：Contoso 使用 Site Recovery 将 Azure 中的应用 VM 复制到次要区域。 [了解详细信息](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

- 部署资源后，Contoso 分配 [Azure 基础结构部署](contoso-migration-infrastructure.md#set-up-tagging)期间定义的 Azure 标记。
- Contoso 的 Ubuntu 服务器没有许可问题。
- Contoso 将启用由 Microsoft 子公司 Cloudyn 许可的 Azure 成本管理。 该服务是一个多云成本管理解决方案，可帮助利用和管理 Azure 与其他云资源。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。 


## <a name="next-steps"></a>后续步骤

本文已介绍 Contoso 如何使用 Azure Site Recovery，将两个 Linux VM 上分层的本地服务支持应用迁移到 Azure IaaS VM。

本系列教程的下一篇文章介绍 Contoso 如何将同一个服务支持应用迁移到 Azure。 这一次，Contoso 将使用 Site Recovery 来迁移应用的前端 VM，并使用 MySQL Workbench 工具通过备份和还原将应用数据库迁移到 Azure Database for MySQL。 [入门](contoso-migration-rehost-linux-vm-mysql.md)。

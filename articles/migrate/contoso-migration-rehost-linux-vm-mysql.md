---
title: 将 Contoso Linux 服务支持应用重新托管到 Azure 和 Azure MySQL | Microsoft Docs
description: 了解 Contoso 如何通过将本地 Linux 应用迁移到 Azure VM 和 Azure MySQL 来重新托管该应用。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 15a429c033cfd1598dd01b5c8cd2743c397dacdb
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225511"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms-and-azure-mysql"></a>Contoso 迁移：将本地 Linux 应用重新托管到 Azure VM 和 Azure MySQL

本文演示 Contoso 如何通过将其本地双层 Linux 服务支持应用 (osTicket) 迁移到 Azure 和 Azure MySQL 来重新托管该应用。

此文件是展示虚构公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云的一系列文章中的第八篇。 该系列包括背景信息和说明如何设置迁移基础结构及运行不同类型的迁移的方案。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。

**文章** | **详细信息** | **Status**
--- | --- | ---
[第 1 篇：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有的 Contoso 迁移方案共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 公司使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [Azure 数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：重新托管到 Azure VM 和 SQL 托管实例](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure。 他们使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用 Web VM，使用 [Azure 数据库迁移](https://docs.microsoft.com/azure/dms/dms-overview)服务将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：重新托管到 Azure VM](contoso-migration-rehost-vm.md) | 展示 Contoso 如何使用 Site Recovery 服务将其 SmartHotel 迁移到 Azure IaaS VM。
[文章 6：重新托管到 Azure VM 和 SQL Server 可用性组](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 公司使用 Site Recovery 迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到 SQL Server 可用性组。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Azure Site Recovery 将其 osTicket Linux 应用迁移到 Azure IaaS VM。
文章 8：将 Linux 应用重新托管到 Azure VM 和 Azure MySQL 服务器（本文） | 演示 Contoso 如何迁移 osTicket Linux 应用。 他们使用 Site Recovery 进行 VM 迁移，使用 MySQL Workbench 迁移到 Azure MySQL 服务器实例。 | 可用

在本文中，Contoso 将双层 Linux Apache MySQL PHP (LAMP) 服务支持应用 (osTicket) 迁移到 Azure。 如果想要使用此开放源代码应用，可从 [GitHub](https://github.com/osTicket/osTicket) 下载。



## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解其想要实现的目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **控制风险**：服务支持应用对于 Contoso 业务至关重要。 公司希望在不遭受任何风险的情况下将其迁移到 Azure。
- **扩展**：Contoso 不希望立即更改应用。 他们只想保持其稳定。


## <a name="migration-goals"></a>迁移目标

Contoso 云团队已确定此迁移的目标，以便确定最佳迁移方法：

- 迁移后，Azure 中应用的功能应当能够与其当前在本地 VMWare 环境中的功能媲美。  无论在云端还是在本地，应用同样重要。 
- Contoso 不打算投资此应用。  该用对业务至关重要，但公司目前只希望将其安全地迁移到云端。
- 完成几个 Windows 应用迁移后，Contoso 想要了解如何在 Azure 中使用基于 Linux 的基础结构。
- Contoso 希望，将应用程序移动到云之后，可以最大程度地减轻数据库管理员任务。

## <a name="proposed-architecture"></a>建议的体系结构

在本方案中：

- 应用分层到两个 VM（OSTICKETWEB 和 OSTICKETMYSQL）中。
- 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com（6.5 版）上。
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (vcenter.contoso.com) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- OSTICKETWEB 上的 Web 层应用将迁移到 Azure IaaS VM。
- 应用数据库将迁移到 Azure Database for MySQL PaaS 服务。
- 因为是迁移生产工作负荷，所以资源将驻留在生产资源组 ContosoRG 中。
- 资源将复制到主要区域（美国东部 2），并置于生产网络 (VNET-PROD-EUS2) 中：
    - Web VM 将驻留在前端子网 (PROD-FE-EUS2) 中。
    - 数据库实例将驻留在数据库子网 (PROD-DB-EUS2) 中。
- 应用数据库将通过 MySQL 工具迁移到 Azure MySQL。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。


![方案体系结构](./media/contoso-migration-rehost-linux-vm-mysql/architecture.png) 


## <a name="migration-process"></a>迁移过程

Contoso 将按如下方式完成迁移进程：

迁移 Web VM：

1. 第一步，Contoso 将设置部署 Site Recovery 所需的 Azure 和本地基础结构。
2. 准备好 Azure 和本地组件后，设置并启用 Web VM 复制。
3. 启动并运行复制后，通过将 VM 故障转移到 Azure 来迁移该 VM。

迁移数据库：

1. Contoso 在 Azure 中设置 MySQL 实例。
2. 设置 MySQL workbench，并在本地备份数据库。
3. 然后将数据库从本地备份还原到 Azure。

![迁移过程](./media/contoso-migration-rehost-linux-vm-mysql/migration-process.png) 


### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 该服务协调和管理 Azure VM、本地 VM 与物理服务器的迁移和灾难恢复。  | 在复制到 Azure 的过程中，会产生 Azure 存储费用。  故障转移时会创建 Azure VM，因此会产生费用。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/)费用和定价。
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | 数据库基于开放源代码 MySQL 服务器引擎。 它提供完全托管的企业就绪社区 MySQL 数据库即服务，用于应用开发和部署。 

 
## <a name="prerequisites"></a>先决条件

如果你（和 Contoso）想要运行此方案，应做好以下准备。

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在学习本系列前几篇文章期间，你应该已创建了一个订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如需更加细化的权限，请查看[此文](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Azure 基础结构** | Contoso 按照[用于迁移的 Azure 基础结构](contoso-migration-infrastructure.md)中所述设置其 Azure 基础结构。<br/><br/> 详细了解有关 Site Recovery 的具体[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)要求。
**本地服务器** | 本地 vCenter Server 应运行版本 5.5、6.0 或 6.5<br/><br/> 运行版本 5.5、6.0 或 6.5 的 ESXi 主机<br/><br/> ESXi 主机上运行的一个或多个 VMware VM。
**本地 VM** | [评审通过 Site Recovery 进行迁移所支持的 Linux VM 需求](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines)。<br/><br/> 验证支持的 [Linux 文件和存储系统](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage)。<br/><br/> VM 必须符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。


## <a name="scenario-steps"></a>方案步骤

下面是 Azure 完成迁移的步骤：

> [!div class="checklist"]
> * **步骤 1：为 Site Recovery 准备 Azure**：创建用于保存复制数据的 Azure 存储帐户，并创建恢复服务保管库。
> * **步骤 2：为 Site Recovery 准备本地 VMware**：为 VM 发现和代理安装准备帐户，并准备好在故障转移后连接到 Azure VM。
 * **步骤 3：设置数据库**：在 Azure 中，设置 Azure MySQL 数据库的实例。
> * **步骤 4：复制 VM**：配置 Site Recovery 源和目标环境、设置复制策略，然后开始将 VM 复制到 Azure 存储。
> * **步骤 5：迁移数据库**：通过 MySQL 工具设置迁移。
> * **步骤 6：通过 Site Recovery 迁移 VM**：运行测试故障转移，确保一切正常工作，然后运行完整的故障转移将 VM 迁移到 Azure。




## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>步骤 1：为 Site Recovery 服务准备 Azure

Contoso 需要几个用于 Site Recovery 的 Azure 组件：

- 故障转移资源所在的 VNet（Contoso 将使用已部署的生产 VNet）。
- 用于保留复制的数据的新 Azure 存储帐户。 
- Azure 中的恢复服务保管库。

Contoso 已在 [Azure 基础结构部署](contoso-migration-infrastructure.md)期间创建 VNet，因此，他们只需创建存储帐户和保管库。


1. Contoso 在美国东部 2 区域中创建 Azure 存储帐户 (contosovmsacc20180528)。

    - 存储帐户必须位于与恢复服务保管库相同的区域。
    - Contoso 使用常规用途帐户，它包含标准存储和 LRS 复制。

    ![Site Recovery 存储](./media/contoso-migration-rehost-linux-vm-mysql/asr-storage.png)

3. 准备好网络和存储帐户后，Contoso 创建保管库 (ContosoMigrationVault)，并将其放入主要美国东部 2 区域中的 ContosoFailoverRG 资源组中。

    ![恢复服务保管库](./media/contoso-migration-rehost-linux-vm-mysql/asr-vault.png)

**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)如何为 Site Recovery 设置 Azure。


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>步骤 2：为 Site Recovery 准备本地 VMware

Contoso 按如下所示准备本地 VMware 基础结构：

- 在 vCenter 服务器上创建一个帐户，以自动化 VM 发现。
- 创建一个帐户，用于在要复制的 VMware VM 上自动安装移动服务。
- 准备本地 VM，以便连接到迁移后创建的 Azure VM。


### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 至少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

Contoso 如下设置帐户：

1. Contoso 在 vCenter 级别创建一个角色。
2. 然后，Contoso 向该角色分配所需权限。


### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在 Contoso 想要迁移的每个 VM 上安装移动服务。

- 如果为 VM 启用了复制，Site Recovery 可以执行此组件的自动推送安装。
- 对于自动安装。 Site Recovery 需要一个有权访问 VM 的帐户。 
- 在复制安装过程中输入帐户详细信息。 
- 该帐户可以是域或本地帐户，只要具有安装权限即可。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

故障转移到 Azure 后，Contoso 想要能够连接到 Azure VM。 为实现这一目标，需要执行几个操作： 

- 为了通过 Internet 进行访问，迁移前在本地 Linux VM 上启用 SSH。  对于 Ubuntu，可使用以下命令：Sudo apt-get ssh install -y 完成此操作。
- 故障转移后，应选中“启动诊断”查看 VM 的屏幕截图。
- 如果这不起作用，则需要验证 VM 是否正在运行，并查看这些[故障排除提示](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

需要更多帮助？

- [了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)如何创建和分配用于自动发现的角色。
- [了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)如何创建用于推送安装移动服务安装的帐户。


## <a name="step-3-provision-azure-database-for-mysql"></a>步骤 3：设置 Azure Database for MySQL

Contoso 在主要美国东部 2 区域中设置 MySQL 数据库实例。

1. 在 Azure 门户中，创建 Azure Database for MySQL 资源。 

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-1.png)

2. 为 Azure 数据库添加名称 contosoosticket。 将数据库添加到生产资源组 ContosoRG，并为其指定凭据。
3. 本地 MySQL 数据库版本为 5.7，因此，他们选择此版本以便获得兼容性。 他们使用满足其数据库需求的默认大小。

     ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-2.png)

4. 对于“备份冗余选项”，Contoso 选择使用“异地冗余”。 通过此选项，发生中断时可在其次要美国中部区域中还原数据库。 仅在设置数据库时，才能配置此选项。

     ![冗余](./media/contoso-migration-rehost-linux-vm-mysql/db-redundancy.png)

4. 在“VNET-PROD-EUS2”网络 >“服务终结点”中，为 SQL 服务添加服务终结点（数据库子网）。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-3.png)

5. 添加子网后，创建允许从生产网络中的数据库子网进行访问的虚拟网络规则。

    ![MySQL](./media/contoso-migration-rehost-linux-vm-mysql/mysql-4.png)


## <a name="step-4-replicate-the-on-premises-vms"></a>步骤 4：复制本地 VM

Contoso 需先设置和启用复制，然后才能将 Web VM 迁移到 Azure。

### <a name="set-a-protection-goal"></a>设置保护目标

1. 在保管库中，在保管库名称 (ContosoVMVault) 下，设置复制目标（“入门” > “Site Recovery” > “准备基础结构”。
2. 指定计算机位于本地、是 VMware VM，且计划复制到 Azure。

    ![复制目标](./media/contoso-migration-rehost-linux-vm-mysql/replication-goal.png)

### <a name="confirm-deployment-planning"></a>确认部署规划

接下来，选择“是的，已完成”确认已完成部署计划。 Contoso 在此方案中仅迁移单个 VM，无需部署计划。

### <a name="set-up-the-source-environment"></a>设置源环境

Contoso 需要配置其源环境。 为执行此操作，他们使用 OVF 模板将 Site Recovery 配置服务器部署为高可用性本地 VMware VM。 设置并运行配置服务器后，在保管库中进行注册。

配置服务器运行多个组件：

- 用于协调本地与 Azure 之间的通信并管理数据复制的配置服务器组件。
- 充当复制网关的进程服务器。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。
- 进程服务器还会将移动服务安装在要复制的 VM 上，并执行针对本地 VMware VM 的自动发现。

Contoso 执行以下步骤：


1. 从“准备基础结构” > “源” > “配置服务器”下载 OVF 模板。
    
    ![下载 OVF](./media/contoso-migration-rehost-linux-vm-mysql/add-cs.png)

2. 将此模板导入 VMware 以创建和部署 VM。

    ![OVF 模板](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-wizard.png)

3. 首次打开 VM 时，系统将进入 Windows Server 2016 安装体验。 此时接受许可协议并输入管理员密码。
4. 安装完成后，以管理员身份登录到 VM。 首次登录时，系统默认运行 Azure Site Recovery 配置工具。
5. 在此工具中，指定在保管库中注册配置服务器时要使用的名称。
6. 该工具会检查 VM 是否能够连接到 Azure。
7. 建立连接后，登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器要注册到的保管库。

    ![注册配置服务器](./media/contoso-migration-rehost-linux-vm-mysql/config-server-register2.png)

8. 该工具将执行一些配置任务，然后重新启动。
9. 再次登录到计算机之后，配置服务器管理向导会自动启动。
10. 在向导中，选择要接收复制流量的 NIC。 此设置一经配置便无法更改。
11. 选择订阅、资源组以及配置服务器要注册到的保管库。

    ![保管库](./media/contoso-migration-rehost-linux-vm-mysql/cswiz1.png) 

12. 现在，下载并安装 MySQL 服务器和 VMWare PowerCLI。 
13. 验证后，指定 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 保留默认端口，并指定 vCenter 服务器的友好名称。
14. 输入为自动发现创建的帐户以及 Site Recovery 将用于自动安装移动服务的凭据。 

    ![vCenter](./media/contoso-migration-rehost-linux-vm-mysql/cswiz2.png)

14. 注册完成后，在 Azure 门户中，Contoso 检查配置服务器和 VMware 服务器是否已在保管库中的“源”页上列出。 发现可能需要 15 分钟或更长时间。 
15. 一切准备就绪后，Site Recovery 将连接到 VMware 服务器并发现 VM。

### <a name="set-up-the-target"></a>设置目标

现在，Contoso 输入目标复制设置。

1. 在“准备基础结构” > “目标”中，选择目标设置。
2. Site Recovery 检查指定目标中是否具有 Azure 存储帐户和网络。


### <a name="create-a-replication-policy"></a>创建复制策略

设置好源和目标后，Contoso 准备创建复制策略。

1. 在“准备基础结构” > “复制设置” > “复制策略” >  “创建和关联”中，创建策略“ContosoMigrationPolicy”。
2. 使用默认设置：
    - **RPO 阈值**：默认为 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
    - **恢复点保留期**： 默认为 24 小时。 此值指定每个恢复点的保留时长。 可以将复制的虚拟机恢复到窗口中的任何点。
    - **应用一致性快照频率**： 默认为 1 小时。 此值指定应用程序一致性快照的创建频率。
 
        ![创建复制策略](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy.png)

5. 此策略自动与配置服务器关联。 

    ![关联复制策略](./media/contoso-migration-rehost-linux-vm-mysql/replication-policy2.png)


**需要更多帮助？**

- 有关上述所有步骤的完整演练，可参阅 [针对本地 VMware VM 设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)。
- 还可查看详细说明，了解如何[设置源环境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[配置复制设置](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。
- [详细了解](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)适用于 Linux 的 Azure 来宾代理。

### <a name="enable-replication-for-the-web-vm"></a>为 Web VM 启用复制

现在，Contoso 可以开始复制 OSTICKETWEB VM。

1. 在“复制应用程序” > “源” > “+复制”中，选择源设置。
2. 他们指出，他们想要启用虚拟机并选择源设置，包括 vCenter 服务器和配置服务器。

    ![启用复制](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication-source.png)

3. 现在，指定目标设置。 其中包括资源组和网络（Azure VM 在故障转移后将位于该网络中），以及其中将存储复制的数据的存储帐户。 

     ![启用复制](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication2.png)

3. Contoso 选择“OSTICKETWEB”用于复制。 

    ![启用复制](./media/contoso-migration-rehost-linux-vm-mysql/enable-replication3.png)

4. 在 VM 属性中，选择应用于在 VM 上自动安装移动服务的帐户。

     ![移动服务](./media/contoso-migration-rehost-linux-vm-mysql/linux-mobility.png)

5. 在“复制设置” > “配置复制设置”中，检查已应用正确的复制策略，然后选择“启用复制”。 将自动安装移动服务。
6.  在“作业”中跟踪复制进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。


**需要更多帮助？**

有关上述所有步骤的完整演练，可参阅[启用复制](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)。


## <a name="step-5-migrate-the-database"></a>步骤 5：迁移数据库

Contoso 将使用备份和还原通过 MySQL 工具迁移数据库。 安装 MySQL Workbench，从 OSTICKETMYSQL 备份数据库，然后将其还原到 Azure Database for MySQL 服务器。

### <a name="install-mysql-workbench"></a>安装 MySQL Workbench

1. Contoso 检查[必备项并下载 MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool)。
2. 根据[安装说明](https://dev.mysql.com/doc/workbench/en/wb-installing.html)安装适用于 Windows 的 MySQL Workbench。
3. 在 MySQL Workbench 中，创建到 OSTICKETMYSQL 的 MySQL 连接。 

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench1.png)

4. 将数据库作为“osticket”导出到本地自包含文件。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench2.png)

5. 在本地备份数据库后，创建到 Azure Database for MySQL 实例的连接。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench3.png)

6. 现在，Contoso 可从自包含文件的 Azure MySQL 实例中导入（还原）数据库。 将为该实例创建新架构 (osticket)。

    ![MySQL Workbench](./media/contoso-migration-rehost-linux-vm-mysql/workbench4.png)

## <a name="step-6-migrate-the-vms-with-site-recovery"></a>步骤 6：使用 Site Recovery 迁移 VM

Contoso 运行快速测试故障转移，然后迁移 VM。

### <a name="run-a-test-failover"></a>运行测试故障转移

在迁移之前，运行测试故障转移可帮助验证一切正常工作。 

1. Contoso 运行测试故障转移，将内容移动到最近可用的时间点（“最新处理”）。
2. 选择“在开始故障转移前关闭计算机”，以便 Site Recovery 在触发故障转移之前尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 
3. 运行测试故障转移： 

    - 运行先决条件检查，确保满足迁移所需的所有条件。
    - 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
    - 使用上一步中处理的数据创建 Azure VM。

3. 故障转移完成后，Azure VM 副本将在 Azure 门户中显示。 然后，Contoso 检查 VM 是否大小合适、已连接到正确的网络且正在运行。 
4. 验证后，清理故障转移，记录并保存任何观察结果。

### <a name="migrate-the-vm"></a>迁移 VM

为了迁移 VM，Contoso 创建了包含 VM 的恢复计划，并将该计划故障转移到了 Azure。

1. Contoso 创建了一个计划，并向其添加了“OSTICKETWEB”。

    ![恢复计划](./media/contoso-migration-rehost-linux-vm-mysql/recovery-plan.png)

2. 针对计划运行故障转移。 选择最新恢复点，并指定 Site Recovery 应在触发故障转移前尝试关闭本地 VM。 可在“作业”页面上跟进故障转移进度。

    ![故障转移](./media/contoso-migration-rehost-linux-vm-mysql/failover1.png)

3. 在故障转移期间，vCenter Server 发出命令，以停止在 ESXi 主机上运行的两个 VM。

    ![故障转移](./media/contoso-migration-rehost-linux-vm-mysql/vcenter-failover.png)

4. 故障转移后，Contoso 验证 Azure VM 按预期显示在 Azure 门户中。

    ![故障转移](./media/contoso-migration-rehost-linux-vm-mysql/failover2.png)  

5. 检查 VM 后，迁移完成。 这将停止 VM 的复制，并停止对 VM 的 Site Recovery 计费。

    ![故障转移](./media/contoso-migration-rehost-linux-vm-mysql/failover3.png)

**需要更多帮助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)如何运行测试故障转移。 
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)如何创建恢复计划。
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)如何故障转移到 Azure。


### <a name="connect-the-vm-to-the-database"></a>将 VM 连接到数据库

作为迁移进程的最后一步，Contoso 将应用的连接字符串更新为指向 Azure Database for MySQL。 

1. 他们使用 Putty 或另一 SSH 客户端建立到 OSTICKETWEB VM 的 SSH 连接。 由于 VM 为专用，因此，他们使用专用 IP 地址进行连接。

    ![连接到数据库](./media/contoso-migration-rehost-linux-vm-mysql/db-connect.png)  

    ![连接到数据库](./media/contoso-migration-rehost-linux-vm-mysql/db-connect2.png)  

2. 更新设置，以便 OSTICKETWEB VM 能够与 OSTICKETMYSQL 数据库进行通信。 当前，通过本地 IP 地址 172.16.0.43 对配置进行硬编码。

    更新前
    
    ![更新 IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip1.png)  

    更新后
    
    ![更新 IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip2.png) 
    
    ![更新 IP](./media/contoso-migration-rehost-linux-vm-mysql/update-ip3.png) 

3. 通过 systemctl restart apache2 重启该服务。

    ![重新启动](./media/contoso-migration-rehost-linux-vm-mysql/restart.png) 

4. 最后，在某一 Contoso 域控制器上更新 OSTICKETWEB 的 DNS 记录。

    ![更新 DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 


##  <a name="clean-up-after-migration"></a>迁移后清理

迁移完成后，osTicket 应用层在 Azure VM 上运行。

现在，Contoso 需要执行以下操作： 
- 从 vCenter 清单中删除 VMware VM
- 从本地备份作业中删除本地 VM。
- 更新内部文档显示新的位置和 IP 地址。 
- 查看与本地 VM 交互的任何资源，更新任何相关设置或文档以反映新配置。
- Contoso 结合使用 Azure Migrate 服务与依赖项映射来评估用于迁移的 OSTICKETWEB VM。 现在，应从 VM 中删除出于此目的而安装的代理（Microsoft Monitoring Agent/依赖项代理）。

## <a name="review-the-deployment"></a>查看部署

现在，在应用正在运行的情况下，Contoso 需要完全操作和保护其新的基础结构。

### <a name="security"></a>“安全”

Contoso 安全团队审查 VM 和数据库，以确定任何安全问题。

- 他们会审查 VM 的网络安全组 (NSG)，进而控制访问权限。 NSG 用于确保仅允许到应用程序的流量可以通过。
- 考虑使用磁盘加密和 Azure KeyVault 保护 VM 磁盘上的数据。
- SSL 未配置 VM 和数据库实例之间的通信。 他们将需要进行此操作，以确保数据库流量不被攻击。

[详细了解](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) VM 的安全实践。

### <a name="backups"></a>备份

- Contoso 将使用 Azure 备份服务在 VM 上备份数据。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
- 他们无需为数据库配置备份。 Azure Database for MySQL 会自动创建并存储服务器备份。 他们选择为数据库使用异地冗余，因此，该数据库富有弹性且生产就绪。

### <a name="licensing-and-cost-optimization"></a>授权和成本优化

- 部署资源后，Contoso 将根据他们在 [Azure 基础结构](contoso-migration-infrastructure.md#set-up-tagging)部署期间的决定分配 Azure 标记。
- Contoso Ubuntu 服务器无授权问题。
- Contoso 将启用由 Microsoft 子公司 Cloudyn 授权的 Azure 成本管理。 它是一个多云成本管理解决方案，可优化 Azure 和其他云资源的使用和管理。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。


## <a name="next-steps"></a>后续步骤

在此方案中，我们介绍了 Contoso 试用的最后一个重新托管方案。他们将本地 Linux osTicket 应用的前端 VM 迁移到 Azure VM，将应用数据库迁移到 Azure MySQL 实例。

在下一组迁移系列教程中，我们将展示 Contoso 如何执行一组包含应用重构的更复杂的迁移，而不是简单的直接迁移。

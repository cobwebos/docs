---
title: 通过将 Contoso 应用迁移到 Azure VM 和 SQL Server AlwaysOn 可用性组来重新托管它 | Microsoft Docs
description: 了解 Contoso 如何通过将本地应用迁移到 Azure VM 和 SQL Server AlwaysOn 可用性组来重新托管它
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: raynew
ms.openlocfilehash: 2415812a62fc000ddb18318ac52b764c1b5de51b
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008427"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-azure-vms-and-sql-server-alwayson-availability-group"></a>Contoso 迁移：在 Azure VM 和 SQL Server AlwaysOn 可用性组上重新托管本地应用

本文演示了 Contoso 如何在 Azure 中重新托管 SmartHotel 应用。 他们将应用前端 VM 和应用数据库分别迁移到 Azure VM 和 Azure SQL Server VM；其中 Azure SQL Server VM 在具有 SQL Server AlwaysOn 可用性组的 Windows Server 故障转移群集中运行。

本文档是系列文章中的其中一篇，目的是展示虚拟公司 Contoso 如何将本地资源迁移到 Microsoft Azure 云。 该系列介绍了背景信息，同时提供了很多应用场景来描述如何设置迁移基础结构、评估本地资源是否适合迁移以及如何运行不同类型的迁移。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。

**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有迁移文章共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 Contoso 使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
[文章 4：在 Azure VM 和 SQL 托管实例上重新托管应用](contoso-migration-rehost-vm-sql-managed-instance.md) | 演示 Contoso 如何将 SmartHotel 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 SQL 托管实例。 | 可用
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | 展示 Contoso 如何在仅使用 Site Recovery 的情况下迁移 SmartHotel 应用 VM。 | 可用
文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组上重新托管应用 | 展示 Contoso 如何迁移 SmartHotel 应用。 Contoso 使用 Site Recovery 来迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 本文
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 展示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用直接迁移到 Azure VM | 可用
[文章 8：在 Azure VM 和 Azure MySQL 服务器上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，以及如何使用 MySQL 工作台将应用数据库迁移到 Azure MySQL 服务器实例。 | 可用
[文章 9：基于 Azure Web 应用和 Azure SQL 数据库重构应用](contoso-migration-refactor-web-app-sql.md) | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure Web 应用，并将应用数据库迁移到 Azure SQL Server 实例 | 可用
[文章 10：基于 Azure Web 应用和 Azure MySQL 重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | 演示 Contoso 如何将 Linux osTicket 应用迁移到多个站点中的 Azure Web 应用，并与 GitHub 集成以便持续交付。 他们将应用数据库迁移到 Azure MySQL 实例。 | 可用
[文章 11：基于 VSTS 重构 TFS](contoso-migration-tfs-vsts.md) | 演示 Contoso 如何通过将本地 Team Foundation Server (TFS) 部署迁移到 Azure 中的 Visual Studio Team Services (VSTS) 来迁移该部署。 | 可用
[文章 12：在 Azure 容器和 Azure SQL 数据库上重塑应用架构](contoso-migration-rearchitect-container-sql.md) | 演示 Contoso 如何将其 SmartHotel 应用迁移并重新架构到 Azure。 他们将应用 Web 层重新架构为 Windows 容器以及 Azure SQL 数据库中的应用数据库。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | 演示 Contoso 如何使用一系列 Azure 功能和服务（包括应用程序服务、Azure Kubernetes、Azure Functions、认知服务和 Cosmos DB）重新生成 SmartHotel 应用。 | 可用



在本文中，Contoso 将迁移 VMware VM 上运行的双层 Windows. NET SmartHotel 应用迁移到 Azure。 此应用作为开源应用提供，可在 [github](https://github.com/Microsoft/SmartHotel360) 上下载。

## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与其业务合作伙伴密切协作，以了解合作伙伴希望在本次迁移中实现的目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **增加效率**：Contoso 需要摒弃不必要的流程，简化开发人员和用户流程。  业务要求 IT 反应迅速，不浪费时间金钱，从而更快满足客户需求。
- **提高灵活性**：Contoso IT 需要对业务需求更加敏感。 它必须能够抢在市场变化之前作出反应，这样才能在全球经济中取得成功。  同时它不能阻碍发展，成为业务的绊脚石。
- **扩展**：随着业务成功发展，Contoso IT 必须提供能够同步成长的系统。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的目标。 这些目标用于确定最佳迁移方式：

- 迁移后，Azure 中应用的功能应当能够与其当前在 VMWare 中的功能媲美。  无论在云端还是在本地，应用同样重要。
- Contoso 不打算投资此应用。  该用对业务至关重要，但公司目前只希望将其安全地迁移到云端。
- 该应用的本地数据库具有可用性问题。 公司希望在 Azure 中将其部署为一个具有故障转移功能的高可用性群集。
- Contoso 计划从当前的 SQL Server 2008 R2 平台升级到 SQL Server 2017。
- Contoso 不希望此应用使用 Azure SQL 数据库，因此目前正在寻找替代方式。

## <a name="proposed-architecture"></a>建议的体系结构

在本方案中：

- 该应用跨两个 VM（WEBVM 和 SQLVM）进行分层。
- 这两个 VM 位于 VMware ESXi 主机 contosohost1.contoso.com（6.5 版）上
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (**vcenter.contoso.com**) 托管。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。

![方案体系结构](media/contoso-migration-rehost-vm-sql-ag/architecture.png) 

### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[数据库管理服务](https://docs.microsoft.com/azure/dms/dms-overview) | Contoso 将使用 DMS 迁移应用数据层。 DMS 将通过站点到站点 VPN 连接到本地 SQLVM 计算机并迁移 DMS，从而在尽量减少停机时间的情况下实现多个数据库源到 Azure 数据平台的无缝迁移。 | 了解[可使用 DMS 的区域](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)并获取[定价详细信息](https://azure.microsoft.com/pricing/details/database-migration/)。
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Contoso 将使用 Site Recovery 直接迁移应用前端 VM。 Site Recovery 可安排和管理 Azure VM 以及本地 VM 和物理服务器的迁移和灾难恢复操作。  | 在复制到 Azure 的过程中，会产生 Azure 存储费用。  故障转移时会创建 Azure VM，因此会产生费用。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/)费用和定价。

 

## <a name="migration-process"></a>迁移过程

- Contoso 将应用 VM 迁移到 Azure。
- 使用 Site Recovery 将前端 VM 迁移到 Azure VM：
    - 首先，准备和设置 Azure 组件，并准备好本地 VMware 基础结构。
    - 待一切准备就绪后，即可开始复制 VM。
    - 在复制操作启用并运行之后，通过将 VM 故障转移到 Azure 来实现迁移。
- 使用数据迁移服务 (DMS) 将数据库迁移到 Azure 中的 SQL Server 群集。
    - 首先需要在 Azure 中预配 SQL Server VM、设置群集和内部负载均衡器，并配置 AlwaysOn 可用性组。
    - 准备就绪后，即可迁移数据库
- 迁移之后，为数据库启用 AlwaysOn 保护。

![迁移过程](media/contoso-migration-rehost-vm-sql-ag/migration-process.png) 
 
## <a name="prerequisites"></a>先决条件

要运行此方案，你（和 Contoso）需具备以下项：

**要求** | **详细信息**
--- | ---
**Azure 订阅** | 在本系列的第 1 篇文章中执行评估时，应该既已创建订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如需更加细化的权限，请查看[此文](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Azure 基础结构** | [了解](contoso-migration-infrastructure.md) Contoso 如何设置 Azure 基础结构。<br/><br/> 详细了解有关 Site Recovery 的具体[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)要求。
**Site Recovery（本地）** | 本地 vCenter Server 应运行版本 5.5、6.0 或 6.5<br/><br/> 运行版本 5.5、6.0 或 6.5 的 ESXi 主机<br/><br/> ESXi 主机上运行的一个或多个 VMware VM。<br/><br/> VM 必须符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。<br/><br/> 支持的[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)配置。<br/><br/> 要复制的 VM 必须满足 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。
**DMS** | 需在[兼容的本地 VPN 设备](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)上运行 DMS。<br/><br/> 必须能够配置本地 VPN 设备。 该设备必须使用面向外部的公共 IPv4 地址，并且该地址不能位于 NAT 设备的后面。<br/><br/> 确保有权访问本地 SQL Server 数据库。<br/><br/> Windows 防火墙应该能够访问源数据库引擎。 [了解详细信息](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> 如果数据库计算机的前面有防火墙，请添加规则，以允许通过 SMB 端口 445 访问数据库和文件。<br/><br/> 用于连接源 SQL Server 和目标托管实例的凭据必须是 sysadmin 服务器角色的成员。<br/><br/> 需要在本地数据库中创建一个可供 DMS 用来备份源数据库的网络共享。<br/><br/> 确保运行源 SQL Server 实例的服务帐户对该网络共享拥有写入特权。<br/><br/> 记下在该网络共享中拥有完全控制特权的 Windows 用户（和密码）。 Azure 数据库迁移服务模拟这些用户凭据，将备份文件上传到 Azure 存储容器。<br/><br/> SQL Server Express 安装过程默认会将 TCP/IP 协议设置为“已禁用”。 请确保已启用该协议。


## <a name="scenario-steps"></a>方案步骤

Contoso 按如下方式运行迁移：

> [!div class="checklist"]
> * **步骤 1：在 Azure 中创建 SQL Server VM**：Contoso 希望在 Azure 中部署群集数据库来实现高可用性。 公司会部署两个 SQL Server VM 和一个 Azure 内部负载均衡器。
> * **步骤 2：部署群集**：部署 SQL Server VM 之后，准备 Azure SQL Server 群集。  将数据库迁移到预创建的此群集中。
> * **步骤 3：准备 DMS**：为准备 DMS，公司会注册数据库迁移提供程序，并创建项目和 DMS 实例。 设置共享访问签名 (SAS) 统一资源标识符 (URI)。 DMS 使用 SAS URI 来访问服务将 SQL Server 备份文件上传到的存储帐户容器。
> * **步骤 4：为 Site Recovery 准备 Azure**：创建恢复服务保管库以及用于保存复制数据的 Azure 存储帐户。
> * **步骤 5：为 Site Recovery 准备本地 VMware**：准备用于发现 VM 和安装代理的帐户并准备好本地 VM，以便这些 VM 能够在故障转移后连接到 Azure VM。
> * **步骤 6：复制 VM**：配置复制设置并启用 VM 复制。
> * **步骤 7：使用 DMS 迁移数据库**：迁移数据库。
> * **步骤 8：使用 Site Recovery 迁移 VM**：先运行测试故障转移，确保一切正常工作，然后运行完整故障转移来迁移 VM。


## <a name="step-1-prepare-a-sql-server-alwayson-availability-group-cluster"></a>步骤 1：准备 SQL Server AlwaysOn 可用性组群集

Contoso 希望未雨绸缪，在 Azure 的群集中部署数据库， 然后能将此群集用于其他数据库。 

- SQL Server VM 将部署到 ContosoRG 资源组（用于生产资源）中。
- 除了唯一名称，两个 VM 使用的设置相同。
- 内部负载均衡器将部署在 ContosoNetworkingRG（用于网络资源）中。
- VM 将运行具有 SQL Server 2017 企业版的 Windows Server 2016。 Contoso 不具备此操作系统的许可证，因此公司将使用 Azure 市场中提供许可证的映像（费用计入 Azure EA 套餐）。

Contoso 按如下方式设置群集：

1. 通过在 Azure 市场中选择 SQL Server 2017 企业版 Windows Server 2016 映像，创建两个 SQL Server VM。 

    ![SQL VM SKU](media/contoso-migration-rehost-vm-sql-ag/sql-vm-sku.png)

2. 在“创建虚拟机向导” > “基本信息”中，进行如下配置：

    - VM 的名称：SQLAOG1 和 SQLAOG2。
    - 公司的计算机是业务关键型的，因此为 VM 磁盘类型启用 SSD。
    - 指定计算机凭据。
    - 将 VM 部署到美国东部 2 主区的 ContosoRG 资源组。

3. 在“大小”中，对两个 VM 都设置 D2s_V3 SKU 作为最小内存。 稍后可按需扩容。
4. 在“设置”中，执行以下操作：

    - 由于这些 VM 是应用的关键数据库，因此使用托管磁盘。
    - 将计算机置于数据库子网 (PROD-DB-EUS2) 中美国东部 2 主区 (VNET-PROD-EUS2) 的生产网络中。
    - 新建一个可用性组 SQLAOGAVSET，该组具有 2 个默认域和 5 个更新域。

    ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-settings.png)

4. 在“SQL Server 设置”中，仅允许 SQL 连接到默认端口 1433 上虚拟网络（专用网）。 在身份验证方面，使用与现场相同的凭据 (contosoadmin)。

    ![SQL VM](media/contoso-migration-rehost-vm-sql-ag/sql-vm-db.png)

**需要更多帮助？**

- [获取](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision#1-configure-basic-settings) SQL Server VM 预配方面的帮助。
- [了解](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-prereq#create-sql-server-vms)如何为不同的 SQL Server SKU 配置 VM。

## <a name="step-2-deploy-the-failover-cluster"></a>步骤 2：部署故障转移群集

Contoso 按如下方式设置群集：

1. 安装 Azure 存储帐户充当云见证。
2. 将 SQL Server VM 添加到 Contoso 本地数据中心的 Active Directory 域中。
3. 在 Azure 中创建群集。
4. 配置云见证。
5. 最后，启用 AlwaysOn 可用性组。

### <a name="set-up-a-storage-account-as-cloud-witness"></a>设置存储帐户充当云见证

要设置云见证，Contoso 需要一个 Azure 存储帐户来保留用于群集仲裁的 Blob 文件。 可使用同一个存储帐户设置多个群集的云见证。 

Contoso 如下创建存储帐户：

1. 为帐户指定一个可辨别的名称 (contosocloudwitness)。
2. 部署具有 LRS 的常规通用帐户。
3. 将帐户放到第 3 个区域（即美国中南部）中。 将帐户置于主区域和辅助区域之外的位置，保证在出现区域性故障时帐户仍然可用。
4. 将帐户置于保留基础结构资源的资源组 (ContosoInfraRG) 中。

    ![云见证](media/contoso-migration-rehost-vm-sql-ag/witness-storage.png)

5. 在公司创建存储帐户时，为其生成主访问密钥和辅助访问密钥。 创建云见证时需使用主访问密钥。 可依次访问存储帐户名称和“访问密钥”来查看密钥。

    ![访问密钥](media/contoso-migration-rehost-vm-sql-ag/access-key.png)

### <a name="add-sql-server-vms-to-contoso-domain"></a>将 SQL Server VM 添加到 Contoso 域

1. Contoso 向 contoso.com 域添加 SQLAOG1 和 SQLAOG2。
2. 然后，在每个 VM 上安装 Windows 故障转移群集功能和工具。

## <a name="set-up-the-cluster"></a>设置群集

在设置群集之前，Contoso 拍摄了每台计算机上 OS 磁盘的快照。

![快照](media/contoso-migration-rehost-vm-sql-ag/snapshot.png)

2. 然后，运行其汇聚在一起的脚本以创建 Windows 故障转移群集。

    ![创建群集](media/contoso-migration-rehost-vm-sql-ag/create-cluster1.png)

3. 创建群集后，验证 VM 是否显示为群集节点。

     ![创建群集](media/contoso-migration-rehost-vm-sql-ag/create-cluster2.png)

## <a name="configure-the-cloud-witness"></a>配置云见证

1. Contoso 使用故障转移群集管理器中的“仲裁配置向导”配置云见证。
2. 在向导中，选择使用存储帐户创建云见证。
3. 云见证配置完毕后，在故障转移群集管理器快照中显示。

    ![云见证](media/contoso-migration-rehost-vm-sql-ag/cloud-witness.png)
            

## <a name="enable-sql-server-always-on-availability-groups"></a>启用 SQL Server AlwaysOn 可用性组

Contoso 现可启用 AlwaysOn：

1. 在 SQL Server 配置管理器中，为 SQL Server (MSSQLSERVER) 服务启用“AlwaysOn 可用性组”。

    ![启用 AlwaysOn](media/contoso-migration-rehost-vm-sql-ag/enable-alwayson.png)

2. 重启服务后，所作更改才能生效。

启用 AlwaysOn 后，Contoso 可设置 AlwaysOn 可用性组用于保护 SmartHotel 数据库。

**需要更多帮助？**

- [阅读](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)云见证相关信息以及如何为其设置存储帐户。
- [了解](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial)有关群集设置和可用性组创建的说明。

## <a name="step-3-deploy-the-azure-load-balancer"></a>步骤 3：部署 Azure 负载均衡器

Contoso 现希望在群集节点前面部署一个内部负载均衡器。 负载均衡器可侦听流量并将流量指向适当的节点。

![负载均衡](media/contoso-migration-rehost-vm-sql-ag/architecture-lb.png)

公司如下创建负载均衡器：

1. 通过 Azure 门户 >“网络” > “负载均衡器”，设置新的内部负载均衡器 ILB-PROD-DB-EUS2-SQLAOG。
2. 将负载均衡器置于数据库子网 PROD-DB-EUS2 的生产网络 VNET-PROD-EUS2 中。
3. 向其分配一个静态 IP 地址 10.245.40.100。
4. 在网络资源组 ContosoNetworkingRG 中分配负载均衡器作为网络元素。

    ![负载均衡](media/contoso-migration-rehost-vm-sql-ag/lb-create.png)

在部署内部负载均衡器之后，Contoso 需对其进行设置。 Contoso 创建后端地址池、设置运行状况探测器并配置负载均衡规则。

### <a name="add-a-backend-pool"></a>添加后端池

为了将流量分发到群集中的 VM，Contoso 需设置一个包含 NIC IP 地址的后端地址池，其中 NIC 面向将从负载均衡器中接收网络流量的 VM。

1. 在门户的负载均衡器设置中，Contoso 添加了一个后端池 ILB-PROD-DB-EUS-SQLAOG-BEPOOL。
2. 将池与可用性集 SQLAOGAVSET 进行关联。 在池中添加 SQLAOG1 和 SQLAOG2 集。

    ![后端池](media/contoso-migration-rehost-vm-sql-ag/backend-pool.png)

### <a name="create-a-health-probe"></a>创建运行状况探测器

Contoso 创建一个运行状况探测器，以便负载均衡器能够监视应用的运行状况。 探测器基于 VM 对运行状况检查的响应方式，向负载均衡器动态添加 VM 或从中删除 VM。

如下创建探测器： 

1. 在门户的负载均衡器设置中，Contoso 创建一个运行状况探测器 SQLAlwaysOnEndPointProbe。
2. 设置探测器以监视 TCP 端口 59999 上的 VM。
3. 在探测之间设置 5 秒的时间间隔，并将阈值设为 2。 如果两次探测失败，VM 将被视为运行不正常。

    ![探测](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

### <a name="configure-the-load-balancer-to-receive-traffic"></a>配置负载均衡器用于接收流量


Contoso 现需设置负载均衡器规则以定义如何将流量分发到 VM。

- 前端 IP 地址处理传入的流量。
- 后端 IP 池接收流量。

如下创建规则：

1. 在门户的负载均衡器设置中，添加新的负载均衡规则 SQLAlwaysOnEndPointListener。
2. Contoso 设置一个前端侦听器来接收 TCP 1433 上传入的 SQL 客户端流量。
3. 指定要将流量路由到的后端池，同时指定 VM 要在其上侦听流量的端口。
4. Contoso 启用浮动 IP（直接服务器返回）。 SQL AlwaysOn 始终需要此设置。

    ![探测](media/contoso-migration-rehost-vm-sql-ag/nlb-probe.png)

**需要更多帮助？**

- [简要了解](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) Azure 负载均衡器。
- [了解](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-basic-internal-portal)如何创建负载均衡器。



## <a name="step-4-prepare-azure-for-the-site-recovery-service"></a>步骤 4：为 Site Recovery 服务准备 Azure

Contoso 需具备以下 Azure 组件才能部署 Site Recovery：

- VM 在故障转移期间创建时所位于的 VNet。
- 一个 Azure 存储帐户，用于保留复制的数据。 
- Azure 中的恢复服务保管库。

上述项配置如下：

1.  ContosoContoso 已创建可在[部署 Azure 基础结构](contoso-migration-rehost-vm-sql-ag.md)时对 Site Recovery 使用的网络/子网。

    - SmartHotel 应用是一个生产应用，而 WEBVM 将迁移到美国东部 2 主区的 Azure 生产网络 (VNET-PROD-EUS2) 中。
    - WEBVM 将置于 ContosoRG 资源组中，该组位于生成子网 (PROD-FE-EUS2) 中且用于生产资源。

2. Contoso 在主要区域中创建了一个 Azure 存储帐户 (contosovmsacc20180528)。

    - 公司使用具有标准存储空间的常规用途帐户，并使用 LRS 复制。
    - 帐户必须与保管库位于同一个区域中。

    ![Site Recovery 存储](media/contoso-migration-rehost-vm-sql-ag/asr-storage.png)

3. 网络和存储帐户就绪后，公司现可创建一个恢复服务保管库 (ContosoMigrationVault)，并将其置于美国东部 2 主区的 ContosoFailoverRG 资源组中。

    ![恢复服务保管库](media/contoso-migration-rehost-vm-sql-ag/asr-vault.png)

**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)如何为 Site Recovery 设置 Azure。


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>步骤 4：为 Site Recovery 准备本地 VMware

Contoso 在本地准备以下项目：

- vCenter 服务器或 vSphere ESXi 主机上的一个帐户，用于自动发现 VM。
- 一个帐户，用于在要复制的 VMware VM 上自动安装移动服务。
- 本地 VM 设置，以便 Contoso 能够在故障转移后连接到 Azure VM 副本。


### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 
- 安排复制、故障转移和故障回复。
- 至少需要一个只读帐户。 需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

Contoso 按如下所述设置帐户：

1. Contoso 在 vCenter 级别创建一个角色。
2. 然后，Contoso 向该角色分配所需权限。



### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

每个 VM 上都必须安装有移动服务。

- 启用 VM 复制后，Site Recovery 可自动推送安装此组件。
- 需要一个帐户，让 Site Recovery 能够用于访问 VM 进行推送安装。 在 Azure 控制台中设置复制时指定此帐户。
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


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>步骤 5：使用 Site Recovery 将本地 VM 复制到 Azure

Contoso 需要设置和启用复制，然后才可运行到 Azure 的迁移。

### <a name="set-a-replication-goal"></a>设置复制目标

1. 在保管库的保管库名称 (ContosoVMVault) 下，选择复制目标（“入门” > “Site Recovery” > “准备基础结构”）。
2. 指定计算机位于本地、在 VMware 上运行且计划复制到 Azure。

    ![复制目标](./media/contoso-migration-rehost-vm-sql-ag/replication-goal.png)

### <a name="confirm-deployment-planning"></a>确认部署规划

需要选择“是，我已完成”来确认已完成部署计划，然后才能继续操作。 Contoso 在此方案中仅迁移一个 VM，无需部署计划。

### <a name="set-up-the-source-environment"></a>设置源环境

Contoso 需要配置其源环境。 为此，下载 OVF 模板，然后使用它将 Site Recovery 配置服务器部署为可用性高的本地 VMware VM。 设置并运行配置服务器后，在保管库中进行注册。

配置服务器运行多个组件：

- 用于协调本地与 Azure 之间的通信并管理数据复制的配置服务器组件。
- 充当复制网关的进程服务器。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。
- 进程服务器还会将移动服务安装在要复制的 VM 上，并执行针对本地 VMware VM 的自动发现。

Contoso 执行以下步骤：


1. 在保管库中，通过“准备基础结构” > “源” > “配置服务器”下载 OVF 模板。
    
    ![下载 OVF](./media/contoso-migration-rehost-vm-sql-ag/add-cs.png)

2. 将模板导入 VMware 以创建和部署 VM。

    ![OVF 模板](./media/contoso-migration-rehost-vm-sql-ag/vcenter-wizard.png)

3. 首次打开 VM 时，系统将进入 Windows Server 2016 安装体验。 此时接受许可协议并输入管理员密码。
4. 安装完成后，以管理员身份登录到 VM。 首次登录时，系统默认运行 Azure Site Recovery 配置工具。
5. 在此工具中，指定在保管库中注册配置服务器时要使用的名称。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器所要注册到的保管库。

    ![注册配置服务器](./media/contoso-migration-rehost-vm-sql-ag/config-server-register2.png)

7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机之后，配置服务器管理向导会自动启动。
9. 在向导中，选择要接收复制流量的 NIC。 此设置一经配置便无法更改。
10. 选择订阅、资源组以及配置服务器要注册到的保管库。
        ![保管库](./media/contoso-migration-rehost-vm-sql-ag/cswiz1.png) 

10. 然后下载并安装 MySQL 服务器和 VMWare PowerCLI。 
11. 验证后，指定 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 保留默认端口，并指定 vCenter 服务器的友好名称。
12. 指定创建用于自动发现的帐户，同时指定用于自动安装移动服务的凭据。 对于 Windows 计算机，帐户需在 VM 上具备本地管理员特权。

    ![vCenter](./media/contoso-migration-rehost-vm-sql-ag/cswiz2.png)

7. 在 Azure 门户中注册完成后，Contoso 仔细检查保管库的“源”页面上是否列出配置服务器和 VMware 服务器。 发现可能需要 15 分钟或更长时间。 
8. 然后，Site Recovery 通过指定的设置连接到 VMware 服务器并发现 VM。

### <a name="set-up-the-target"></a>设置目标

现在，Contoso 指定目标复制设置。

1. 在“准备基础结构” > “目标”中，选择目标设置。
2. Site Recovery 检查指定目标中是否具有 Azure 存储帐户和网络。

### <a name="create-a-replication-policy"></a>创建复制策略

Contoso 现可创建复制策略。

1. 在“准备基础结构” > “复制设置” > “复制策略” >  “创建和关联”中，创建策略“ContosoMigrationPolicy”。
2. 使用默认设置：
    - **RPO 阈值**：默认为 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
    - **恢复点保留期**。 默认为 24 小时。 此值指定每个恢复点的保留时长。 可以将复制的虚拟机恢复到窗口中的任何点。
    - **应用一致性快照频率**。 默认为 1 小时。 此值指定应用程序一致性快照的创建频率。
 
        ![创建复制策略](./media/contoso-migration-rehost-vm-sql-ag/replication-policy.png)

5. 此策略自动与配置服务器关联。 

    ![关联复制策略](./media/contoso-migration-rehost-vm-sql-ag/replication-policy2.png)



### <a name="enable-replication"></a>启用复制

Contoso 现可开始复制 WebVM。

1. 在“复制应用程序” > “源” > “+复制”中，选择源设置。
2. 公司指出希望启用 VM 并选择 vCenter 服务器和配置服务器。

    ![启用复制](./media/contoso-migration-rehost-vm-sql-ag/enable-replication1.png)

3. 接下来指定目标设置，包括资源组和 VNet，以及复制数据要存储到的存储帐户。

     ![启用复制](./media/contoso-migration-rehost-vm-sql-ag/enable-replication2.png)

3. Contoso 选择 WebVM 进行复制、检查复制策略并启用复制操作。 启用复制操作后，Site Recovery 在 VM 上安装移动服务。
 
    ![启用复制](./media/contoso-migration-rehost-vm-sql-ag/enable-replication3.png)

4. 在“作业”中跟踪复制进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。
5. 在 Azure 门户的“基本信息”中，Contoso 可查看复制到 Azure 的 VM 的结构。

    ![基础结构视图](./media/contoso-migration-rehost-vm-sql-ag/essentials.png)


**需要更多帮助？**

- 有关上述所有步骤的完整演练，可参阅 [针对本地 VMware VM 设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)。
- 还可查看详细说明，了解如何[设置源环境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[配置复制设置](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。
- 有关详细详细，可参阅[启用复制](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)。


## <a name="step-5-install-the-database-migration-assistant-dma"></a>步骤 5：安装数据库迁移助手 (DMA)

Contoso 使用 DMA 将 SmartHotel 数据库迁移到 Azure VM SQLAOG1。 如下设置 DMA：

1. 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=53595)下载工具到本地 SQL Server VM SQLVM。
2. 在 VM 上运行安装程序 (DownloadMigrationAssistant.msi)。
3. 在“完成”页面上，先选择“启动 Microsoft 数据迁移助手”，再完成向导。

## <a name="step-6-migrate-the-database-with-dma"></a>步骤 6：使用 DMA 迁移数据库

1. 在 DMA 中，运行新的迁移——SmartHotel 迁移。
2. 选择“目标服务器类型”作为“Azure 虚拟机上的 SQL Server”。 

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-1.png)

3. 在迁移详细信息中，添加 SQLVM 作为源服务器，并添加 SQLAOG1 作为目标。 为每台计算机指定凭据。

     ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-2.png)

4. 为数据库和配置信息创建本地共享。 SQLVM 和 SQLAOG1 上的 SQL 服务帐户必须能够通过写访问权限访问此共享。

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-3.png)

5. Contoso 选择应迁移的登录信息，然后开始迁移。 完成后，DMA 显示迁移成功。

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-4.png)

6. 验证数据库是否正在 SQLAOG1 上运行。

    ![DMA](media/contoso-migration-rehost-vm-sql-ag/dma-5.png)

DMS 跨 Contoso 数据中心和 Azure 之间的站点到站点 VPN 连接接入本地 SQL Server VM，然后进行数据库迁移。

## <a name="step-7-protect-the-database"></a>步骤 7：保护数据库

应用数据库已在 SQLAOG1 上运行，因此 Contoso 现可使用 AlwaysOn 可用性组保护此数据库。 公司使用 SQL Management Studio 配置 AlwaysOn，然后使用 Windows 群集分配侦听器。 

### <a name="create-an-alwayson-availability-group"></a>创建 AlwaysOn 可用性组

1. 在 SQL Management Studio 中，右键单击“Alwayson 高可用性”以启动[“新建可用性组”向导]。
2. 在“指定选项”中，将可用性组命名为 SHAOG。 在“选择数据库”中，选择 SmartHotel 数据库。

    ![AlwaysOn 可用性组](media/contoso-migration-rehost-vm-sql-ag/aog-1.png)

3. 在“指定副本”中，添加两个 SQL 节点作为可用性副本，并将其配置为通过同步提交提供自动故障转移功能。

     ![AlwaysOn 可用性组](media/contoso-migration-rehost-vm-sql-ag/aog-2.png)

4. 为组 (SHAOG) 和端口配置侦听器。 将内部负载均衡器的 IP 地址添加作为静态 IP 地址 (10.245.40.100)。

    ![AlwaysOn 可用性组](media/contoso-migration-rehost-vm-sql-ag/aog-3.png)

5. 在“选择数据同步”中，启用自动种子设定。 借助此选项，SQL Server 自动为组中的每个数据库创建辅助副本，因此 Contoso 无需手动备份和恢复这些副本。 验证后，创建可用性组。

    ![AlwaysOn 可用性组](media/contoso-migration-rehost-vm-sql-ag/aog-4.png)

6. Contoso 在组创建期间遇到问题。 公司使用的不是 Active Directory Windows 集成安全性，因此需要授予权限允许 SQL 登录，才能创建 Windows 故障转移群集角色。

    ![AlwaysOn 可用性组](media/contoso-migration-rehost-vm-sql-ag/aog-5.png)

6. 组创建之后，Contoso 可在 SQL Management Studio 中查看此组。

### <a name="configure-a-listener-on-the-cluster"></a>在群集上配置侦听器

在 SQL 部署设置的最后，Contoso 配置内部负载均衡器作为群集上的侦听器，并将侦听器联网。 公司使用脚本完成此操作。

![群集侦听器](media/contoso-migration-rehost-vm-sql-ag/cluster-listener.png)


### <a name="verify-the-configuration"></a>验证配置

一切设置就绪后，Contoso 现在 Azure 中具有一个正常工作的可用性组，该组使用迁移后的数据库。 验证方式时连接到 SQL Management Studio 中的内部负载均衡器。

![ILB 连接](media/contoso-migration-rehost-vm-sql-ag/ilb-connect.png)

**需要更多帮助？**
- 了解如何创建[可用性组](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#create-the-availability-group)和[侦听器](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-tutorial#configure-listener)。
- [手动设置群集，使其使用负载均衡器 IP 地址](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener#configure-the-cluster-to-use-the-load-balancer-ip-address)。
- [详细了解](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)如何创建和使用 SAS。


## <a name="step-8-migrate-the-vm-with-site-recovery"></a>步骤 8：使用 Site Recovery 迁移 VM

Contoso 运行快速测试故障转移，然后迁移 VM。

### <a name="run-a-test-failover"></a>运行测试故障转移

迁移之前，可运行测试故障转移，帮助保证一切如期正常工作。 

1. Contoso 运行测试故障转移，将内容移动到最近可用的时间点（“最新处理”）。
2. 选择“在开始故障转移前关闭计算机”，以便 Site Recovery 在触发故障转移之前尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 
3. 运行测试故障转移： 

    - 运行先决条件检查，确保满足迁移所需的所有条件。
    - 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
    - 使用上一步中处理的数据创建 Azure VM。

3. 故障转移完成后，Azure VM 副本将在 Azure 门户中显示。 然后，Contoso 检查 VM 是否大小合适、已连接到正确的网络且正在运行。 
4. 验证后，Contoso 会清理故障转移，记录并保存任何观察结果。 

### <a name="run-a-failover"></a>运行故障转移

1. 验证测试故障转移如期工作中后，Contoso 会创建迁移恢复计划，并在计划中添加 WEBVM。

     ![恢复计划](./media/contoso-migration-rehost-vm-sql-ag/recovery-plan.png)

2. 针对计划运行故障转移。 选择最新恢复点，并指定 Site Recovery 应在触发故障转移前尝试关闭本地 VM。

    ![故障转移](./media/contoso-migration-rehost-vm-sql-ag/failover1.png)

3. 故障转移后，验证 Azure VM 是否按预期显示在 Azure 门户中。

    ![恢复计划](./media/contoso-migration-rehost-vm-sql-ag/failover2.png)

6. 验证 Azure 中是否显示 VM 之后，完成迁移过程、停止 VM 的复制，并停止就 VM 收取 Site Recovery 使用费。

    ![故障转移](./media/contoso-migration-rehost-vm-sql-ag/failover3.png)

### <a name="update-the-connection-string"></a>更新连接字符串

在迁移过程的最后，Contoso 更新应用程序的连接字符串，使其指向 SHAOG 侦听器上运行的迁移后的数据库。 然后，在 Azure 中当前运行的 WEBVM 上更改此配置。  此配置位于 ASP 应用程序的 web.config 中。 

1. 在 C:\inetpub\SmartHotelWeb\web.config 中找到文件。更改服务器的名称，反映出 AOG 的 FQDN：shaog.contoso.com。

    ![故障转移](./media/contoso-migration-rehost-vm-sql-ag/failover4.png)  

2. 更新并保存文件后，在 WEBVM 上重启 IIS。 操作方式是在 cmd 提示符处使用 IISRESET /RESTART。
2. 重启 IIS 后，应用程序现将使用 SQL MI 上运行的数据库。


**需要更多帮助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)如何运行测试故障转移。 
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)如何创建恢复计划。
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)如何故障转移到 Azure。

## <a name="clean-up-after-migration"></a>迁移后清理

迁移之后，SmartHotel 应用在 Azure VM 上运行，且 SmartHotel 数据库位于 Azure SQL 群集中。

接下来，Contoso 需要完成以下清理步骤：  

- 从 vCenter 清单中删除本地 VM。
- 从本地备份作业中删除 VM。
- 更新内部文档，从而显示新位置以及 WEBVM 的 IP 地址。
- 查看与已停用的 VM 交互的所有资源，并更新所有相关设置或文档以反映新配置。
- 添加两个新的 VM（SQLAOG1 和 SQLAOG2）；应将其添加到生产监视系统。

## <a name="review-the-deployment"></a>查看部署

Azure 显示已迁移的资源后，Contoso 需要积极行动、全面保护新的基础结构。

### <a name="security"></a>“安全”

Contoso 安全团队会审查 Azure VMs WEBVM、SQLAOG1 和 SQLAOG2，确定是否存在任何安全问题。 

- 他们会审查 VM 的网络安全组 (NSG)，进而控制访问权限。 NSG 用于确保仅允许到应用程序的流量可以通过。
- 他们正在考虑使用 Azure 磁盘加密服务和 KeyVault 保护磁盘上的数据。
- 需评估透明数据加密 (TDE)，然后在新的 SQL AOG 上运行的 SmartHotel 数据库中启用它。 [了解详细信息](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017)。

[详细了解](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) VM 的安全做法。

### <a name="backups"></a>备份

Contoso 计划使用 Azure 备份服务备份 WEBVM, SQLAOG1 和 SQLAOG2 上的数据。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

1. Contoso 目前拥有 WEBVM 许可证并将享有 Azure 混合权益。  公司将对现有的 Azure VM 进行转换，以利用此定价的优势。
2. Contoso 将启用由 Microsoft 子公司 Cloudyn 授权的 Azure 成本管理。 该服务是一个多云成本管理解决方案，可帮助利用和管理 Azure 与其他云资源。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。 

## <a name="conclusion"></a>结束语

在本文中，Contoso 通过使用 Site Recovery 服务将应用前端 VM 迁移到 Azure，在 Azure 中重新托管了 SmartHotel 应用。 该公司将应用数据库迁移到了 Azure 中预配的 SQL Server 群集，并在 SQL Server AlwaysOn 可用性组中进行保护。

## <a name="next-steps"></a>后续步骤

在本系列的下一篇文章中，将展示 Contoso 如何重新托管 Linux 上运行且部署有 MySQL 数据库的桌面版 osTicket 应用。



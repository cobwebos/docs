---
title: 通过迁移到 Azure VM 和 Azure SQL 数据库托管实例，重新托管 Contoso 本地应用 | Microsoft Docs
description: 了解 Contoso 如何在 Azure VM 上和通过使用 Azure SQL 数据库托管实例重新托管本地应用。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 3e3f8dffbaa7109423aacdbfbaa658bada8bb84a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215333"
---
# <a name="contoso-migration-rehost-an-on-premises-app-on-an-azure-vm-and-sql-database-managed-instance"></a>Contoso 迁移：在 Azure VM 和 SQL 数据库托管实例上重新托管本地应用

在本文中，Contoso 使用 Azure Site Recovery 服务将其 SmartHotel 应用前端 VM 迁移到 Azure VM。 Contoso 还将应用数据库迁移到 Azure SQL 数据库托管实例。

> [!NOTE]
> Azure SQL 数据库托管实例目前以预览版提供。

我们提供一系列的文章展示虚拟公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云中，而本文是该系列中的一篇文章。 该系列介绍了背景信息，同时提供一系列应用场景来描述如何设置迁移基础结构和运行不同类型的迁移。 应用场景越来越复杂。 我们将逐渐添加其他文章进行讲解。


文章 | 详细信息 | 状态
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 概述 Contoso 的迁移策略、文章系列和该系列所用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | Contoso 装备其本地基础结构和 Azure 基础结构进行迁移。 此系列所有迁移文章共用同一个基础结构。 | 可用
[文章 3：评估要迁移到 Azure 的本地资源](contoso-migration-assessment.md) | Contoso 评估 VMware 上运行的本地双层 SmartHotel 应用。 Contoso 使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM。 Contoso 使用[数据迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
文章 4：在 Azure VM 和 SQL 数据库托管实例上重新托管应用 | Contoso 将其本地 SmartHotel 应用直接迁移到 Azure。 Contoso 使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM。 Contoso 使用 [Azure 数据库迁移服务](https://docs.microsoft.com/azure/dms/dms-overview)将应用数据库迁移到 Azure SQL 数据库托管实例。 | 本文
[文章 5：在 Azure VM 上重新托管应用](contoso-migration-rehost-vm.md) | Contoso 使用 Site Recovery 服务将其 SmartHotel 应用 VM 迁移到 Azure VM。 | 可用
[文章 6：在 Azure VM 和 SQL Server AlwaysOn 可用性组中重新托管应用](contoso-migration-rehost-vm-sql-ag.md) | Contoso 迁移 SmartHotel 应用。 Contoso 使用 Site Recovery 迁移应用 VM。 它使用数据库迁移服务将应用数据库迁移到受 AlwaysOn 可用性组保护的 SQL Server 群集。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | Contoso 使用 Site Recovery 将其 Linux osTicket 应用直接迁移到 Azure VM。 | 可用
[文章 8：在 Azure VM 和 Azure Database for MySQL 上重新托管 Linux 应用](contoso-migration-rehost-linux-vm-mysql.md) | Contoso 使用 Site Recovery 将其 Linux osTicket 应用迁移到 Azure VM。 它使用 MySQL Workbench 将应用数据库迁移到 Azure Database for MySQL。 | 可用
[文章 9：在 Azure Web 应用和 Azure SQL 数据库中重构应用](contoso-migration-refactor-web-app-sql.md) | Contoso 将其 SmartHotel 应用迁移到 Azure Web 应用，并将应用数据库迁移到 Azure SQL Server 实例。 | 可用
[文章 10：在 Azure Web 应用和 Azure Database for MySQL 中重构 Linux 应用](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso 将其 Linux osTicket 应用迁移到多个站点中的 Azure Web 应用。 该 Web 应用与 GitHub 集成以便持续交付。 Contoso 将应用数据库迁移到 Azure Database for MySQL 实例。 | 可用
[文章 11：在 Visual Studio Team Services 中重构 Team Foundation Server](contoso-migration-tfs-vsts.md) | Contoso 通过将其本地 Team Foundation Server 部署迁移到 Azure 中的 Visual Studio Team Services 来迁移该部署。 | 可用
[文章 12：在 Azure 容器和 Azure SQL 数据库中重构应用](contoso-migration-rearchitect-container-sql.md) | Contoso 将其 SmartHotel 应用迁移到 Azure，然后重构该应用。 Contoso 将应用 Web 层重构为 Windows 容器，并使用 Azure SQL 数据库重构应用数据库。 | 可用
[文章 13：在 Azure 中重新生成应用](contoso-migration-rebuild.md) | Contoso 使用一系列 Azure 功能和服务（包括 Azure 应用服务、Azure Kubernetes 服务、Azure Functions、Azure 认知服务和 Azure Cosmos DB）重新生成其 SmartHotel 应用。 | 可用

可以从 [GitHub](https://github.com/Microsoft/SmartHotel360) 下载本文所用的示例 SmartHotel 应用。

## <a name="on-premises-architecture"></a>本地体系结构


此图展示了 Contoso 当前的本地基础结构：

![Contoso 当前的体系结构](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso 有一个主要数据中心。 该数据中心位于美国东部的纽约市。
- Contoso 在美国还有 3 家当地分支机构。
- 主要数据中心通过城域光纤以太网连接到 Internet（速度 500 MBps）。
- 每家分支机构均在本地使用业务级连接实现联网，使用 IPsec VPN 隧道连接回主要数据中心。 此设置使得 Contoso 的整个网络永久连接不掉线，同时优化了 Internet 的连接性。
- 主要数据中心通过 VMware 实现了完全的虚拟化处理。 Contoso 有两台 ESXi 6.5 虚拟化主机，由 vCenter Server 6.5 进行托管。
- Contoso 使用 Active Directory 进行标识管理。 Contoso 在内网上使用 DNS 服务器。
- 数据中心的域控制器在 VMware VM 上运行。 而当地分支机构的域控制器在物理服务器上运行。

## <a name="business-drivers"></a>业务驱动因素

Contoso 的 IT 领导团队与公司的业务合作伙伴密切协作，以了解本次迁移要实现的业务目标：

- **应对业务增长**：Contoso 正在发展壮大。 因此，公司的本地系统和基础结构面临的压力也越来越大。
- **提高效率**：Contoso 需要摒弃不必要的流程，简化开发人员和用户流程。 业务要求 IT 反应迅速，不浪费时间或金钱，让公司可以更快满足客户需求。
- **提高灵活性**：Contoso IT 需要对业务需求更加敏感。 它必须能够抢在市场变化之前作出反应，这样才能让公司在全球经济中取得成功。 同时它不能阻碍发展，成为业务的绊脚石。
- **扩展**：随着公司业务成功发展，Contoso IT 部门必须提供能够同步成长的系统。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队已确定本次迁移的目标。 公司使用迁移目标确定最佳迁移方法。

- 迁移后，Azure 中应用的性能应当能够与其当前在 Contoso 本地 VMWare 环境中的性能媲美。 迁移到云并非表示应用性能变得不太重要。
- Contoso 不打算投资该应用。 该应用对于业务至关重要，但是 Contoso 只希望将其当前窗体中的应用移动到云。
- 应用迁移后，数据库管理任务应该会极大弱化。
- Contoso 不希望对此应用使用 Azure SQL 数据库。 正在寻求替代项。

## <a name="proposed-architecture"></a>建议的体系结构

在本方案中：

- Contoso 想要迁移其本地双层旅游应用。
- 该应用跨两个 VM（WEBVM 和 SQLVM）进行分层，并位于 VMware ESXi 6.5 版主机 (contosohost1.contoso.com) 上。 
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (vcenter.contoso.com) 托管。
- Contoso 将应用数据库 (SmartHotelDB) 迁移到 Azure SQL 数据库托管实例上。
- Contoso 将本地 VMware VM 迁移到 Azure VM 上。
- Contoso 有一个本地数据中心 (contoso-datacenter)，和一个本地域控制器 (contosodc1)。
- 迁移完成时，Contoso 数据中心的本地 VM 将停止使用。

![方案体系结构](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure 服务

服务 | Description | 成本
--- | --- | ---
[数据库管理服务](https://docs.microsoft.com/azure/dms/dms-overview) | 使用数据库管理服务可从多个数据库源无缝迁移到 Azure 数据平台，且会尽量缩短停机时间。 | 了解[支持的区域](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)和[数据库管理服务定价](https://azure.microsoft.com/pricing/details/database-migration/)。
[Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | 托管实例是一个托管数据库服务，表示 Azure 云中完全托管的 SQL Server 实例。 它与最新版本的 SQL Server 数据库引擎使用相同的代码，并具有最新的功能、性能改进和安全修补。 | 使用 Azure 中运行的 SQL 数据库托管实例会产生容量相关的费用。 详细了解[托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | Site Recovery 服务可安排和管理 Azure VM 以及本地 VM 和物理服务器的迁移和灾难恢复操作。  | 在复制到 Azure 的过程中，会产生 Azure 存储费用。  故障转移时会创建 Azure VM，因此会产生费用。 详细了解 [Site Recovery 费用和定价](https://azure.microsoft.com/pricing/details/site-recovery/)。

 ## <a name="migration-process"></a>迁移过程

通过完成这些步骤，Contoso 将其 SmartHotel 应用的 Web 和数据层迁移到 Azure：

1. Contoso 已部署有 Azure 基础结构，因此只需为此方案添加几个特定的 Azure 组件即可。
2. 数据层将通过数据迁移服务进行迁移。 数据迁移服务跨 Contoso 数据中心和 Azure 之间的站点到站点 VPN 连接本地 SQL Server VM。 然后，数据迁移服务迁移数据库。
3. Web 层将通过使用 Site Recovery 进行直接迁移。 该进程需要准备本地 VMware 环境、设置和启用复制操作，同时通过将 VM 故障转移到 Azure 实现 VM 迁移。

     ![迁移体系结构](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 

## <a name="prerequisites"></a>先决条件

在本方案中，Contoso 以及其他用户必须满足以下先决条件：

要求 | 详细信息
--- | ---
**注册托管实例预览版** | 必须在 SQL 数据库托管实例的受限公共预览版中注册。 需有一个 Azure 订阅才能[注册](https://portal.azure.com#create/Microsoft.SQLManagedInstance)。 注册需要几天时间才能完成，因此请务必在开始部署本方案之前进行注册。
**Azure 订阅** | 在本系列的第 1 篇文章中执行评估时，应该已创建一个订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是订阅管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如果需要更为细化的权限，请参阅[使用基于角色的访问控制管理 Site Recovery 访问权限](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Site Recovery（本地）** | 本地 vCenter Server 示例应运行版本 5.5、6.0 或 6.5<br/><br/> 运行版本 5.5、6.0 或 6.5 的 ESXi 主机<br/><br/> ESXi 主机上运行的一个或多个 VMware VM。<br/><br/> VM 必须符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。<br/><br/> 支持的[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)配置。
**数据库管理服务** | 对于数据库管理服务，需要[兼容的本地 VPN 设备](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)。<br/><br/> 必须能够配置本地 VPN 设备。 它必须有一个面向外部的公共 IPv4 地址。 该地址不能位于 NAT 设备之后。<br/><br/> 确保有权访问本地 SQL Server 数据库。<br/><br/> Windows 防火墙应该能够访问源数据库引擎。 了解如何[为数据库引擎访问配置 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> 如果数据库计算机的前面有防火墙，请添加规则，以允许通过 SMB 端口 445 访问数据库和文件。<br/><br/> 用于连接源 SQL Server 实例和以托管实例为目标的凭据必须是 sysadmin 服务器角色的成员。<br/><br/> 本地数据库中需要一个可供数据库管理服务用来备份源数据库的网络共享。<br/><br/> 确保运行源 SQL Server 实例的服务帐户对该网络共享拥有写入权限。<br/><br/> 记下在该网络共享中拥有完全控制权限的 Windows 用户和密码。 数据库管理服务模拟这些用户凭据，将备份文件上传到 Azure 存储容器。<br/><br/> SQL Server Express 安装过程默认会将 TCP/IP 协议设置为“已禁用”。 请确保已启用该协议。

## <a name="scenario-steps"></a>方案步骤

Contoso 计划按以下流程设置部署：

> [!div class="checklist"]
> * **步骤 1：设置 SQL 数据库托管实例**：Contoso 需要预创建一个托管实例，以便将本地 SQL Server 数据库迁移到其中。
> * **步骤 2：准备数据库管理服务**：Contoso 必须注册数据库迁移提供程序，创建实例，然后创建一个数据库管理服务项目。 Contoso 还必须为数据库管理服务设置共享访问签名 (SAS) 统一资源标识符 (URI)。 SAS URI 提供对 Contoso 存储帐户中资源的委托访问，因此 Contoso 可以向存储对象授予有限的权限。 Contoso 设置 SAS URI，因此数据库管理服务可以访问该服务上传 SQL Server 备份文件的存储帐户容器。
> * **步骤 3：为 Site Recovery 准备 Azure**：Contoso 必须创建一个存储帐户为 Site Recovery 保存复制数据。 它还必须创建 Azure 恢复服务保管库。
> * **步骤 4：为 Site Recovery 准备本地 VMware**：Contoso 将准备帐户用来发现 VM 和安装代理，以在故障转移后连接 Azure VM。
> * **步骤 5：复制 VM**：为了设置复制，Contoso 配置 Site Recovery 源和目标环境、设置复制策略，并开始将 VM 复制到 Azure 存储。
> * **步骤 6：通过使用数据库管理服务迁移数据库**：Contoso 迁移数据库。
> * **步骤 7：通过使用 Site Recovery 迁移 VM**：Contoso 运行测试故障转移，确保一切正常工作。 然后，Contoso 运行完整故障转移将 VM 迁移到 Azure。

## <a name="step-1-prepare-a-sql-database-managed-instance"></a>步骤 1：准备 SQL 数据库托管实例

若要设置 Azure SQL 数据库托管实例，Contoso 需要一个满足以下要求的子网：

- 子网必须是专用网络。 它必须为空，且不能包含其他任何云服务。 子网不能为网关子网。
- 创建托管实例后，Contoso 不应将资源添加到该子网。
- 该子网不能有关联的网络安全组。
- 子网必须有用户定义的路由 (UDR) 路由表。 分配的唯一路由应为 0.0.0.0/0 下一跃点 Internet。 
- 可选的自定义 DNS：如果在 Azure 虚拟网络中指定了自定义 DNS，则必须将 Azure 的递归解析程序 IP 地址（例如 168.63.129.16）添加到列表。 了解如何[为托管实例配置自定义 DNS](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。
- 该子网不能有关联的服务终结点（存储或 SQL）。 应在虚拟网络上禁用服务终结点。
- 该子网必须至少有 16 个 IP 地址。 了解如何[调整托管实例的子网大小](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances)。
- 在 Contoso 混合环境中，自定义 DNS 设置为必需项。 Contoso 将 DNS 设置配置为使用一个或多个公司 Azure DNS 服务器。 详细了解 [DNS 自定义](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>为托管实例设置虚拟网络

Contoso 按如下所述设置虚拟网络： 

1. Contoso 在主要美国东部 2 区域中创建新的虚拟网络 (VNET-SQLMI-EU2)。 它将虚拟网络添加到 ContosoNetworkingRG 资源组。
2. Contoso 分配 10.235.0.0/24 地址空间。 Contoso 确保范围不与企业中任意其他网络重叠。
2. Contoso 将两个子网添加到网络：
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29)。 此子网用于将目录附加到托管实例。

    ![托管实例 - 创建虚拟网络](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. 部署虚拟网络和子网之后，Contoso 按如下方式进行网络对等互连：

    - 将 VNET-SQLMI-EUS2 与 VNET-HUB-EUS2（美国东部 2 的中心虚拟网络）进行对等互连。
    - 将 VNET-SQLMI-EUS2 与 VNET-PROD-EUS2（生产网络）进行对等互连。

    ![网络对等互连](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso 设置自定义的 DNS 设置。 DNS 先指向 Contoso 的 Azure 域控制器。 Azure DNS 为辅助设置。 Contoso Azure 域控制器位置如下所示：

    - 位于美国东部 2 生产网络 (VNET-PROD-EUS2) 的 PROD-DC-EUS2 子网中
    - CONTOSODC3 地址：10.245.42.4
    - CONTOSODC4 地址：10.245.42.5
    - Azure DNS 解析程序：168.63.129.16

     ![网络 DNS 服务器](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

*需要更多帮助？*

- 简要了解 [SQL 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)。
- 了解如何[为 SQL 数据库托管实例创建虚拟网络](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances)。
- 了解如何[设置对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)。
- 了解如何[更新 Azure Active Directory DNS 设置](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)。

### <a name="set-up-routing"></a>设置路由

托管实例放置在专用虚拟网络中。 Contoso 需要路由表，使得虚拟网络可以与 Azure 管理服务进行通信。 如果虚拟网络不能与管理它的服务进行通信，则无法访问虚拟网络。

Contoso 会考虑以下因素：

- 路由表包含一组规则（路由），它们指定应如何在虚拟网络中路由从托管实例发出的数据包。
- 路由表与部署有托管实例的子网进行关联。 离开子网的每个数据包均根据关联的路由表进行处理。
- 子网仅能与一个路由表相关联。
- 在 Microsoft Azure 中创建路由表不额外产生费用。

 设置路由：

1. Contoso 创建 UDR 表。 Contoso 在 ContosoNetworkingRG 资源组中创建路由表。

    ![路由表](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. 为了满足托管实例要求，在部署路由表 (MIRouteTable) 之后，Contoso 添加具有地址前缀 0.0.0.0/0 的路由。 将“下一个跃点类型”选项设置为“Internet”。

    ![路由表前缀](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso 将路由表与 VNET-SQLMI-EUS2 网络中的 SQLMI-DB-EUS2 子网进行关联。 

    ![路由表子网](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
*需要更多帮助？*

了解如何[为托管实例设置路由](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route)。

### <a name="create-a-managed-instance"></a>创建托管实例

Contoso 现可预配 SQL 数据库托管实例：

1. 因为托管实例为企业应用提供服务，所以 Contoso 在公司的主要美国东部 2 区域中部署托管实例。 Contoso 将托管实例添加到 ContosoRG 资源组。
2. Contoso 为实例选择定价层、大小计算和存储。 详细了解[托管实例定价](https://azure.microsoft.com/pricing/details/sql-database/managed/)。

    ![托管实例](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. 部署托管实例之后，ContosoRG 资源组中出现两个新的资源：

    - 一个虚拟群集，用于应对 Contoso 有多个托管实例的情况。
    - SQL Server 数据库托管实例。 

    ![托管实例](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

*需要更多帮助？*

了解如何[预配托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)。

## <a name="step-2-prepare-the-database-management-service"></a>步骤 2：准备数据库管理服务

若要准备数据库管理服务，Contoso 需要执行一些操作：

- 在 Azure 中注册数据库管理服务提供程序。
- 为数据库管理服务提供对 Azure 存储的访问权限，以便上传用于迁移数据库的备份文件。 为了提供对 Azure 存储的访问权限，Contoso 创建了一个 Azure Blob 存储容器。 Contoso 为 Blob 存储容器生成 SAS URI。 
- 创建数据库管理服务项目。

然后，Contoso 完成以下步骤：

1. Contoso 在其订阅下注册数据库迁移提供程序。
    ![数据库管理服务 - 注册](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. Contoso 创建 Blob 存储容器。 Contoso 生成 SAS URI，以便数据库管理服务可以访问它。

    ![数据库管理服务 - 生成 SAS URI](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. Contoso 创建数据库管理服务实例。 

    ![数据库管理服务 - 创建实例](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso 将数据库管理服务实例置于 VNET-PROD-DC-EUS2 虚拟网络的 PROD-DC-EUS2 子网中。
    - Contoso 将数据库管理服务置于该位置，因为该服务必须位于可以通过 VPN 网关访问本地 SQL Server VM 的虚拟网络中。
    - VNET-PROD-EUS2 与 VNET-HUB-EUS2 对等互连，且可使用远程网关。 “使用远程网关”选项可确保数据库管理服务能够按需进行通信。

        ![数据库管理服务 - 配置网络](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

*需要更多帮助？*

- 了解如何[设置数据库管理服务](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)。
- 了解如何[创建和使用 SAS](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)。


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>步骤 3：为 Site Recovery 服务准备 Azure

Contoso 需要多个 Azure 元素来设置 Site Recovery，以便迁移其 Web 层 VM (WEBMV)：

- 一个虚拟网络，用于放置故障转移后的资源。
- 一个存储帐户，用于保留复制的数据。 
- Azure 中的恢复服务保管库。

Contoso 如下设置 Site Recovery：

1. 由于 VM 是 SmartHotel 应用的 Web 前端，所以 Contoso 将 VM 故障转移到现有的生产网络 (VNET-PROD-EUS2) 和子网 (PROD-FE-EUS2)。 网络和子网位于主要美国东部 2 区域中。 Contoso 在[部署 Azure 基础结构](contoso-migration-infrastructure.md)时设置网络。
2. Contoso 创建存储帐户 (contosovmsacc20180528)。 Contoso 使用通用帐户。 Contoso 选择标准存储和本地冗余存储复制。

    ![Site Recovery - 创建存储帐户](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. 有了网络和存储帐户后，Contoso 创建保管库 (ContosoMigrationVault)。 Contoso 将保管库置于 ContosoFailoverRG 资源组和主要美国东部 2 区域中。

    ![Recovery Services - 创建保管库](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

*需要更多帮助？*

了解如何[为 Site Recovery 设置 Azure](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)。


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>步骤 4：为 Site Recovery 准备本地 VMware

若要为 Site Recovery 准备 VMware，Contoso 必须完成以下任务：

- 在 vCenter Server 实例或 vSphere ESXi 主机上准备一个帐户。 该帐户自动发现 VM。
- 准备一个帐户，它可用于在 Contoso 要复制的 VMware VM 上自动安装 Mobility Service。
- 准备本地 VM，使其可在故障恢复后创建时连接到 Azure VM。


### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 最少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 Contoso 需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

Contoso 通过完成以下任务设置帐户：

1. 在 vCenter 级别创建一个角色。
2. 然后向该角色分配所需权限。

*需要更多帮助？*

了解如何[为自动发现创建和分配角色](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)。

### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装 Mobility Service

必须在 Contoso 想要复制的 VM 上安装 Mobility Service。 Contoso 考虑以下有关 Mobility Service 的因素：

- 如果 Contoso 为 VM 启用了复制，Site Recovery 可以执行此组件的自动推送安装。
- 若要执行自动推送安装，Contoso 必须准备一个由 Site Recovery 用于访问 VM 的帐户。
- Contoso 在 Azure 控制台中设置复制时指定此帐户。
- Contoso 必须有一个在 VM 上具有安装权限的域或本地帐户。

*需要更多帮助*

了解如何[创建一个帐户用于 Mobility Service 的推送安装](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)。

### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

故障转移到 Azure 之后，Contoso 希望能够连接到 Azure 中复制的 VM。 若要连接到 Azure 中的复制 VM，Contoso 必须在迁移前在本地 VM 上完成一些任务： 

1. 为了通过 Internet 进行访问，Contoso 在故障转移之前在本地 VM 上启用 RDP。 Contoso 确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。
2. 为了通过 Contoso 的点对点 VPN 进行访问，Contoso 在本地计算机上启用 RDP。 Contoso 在“Windows 防火墙” > “允许的应用和功能”中允许“域和专用”网络使用 RDP。
3. Contoso 将本地 VM 上操作系统的 SAN 策略设置为“OnlineAll”。

Contoso 还需要在运行故障转移时检查这些项：

- 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果 Windows 更新处于待处理状态，则 Contoso 不能在更新完成前登录到虚拟机。
- 故障转移后，Contoso 应选中“启动诊断”才能查看 VM 的屏幕截图。 如果 Contoso 不能查看启动诊断，则应检查 VM 是否正在运行，然后查看[故障排除提示](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。

## <a name="step-5-replicate-the-on-premises-vms-to-azure-by-using-site-recovery"></a>步骤 5：使用 Site Recovery 将本地 VM 复制到 Azure

在运行到 Azure 迁移之前，Contoso 需要设置复制并为本地 VM 启用复制功能。

### <a name="set-a-replication-goal"></a>设置复制目标

1. 在保管库中，在保管库名称 (ContosoVMVault) 下，Contoso 设置复制目标（“入门” > “Site Recovery” > “准备基础结构”）。
2. Contoso 指定其计算机位于本地、它们是 VMware VM 以及 Contoso 希望复制到 Azure。

    ![准备基础结构 - 保护目标](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>确认部署规划

若要继续，Contoso 需要确认已完成部署规划。 若要确认，Contoso 选择“是的，我已完成”。 在此部署中，Contoso 仅迁移单个 VM，因此它不需要部署规划。

### <a name="set-up-the-source-environment"></a>设置源环境

现在，Contoso 需要配置其源环境。 若要设置其源环境，Contoso 下载 OVF 模板。 Contoso 使用模板将配置服务器及其关联的组件部署为可用性高的本地 VMware VM。 服务器上的组件包括：

- 在本地基础结构与 Azure 之间协调通信的配置服务器。 配置服务器管理数据复制。
- 充当复制网关的进程服务器。 进程服务器：
    - 接收复制数据。
    - 通过使用缓存、压缩和加密来优化复制数据。
    - 将复制数据发送到 Azure 存储。
- 进程服务器还在 Contoso 想要复制的 VM 上安装 Mobility Service。 进程服务器执行本地 VMware VM 的自动发现。
- 创建和启动配置服务器 VM 之后，Contoso 在保管库中注册服务器。

设置源环境：

1. Contoso 从 Azure 门户下载 OVF 模板（“准备基础结构” > “源” > “配置服务器”）。
    
    ![添加配置服务器](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. Contoso 将模板导入 VMware 以创建和部署 VM。

    ![部署 OVF 模板](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  Contoso 首次打开 VM 时，VM 进入 Windows Server 2016 安装体验。 Contoso 接受许可协议并输入管理员密码。
4. 安装完成后，Contoso 以管理员身份登录 VM。 Contoso 首次登录 VM 时，Azure Site Recovery 配置工具自动运行。
5. 在 Site Recovery 配置工具中，Contoso 输入要用来在保管库中注册配置服务器的名称。
6. 此工具检查 VM 到 Azure 的连接。 建立连接后，Contoso 选择“登录”，登录 Azure 订阅。 凭据必须有权访问在其中注册配置服务器的保管库。 

    ![注册配置服务器](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. 该工具将执行一些配置任务，然后重新启动。 Contoso 再次登录计算机。 配置服务器管理向导会自动启动。
8. 在向导中，Contoso 选择要接收复制流量的 NIC。 此设置一经配置便无法更改。
9. Contoso 选择订阅、资源组以及在其中注册配置服务器的服务保管库。

    ![选择恢复服务保管库](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png)

10. Contoso 下载并安装 MySQL Server 和 VMWare PowerCLI。 然后，Contoso 验证服务器设置。
11. 验证后，Contoso 输入 vCenter Server 实例或 vSphere 主机的 FQDN 或 IP 地址。 Contoso 保留默认端口，并在 Azure 中输入 vCenter Server 实例的显示名称。
12. Contoso 必须指定之前创建的帐户，这样 Site Recovery 才能自动发现可用于复制的 VMware VM。 
13. Contoso 输入凭据，以便启用复制时自动安装 Mobility Service。 对于 Windows 计算机，帐户需在 VM 上具备本地管理员权限。 

    ![配置 vCenter Server](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. 注册完成后，在 Azure 门户中，Contoso 再次验证配置服务器和 VMware 服务器是否已在保管库中的“源”页上列出。 发现可能需要 15 分钟或更长时间。 
8. Site Recovery 会使用指定的设置连接 VMware 服务器。 Site Recovery 可发现 VM。

### <a name="set-up-the-target"></a>设置目标

现在，Contoso 需要配置目标复制环境：

1. 在“准备基础结构” > “目标”中，Contoso 选择目标设置。
2. Site Recovery 检查指定目标中是否具有存储帐户和网络。

### <a name="create-a-replication-policy"></a>创建复制策略

源和目标设置完成后，Contoso 创建复制策略，并将其关联到配置服务器：

1. 在“准备基础结构” > “复制设置” > “复制策略” >  “创建和关联”中，Contoso 创建“ContosoMigrationPolicy”策略。
2. Contoso 使用默认设置：
    - **RPO 阈值**：默认为 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
    - **恢复点保留期**：默认值为 24 小时。 此值指定每个恢复点的保留时长。 可以将复制的虚拟机恢复到窗口中的任何点。
    - **应用一致性快照频率**：默认值为 1 小时。 此值指定应用程序一致性快照的创建频率。
 
    ![复制策略 - 创建](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

3. 此策略自动与配置服务器关联。 

    ![复制策略 - 关联](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)

*需要更多帮助？*

- 有关上述步骤的完整演练，可参阅 [针对本地 VMware VM 设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)。
- 还可查看详细说明，了解如何[设置源环境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[配置复制设置](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。

### <a name="enable-replication"></a>启用复制

Contoso 现可开始复制 WebVM。

1. 在“复制应用程序” > “源” > “复制”下，Contoso 选择源设置。
2. Contoso 表示需要启用虚拟机，选择 vCenter Server 实例并设置配置服务器。

    ![启用复制 - 源](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. Contoso 指定目标设置，包括故障转移后 Azure VM 所在的资源组和网络。 Contoso 指定将存储复制数据的存储帐户。

    ![启用复制 - 目标](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso 选择 WebVM 进行复制。 启用复制操作后，Site Recovery 在每个 VM 上安装 Mobility Service。 

    ![启用复制 - 选择 VM](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso 检查是否已选中正确的复制策略。 然后，它启用 WEBVM 复制。 Contoso 跟踪“作业”中的复制进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。
6. 在 Azure 门户的“基本信息”中，Contoso 可查看复制到 Azure 的 VM 的结构：

    ![基础结构视图](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)

*需要更多帮助？*

有关上述步骤的完整演练，可参阅[启用复制](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)。

## <a name="step-6-migrate-the-database-by-using-the-database-management-service"></a>步骤 6：通过使用数据库管理服务迁移数据库

Contoso 需要先创建一个数据库管理服务项目，然后迁移数据库。

### <a name="create-a-database-management-service-project"></a>创建数据库管理服务项目

1. Contoso 创建数据库管理服务项目。 Contoso 选择“SQL Server”源服务器类型。 Contoso 选择“Azure SQL 数据库托管实例”作为目标。

     ![数据库管理服务 - 新迁移项目](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. 迁移向导随即打开。

### <a name="migrate-the-database"></a>迁移数据库 

1. 在迁移向导中，Contoso 指定本地数据库所在的源 VM。 Contoso 输入凭据以访问数据库。

    ![数据库管理服务 - 源详细信息](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. Contoso 选择要迁移的数据库 (SmartHotel.Registration)：

    ![数据库管理服务 - 选择源数据库](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. 对于目标，Contoso 在 Azure 中输入托管实例的名称。 Contoso 输入托管实例的访问凭据。

    ![数据库管理服务 - 目标详细信息](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. 在“新建活动” > “运行迁移”中，Contoso 指定以下设置以运行迁移：
    - 源和目标凭据。
    - 要迁移的数据库。
    - Contoso 在本地 VM 上创建的网络共享。 数据库管理服务将源备份到此共享。 
        - 运行源 SQL Server 实例的服务帐户必须具有此共享的写权限。
        - 必须使用共享的 FQDN 路径。
    - 为数据库管理服务提供存储帐户容器访问权限的 SAS URI，服务将备份文件上载到该容器以进行迁移。

        ![数据库管理服务 - 配置迁移设置](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. Contoso 保存迁移，然后运行它。
6. 在“概述”中，Contoso 监视迁移状态。

    ![数据库管理服务 - 监视](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 完成迁移后，Contoso 验证托管实例上是否存在该目标数据库。

    ![数据库管理服务 - 验证数据库迁移](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-by-using-site-recovery"></a>步骤 7：通过使用 Site Recovery 迁移 VM

Contoso 运行快速测试故障转移，然后迁移 VM。

### <a name="run-a-test-failover"></a>运行测试故障转移

迁移 WEBVM 前，测试故障转移有助于确保一切如预期正常工作。 Contoso 完成以下步骤：

1. Contoso 运行测试故障转移，将内容移动到最近可用的时间点（“最新处理”）。
2. Contoso 选择“在开始故障转移前关闭计算机”。 选中此选项后，Site Recovery 尝试在触发故障转移前关闭源 VM。 即使关机失败，故障转移也仍会继续。 
3. 运行测试故障转移： 
    1. 运行先决条件检查，确保满足迁移所需的所有条件。
    2. 故障转移处理数据，以便创建 Azure VM。 如果选择了最新恢复点，则基于该数据创建恢复点。
    3.  使用上一步中处理的数据创建 Azure VM。
3. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户中。 Contoso 验证一切是否工作正常：VM 大小合适、连接到正确的网络且处于运行状态。 
4. 验证测试故障转移后，Contoso 清理故障转移。 然后，它记录并保存任何观察结果。 

### <a name="migrate-the-vm"></a>迁移 VM

1. 验证测试故障转移如期工作后，Contoso 会创建迁移恢复计划， Contoso 将 WEBVM 添加到计划：

     ![创建恢复计划 - 选择项](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. Contoso 对计划运行故障转移。 Contoso 选择最新的恢复点。 Contoso 指定 Site Recovery 应尝试在触发故障转移前关闭本地 VM。

    ![故障转移](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. 故障转移后，Contoso 验证 Azure VM 按预期显示在 Azure 门户中。

   ![恢复计划详细信息](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. 验证 Azure 中是否显示 VM 之后，Contoso 完成迁移过程、停止 VM 的复制，并停止就 VM 收取 Site Recovery 使用费。

    ![故障转移 - 完成迁移](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>更新连接字符串

在迁移过程的最后，Contoso 更新应用程序的连接字符串，使其指向 Contoso 托管实例上运行的迁移后的数据库。

1. 在 Azure 门户中，Contoso 通过选择“设置” > “连接字符串”查找连接字符串。

    ![连接字符串](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. Contoso 使用 SQL 数据库托管实例的用户名和密码更新字符串。
3. 配置字符串后，Contoso 替换应用程序的 web.config 文件中的当前连接字符串。
4. 更新并保存文件后，Contoso 在 WEBVM 上重启 IIS。 若要重启 IIS，Contoso 在命令提示符窗口中运行 `IISRESET /RESTART`。
5. 重启 IIS 后，应用使用 SQL 数据库托管实例上运行的数据库。
6. 此时，Contoso 可以关闭本地 SQLVM 计算机。 迁移已完成。

*需要更多帮助？*

- 了解如何[运行测试故障转移](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)。 
- 了解如何[创建恢复计划](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)。
- 了解如何[故障转移到 Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)。

## <a name="clean-up-after-migration"></a>迁移后的清理

迁移完成后，SmartHotel 应用在 Azure VM 上运行，且 SmartHotel 数据库在 Azure SQL 数据库托管实例上可用。  

现在，Contoso 需要执行以下清理任务：  

- 从 vCenter Server 清单中删除 WEBVM 计算机。
- 从 vCenter Server 清单中删除 SQLVM 计算机。
- 从本地备份作业中删除 WEBVM 和 SQLVM。
- 更新内部文档，从而显示新位置以及 WEBVM 的 IP 地址。
- 从内部文档删除 SQLVM。 或者，Contoso 可以修改文档，将 SQLVM 显示为已删除且不再显示在 VM 清单中。
- 查看与已解除授权的 VM 进行交互的任何资源。 更新任何相关设置或文档以反映新配置。

## <a name="review-the-deployment"></a>查看部署

Azure 中存在已迁移的资源后，Contoso 需要全面运营并保护其新基础设施。

### <a name="security"></a>“安全”

Contoso 安全团队会审查 Azure VM 和 SQL 数据库托管实例，检查实施方面是否存在任何安全问题：

- 安全团队审查用来控制对 VM 访问的网络安全组。 网络安全组有助于确保只可传递应用允许的流量。
- Contoso 的安全团队还在考虑通过使用 Azure 磁盘加密和 Azure Key Vault 保护磁盘上的数据。
- 安全团队对托管实例启用威胁检测。 如果检测到威胁，威胁检测会向 Contoso 的安全团队/服务台系统发送警报以创建票证。 详细了解[托管实例威胁检测](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)。

     ![托管实例安全 - 威胁检测](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

若要详细了解 VM 的安全做法，请参阅[Azure 中 IaaS 工作负荷的安全性最佳实践](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control)。

### <a name="backups"></a>备份

Contoso 使用 Azure 备份服务来备份 WEBVM 上的数据。 详细了解 [Azure 备份](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

- Contoso 具有 WEBVM 适用的现有许可。 为利用 Azure 混合权益的定价优势，Contoso 转换现有 Azure VM。
- Contoso 启用由 Microsoft 子公司 Cloudyn 许可的 Azure 成本管理。 成本管理是一个多云成本管理解决方案，可帮助 Contoso 使用和管理 Azure 和其他云资源。 详细了解 [Azure 成本管理](https://docs.microsoft.com/azure/cost-management/overview)。 


## <a name="conclusion"></a>结束语

在本文中，Contoso 通过使用 Site Recovery 服务将应用前端 VM 迁移到 Azure，在 Azure 中重新托管 SmartHotel 应用。 Contoso 使用 Azure 数据库管理服务将本地数据库迁移到 Azure SQL 数据库托管实例。

## <a name="next-steps"></a>后续步骤

在本系列的下一篇文章中，Contoso 通过使用 Azure Site Recovery 服务[在 Azure VM 上重新托管 SmartHotel 应用](contoso-migration-rehost-vm.md)。


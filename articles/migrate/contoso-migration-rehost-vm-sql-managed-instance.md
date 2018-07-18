---
title: 通过迁移到 Azure VM 和 Azure SQL 托管实例，重新在 Azure 中托管 Contoso 应用 | Microsoft Docs
description: 了解 Contoso 如何重新托管 Azure VM 和 Azure SQL 托管实例上的本地应用
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/13/2018
ms.author: raynew
ms.openlocfilehash: 99eda135161a228fde139458de30f5120af55153
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723609"
---
# <a name="contoso-migration-rehost-an-on-premises-app-to-azure-vms-and-azure-sql-managed-instance"></a>Contoso 迁移：将本地应用重新托管到 Azure VM 和 Azure SQL 托管实例上

本文介绍 Contoso 如何通过使用 Azure Site Recovery 服务将 SmartHotel 应用前端 VM 迁移到 Azure VM，以及如何将应用数据库迁移到 Azure SQL 托管实例上。

> [!NOTE]
> Azure SQL 托管实例目前处于预览状态。

我们提供一系列的文章展示虚拟公司 Contoso 如何将其本地资源迁移到 Microsoft Azure 云中，而本文档是该系列的第 4 篇。 该系列介绍了背景信息，同时提供一系列应用场景来描述如何设置迁移基础结构和运行不同类型的迁移。 应用场景越来越复杂，我们将逐渐添加其他文章进行讲解。


**文章** | **详细信息** | **Status**
--- | --- | ---
[文章 1：概述](contoso-migration-overview.md) | 简要介绍 Contoso 的迁移策略、文章系列和所使用的示例应用。 | 可用
[文章 2：部署 Azure 基础结构](contoso-migration-infrastructure.md) | 介绍 Contoso 如何装备其本地和 Azure 基础结构进行迁移。 所有的 Contoso 迁移方案共用同一个基础结构。 | 可用
[文章 3：访问本地资源](contoso-migration-assessment.md)  | 展示 Contoso 如何评估 VMware 上运行的本地双层 SmartHotel 应用。 公司使用 [Azure Migrate](migrate-overview.md) 服务评估应用 VM，使用 [Azure 数据库迁移助手](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017)评估应用 SQL Server 数据库。 | 可用
文章 4：重新托管到 Azure VM 和 SQL 托管实例（本文） | 演示 Contoso 如何将 SmartHotel 应用迁移到 Azure。 公司使用 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) 迁移应用前端 VM，同时使用 [Azure 数据库迁移](https://docs.microsoft.com/azure/dms/dms-overview)服务将应用数据库迁移到 SQL 托管实例上。 | 可用
[文章 5：重新托管到 Azure VM](contoso-migration-rehost-vm.md) | 展示 Contoso 如何在仅使用 Site Recovery 的情况下迁移 SmartHotel 应用 VM。
[文章 6：重新托管到 Azure VM 和 SQL Server 可用性组](contoso-migration-rehost-vm-sql-ag.md) | 展示 Contoso 如何迁移 SmartHotel 应用。 公司使用 Site Recovery 迁移应用 VM，同时使用数据库迁移服务将应用数据库迁移到 SQL Server 可用性组。 | 可用
[文章 7：将 Linux 应用重新托管到 Azure VM](contoso-migration-rehost-linux-vm.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM。 | 可用
[文章 8：将 Linux 应用重新托管到 Azure VM 和 Azure MySQL 服务器](contoso-migration-rehost-linux-vm-mysql.md) | 演示 Contoso 如何使用 Site Recovery 将 Linux osTicket 应用迁移到 Azure VM，以及如何使用 MySQL 工作台将其迁移到 Azure MySQL 服务器实例。 | 可用

若要使用本文中所用的 SmartHotel 应用示例，可在 [github](https://github.com/Microsoft/SmartHotel360) 上下载。

## <a name="on-premises-architecture"></a>本地体系结构

下图展示了 Contoso 当前的本地体系结构。

![Contoso 体系结构](./media/contoso-migration-rehost-vm-sql-managed-instance/contoso-architecture.png)  

- Contoso 的一个主要数据中心位于美国东部的纽约市。
- 公司在美国还有 3 家当地分支机构。
- 主要数据中心通过城域光纤以太网连接到 Internet（速度 500 mbps）。
- 每家分支机构均在本地使用业务级连接实现联网，使用 IPSec VPN 隧道连接回主要数据中心。 这使得公司整个网络永久连接不掉线，同时优化了 Internet 的连接性。
- 主要数据中心通过 VMware 实现了完全的虚拟化处理。 公司有两台 ESXi 6.5 虚拟化主机，由 vCenter Server 6.5 进行托管。
- Contoso 使用 Active Directory 管理身份信息，并在内网上使用 DNS 服务器。
- 数据中心的域控制器在 VMware VM 上运行。 而当地分支机构的域控制器在物理服务器上运行。



## <a name="business-drivers"></a>业务驱动因素

IT 领导团队与业务合作伙伴密切协作，以了解本次迁移要实现的业务目标：

- **满足业务增长**：Contoso 在不断壮大，这给本地系统和基础结构造成了压力。
- **增加效率**：Contoso 需要摒弃不必要的流程，简化开发人员和用户流程。  业务要求 IT 反应迅速，不浪费时间金钱，从而更快满足客户需求。
- **提高灵活性**：Contoso IT 需要对业务需求更加敏感。 它必须能够抢在市场变化之前作出反应，这样才能在全球经济中取得成功。  同时它不能阻碍发展，成为业务的绊脚石。
- **扩展**：随着业务成功发展，Contoso IT 必须提供能够同步成长的系统。

## <a name="migration-goals"></a>迁移目标

Contoso 云团队制定了本次迁移的目标。 这些目标用于确定最佳迁移方式：

- 迁移后，Azure 中应用的功能应当能够与其当前在本地 VMWare 环境中的功能媲美。  迁移到云并非表示应用性能变得不太重要。
- Contoso 不打算投资此应用。  该应用对业务至关重要，但公司目前只希望将其原样迁移到云端。
- 应用迁移后，数据库管理任务应该会极大弱化。
- Contoso 不希望此应用使用 Azure SQL 数据库，因此目前正在寻找替代方式。

## <a name="proposed-architecture"></a>建议的体系结构

在本方案中：

- Contoso 想要迁移其本地双层旅游应用。
- 该应用跨两个 VM（WEBVM 和 SQLVM）进行分层，并托管在 VMware ESXi 主机 contosohost1.contoso.com（版本 6.5）上
- VMware 环境由 VM 上运行的 vCenter Server 6.5 (vcenter.contoso.com) 托管。
- 公司将应用数据库 (SmartHotelDB) 迁移到 Azure SQL 托管实例上，
- 并将本地 VMware VM 迁移到 Azure VM 上。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 迁移完成后，Contoso 数据中心的本地 VM 将停止使用。

![方案体系结构](media/contoso-migration-rehost-vm-sql-managed-instance/architecture.png) 

### <a name="azure-services"></a>Azure 服务

**服务** | **说明** | **成本**
--- | --- | ---
[数据库管理服务](https://docs.microsoft.com/azure/dms/dms-overview) | 使用 DMS 可从多个数据库源无缝迁移到 Azure 数据平台，且会尽量缩短停机时间。 | 了解[可使用 DMS 的区域](https://docs.microsoft.com/azure/dms/dms-overview#regional-availability)并获取[定价详细信息](https://azure.microsoft.com/pricing/details/database-migration/)。
[Azure SQL 托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | 托管实例是一个托管数据库服务，表示 Azure 云中完全托管的 SQL Server 实例。 它与最新版本的 SQL Server 数据库引擎共享相同代码，并具有最新的功能、性能改进和安全修补。 | 使用 Azure 中运行的 Azure SQL 数据库托管实例会产生容量相关的费用。 [了解详细信息](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 该服务协调和管理 Azure VM、本地 VM 与物理服务器的迁移和灾难恢复。  | 在复制到 Azure 的过程中，会产生 Azure 存储费用。  故障转移时会创建 Azure VM，因此会产生费用。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/)费用和定价。

 

## <a name="migration-process"></a>迁移过程

Contoso 会将 SmartHotel 应用的 Web 层和数据层都迁移到 Azure 中。

1. Contoso 已部署有 Azure 基础结构，因此只需就此方案添加几个特定的 Azure 组件即可。
2. 数据层将通过数据迁移服务 (DMS) 进行迁移。  DMS 跨 Contoso 数据中心和 Azure 之间的站点到站点 VPN 连接接入本地 SQL Server VM，然后进行数据库迁移。
3. Web 层将通过 Azure Site Recovery 进行直接迁移。 这需要准备本地 VMware 环境、设置和启用复制操作，同时通过 VM 故障转移到 Azure 实现迁移。

     ![迁移体系结构](media/contoso-migration-rehost-vm-sql-managed-instance/migration-architecture.png) 


## <a name="prerequisites"></a>先决条件

在本方案中，Contoso（以及你）需要满足以下条件。

**要求** | **详细信息**
--- | ---
**注册预览版** | 需要在 SQL 托管实例的受限公共预览版中登记。 需有一个 Azure 订阅才能[注册](https://portal.azure.com#create/Microsoft.SQLManagedInstance)。 注册需要几天时间才能完成，因此请一定要在部署本方案之前进行注册。
**Azure 订阅** | 在本系列的第 1 篇文章中执行评估时，应该既已创建订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如需更加细化的权限，请查看[此文](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Site Recovery（本地）** | 本地 vCenter Server 应运行版本 5.5、6.0 或 6.5<br/><br/> 运行版本 5.5、6.0 或 6.5 的 ESXi 主机<br/><br/> ESXi 主机上运行的一个或多个 VMware VM。<br/><br/> VM 必须符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。<br/><br/> 支持的[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)配置。
**DMS** | 需在[兼容的本地 VPN 设备](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)上运行 DMS。<br/><br/> 必须能够配置本地 VPN 设备。 该设备必须使用面向外部的公共 IPv4 地址，并且该地址不能位于 NAT 设备的后面。<br/><br/> 确保有权访问本地 SQL Server 数据库。<br/><br/> Windows 防火墙应该能够访问源数据库引擎。 [了解详细信息](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> 如果数据库计算机的前面有防火墙，请添加规则，以允许通过 SMB 端口 445 访问数据库和文件。<br/><br/> 用于连接源 SQL Server 和目标托管实例的凭据必须是 sysadmin 服务器角色的成员。<br/><br/> 需要在本地数据库中创建一个可供 DMS 用来备份源数据库的网络共享。<br/><br/> 确保运行源 SQL Server 实例的服务帐户对该网络共享拥有写入特权。<br/><br/> 记下在该网络共享中拥有完全控制特权的 Windows 用户（和密码）。 Azure 数据库迁移服务模拟这些用户凭据，将备份文件上传到 Azure 存储容器。<br/><br/> SQL Server Express 安装过程默认会将 TCP/IP 协议设置为“已禁用”。 请确保已启用该协议。


## <a name="scenario-steps"></a>方案步骤

Contoso 计划按以下流程设置部署：

> [!div class="checklist"]
> * **步骤 1：设置 SQL Azure 托管实例**：需要预创建一个托管实例，以便将本地 SQL Server 数据库迁移到其中。
> * **步骤 2：准备 DMS**：需要注册数据库迁移提供程序，再创建实例，然后创建 DMS 项目。 还需要为 DMS 设置 SA URI。 共享访问签名 (SAS) 统一资源标识符 (URI) 提供存储帐户的委托访问权限，让你能够授予对存储对象的受限权限。 公司设置 SAS URI，以便 DMS 可访问服务将 SQL Server 备份文件上传到的存储帐户容器。
> * **步骤 3：为 Site Recovery 准备 Azure**：对于 Site Recovery，必须创建 Azure 存储帐户用于保存复制的数据，还要创建恢复服务保管库。
> * **步骤 4：为 Site Recovery 准备本地 VMware**：Contoso 将准备帐户用来发现 VM 和安装代理，并准备好在故障转移后连接到 Azure VM。
> * **步骤 5：复制 VM**：为了设置复制，公司配置 Site Recovery 源和目标环境、设置复制策略，并开始将 VM 复制到 Azure 存储。
> * **步骤 6：使用 DMS 迁移数据库**：现即可迁移数据库。
> * **步骤 7：使用 Site Recovery 迁移 VM**：运行测试故障转移，确保一切正常工作，然后运行完整故障转移将 VM 迁移到 Azure。


## <a name="step-1-prepare-an-azure-sql-managed-instance"></a>步骤 1：准备 Azure SQL 托管实例

Contoso 需具备子网才能设置 Azure SQL 托管实例：

- 子网必须是专用网络。 子网必须为空、不包含任何其他空，且不得是网关子网。
- 创建托管实例后，不得向其添加资源。
- 该子网不能有关联的 NSG。
- 该子网必须有一个用户路由表 (UDR)，并且向该路由表分配了 0.0.0.0/0 下一跃点 Internet 作为唯一路由。 
- 可选的自定义 DNS：如果在 VNet 中指定了自定义 DNS，则必须将 Azure 的递归解析程序 IP 地址（例如 168.63.129.16）添加到列表。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。
- 该子网不能有关联的服务终结点（存储或 SQL）。 应在虚拟网络上禁用服务终结点。
- 该子网必须至少有 16 个 IP 地址。 [详细了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances)如何调整托管实例子网的大小。
- 需在混合环境中使用自定义的 DNS 设置。 Contoso 将配置 DNS 设置以使用其一个或多个 Azure DNS 服务器。 [详细了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns) DNS 自定义。

### <a name="set-up-a-virtual-network-for-the-managed-instance"></a>为托管实例设置虚拟网络

Contoso 如下设置 VNet： 

1. Contoso 在 ContosoNetworkingRG 资源组中的美国东部 2 主区新建一个 VNet (VNET-SQLMI-EU2)。
2. Contoso 分配一个地址区域 10.235.0.0/24，确保该区域不与 Contoso 企业中任何其他网络重叠。
2. 公司向网络添加两个子网：
    - SQLMI-DS-EUS2 (10.235.0.0.25)
    - SQLMI-SAW-EUS2 (10.235.0.128/29)。 该子网将用于将目录附加到托管实例 (SQLMI)。

    ![托管实例网络](media/contoso-migration-rehost-vm-sql-managed-instance/mi-vnet.png)

6. 部署 VNet 和子网之后，Contoso 按如下方式进行网络对等互连：

    - 将 VNET-SQLMI-EUS2 与 VNET-HUB-EUS2（美国东部 2 区的中心 VNet）进行对等互连。
    - 将 VNET-SQLMI-EUS2 与 VNET-PROD-EUS2（生产网络）进行对等互连。

    ![网络对等互连](media/contoso-migration-rehost-vm-sql-managed-instance/mi-peering.png)

7. Contoso 设置自定义的 DNS 设置。 DNS 将首先指向其 Azure DC。 Azure DNS 将用作辅助系统。 Contoso Azure DC 的位置如下所示：

    - 位于美国东部 2 区生产网络 (VNET-PROD-EUS2) 的 PROD-DC-EUS2 子网中。
    - CONTOSODC3 地址：10.245.42.4
    - CONTOSODC4 地址：10.245.42.5
    - Azure DNS 解析程序：168.63.129.16

     ![网络 DNS](media/contoso-migration-rehost-vm-sql-managed-instance/mi-dns.png)

**需要更多帮助？**

- [简要了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) Azure SQL 托管实例。
- [了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#create-a-new-virtual-network-for-managed-instances)如何创建用于 SQL 托管实例的 VNet。
- [了解](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)如何设置对等互连。
- [了解](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)如何更新 Azure AD DNS 设置。



### <a name="set-up-routing"></a>设置路由

托管实例位于专用 VNET 中，因此 Contoso 需要一个路由表才能与 Azure 管理服务通信。 如果该实例不能与托管它的服务通信，则它不可访问。

- 路由表包含一组规则（路由），它们指定应如何在 VNet 中路由从托管实例发出的数据包。
- 路由表与部署有托管实例的子网进行关联。 每个数据包均根据关联的路由表进行处理。
- 一个子网只能与一个路由表进行关联。
- 在 Microsoft Azure 中创建路由表不额外产生费用。

1. Contoso 创建用户定义的路由表。 此表的创建位置为 ContosoNetworkingRG 资源组。

    ![路由表](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table.png)

2. 为了满足托管实例要求，在部署路由表 (MIRouteTable) 之后，Contoso 向路由添加地址前缀 0.0.0.0/0，并将“下一个跃点类型”设置为“Internet”。

    ![路由表前缀](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-prefix.png)
    
3. Contoso 将路由表与 VNET-SQLMI-EUS2 网络中的 SQLMI-DB-EUS2 子网进行关联。 

    ![路由表子网](media/contoso-migration-rehost-vm-sql-managed-instance/mi-route-table-subnet.png)
    
**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal#create-new-route-table-and-a-route)如何为托管实例设置路由。

### <a name="create-a-managed-instance"></a>创建托管实例

Contoso 现可预配 SQL 数据库托管实例。

1. 由于托管实例用作业务应用，因此 Contoso 将其部署到 ContosoRG 资源组中的美国东部 2 主区。 
2. 选择定价层，以及实例的大小计算和存储。 [详细了解](https://azure.microsoft.com/pricing/details/sql-database/managed/)定价情况。

    ![托管实例](media/contoso-migration-rehost-vm-sql-managed-instance/mi-create.png)

3. 部署托管实例之后，ContosoRG 资源组中出现两个新的资源：

    - 一个虚拟群集，用于应对存在多个托管实例的情况。
    - SQL Server 托管实例。 

    ![托管实例](media/contoso-migration-rehost-vm-sql-managed-instance/mi-resources.png)

**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal)如何预配托管实例。

## <a name="step-2-prepare-dms"></a>步骤 2：准备 DMS

Contoso 需完成以下几项操作才可准备 DMS：

- 在 Azure 中注册 DMS 提供程序
- 向 DMS 提供 Azure 存储的访问权限，让其能够上传用于迁移数据库的备份文件。 操作方式是创建一个 Blob 存储容器，并为其生成一个共享访问签名 (SAS) URI。 
- 创建一个 DMS 项目。


完成下述步骤：

1. Contoso 在其订阅下注册数据库迁移提供程序。
    ![DMS 注册](media/contoso-migration-rehost-vm-sql-managed-instance/dms-subscription.png)

2. 创建一个存储 Blob 容器并生成一个 SAS URI，以便 DMS 可对其进行访问。

    ![SAS URI](media/contoso-migration-rehost-vm-sql-managed-instance/dms-sas.png)

3. 最后，公司创建一个 DMS 实例。 

    ![DMS 实例](media/contoso-migration-rehost-vm-sql-managed-instance/dms-instance.png)

4. Contoso 将 DMS 实例置于 VNET-PROD-DC-EUS2 VNet 的 PROD-DC-EUS2 子网中。
    - 这样做的原因是，该实例必须位于可通过 VPN 网关访问本地 SQL Server VM 的 VNet 中。
    - VNET-PROD-EUS2 与 VNET-HUB-EUS2 对等互连，且可使用远程网关。  这确保 DMS 能够按需进行通信。

        ![添加网络](media/contoso-migration-rehost-vm-sql-managed-instance/dms-network.png)

**需要更多帮助？**
- [了解](https://docs.microsoft.com/azure/dms/quickstart-create-data-migration-service-portal)如何设置 DMS。
- [详细了解](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)如何创建和使用 SAS。


## <a name="step-3-prepare-azure-for-the-site-recovery-service"></a>步骤 3：为 Site Recovery 服务准备 Azure

Contoso 需具备大量 Azure 元素才可设置 Site Recovery 进行 Web 层 VM (WEBMV) 的迁移

- 一个 VNet，用于放置故障转移后的资源。
- 一个 Azure 存储帐户，用于保留复制的数据。 
- Azure 中的恢复服务保管库。

公司如下设置 Site Recovery：

1. 由于 VM 是 SmartHotel 应用的 Web 前端，因此公司将 VM 故障转移到美国东部 2 主区的现有生产网络 (VNET-PROD-EUS2) 和子网 (PROD-FE-EUS2) 中。 公司是在[部署 Azure 基础结构](contoso-migration-infrastructure.md)时设置此网络的
2. 创建 Azure 存储帐户 (contosovmsacc20180528)。 公司使用具有标准存储空间的常规用途帐户，并使用 LRS 复制。

    ![Site Recovery 存储](media/contoso-migration-rehost-vm-sql-managed-instance/asr-storage.png)

3. 网络和存储帐户就绪后，Contoso 现可创建一个保管库 (ContosoMigrationVault)，并将其置于美国东部 2 主区的 ContosoFailoverRG 资源组中。

    ![恢复服务保管库](media/contoso-migration-rehost-vm-sql-managed-instance/asr-vault.png)

**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure)如何为 Site Recovery 设置 Azure。


## <a name="step-4-prepare-on-premises-vmware-for-site-recovery"></a>步骤 4：为 Site Recovery 准备本地 VMware

要为 Site Recovery 准备 VMware，Contoso 需要执行以下操作：

- 在 vCenter 服务器或 vSphere ESXi 主机上准备一个帐户，用于自动发现 VM。
- 准备一个帐户，它可用于在要复制的 VMware VM 上自动安装移动服务。
- 准备本地 VM，使其可在故障恢复后创建时连接到 Azure VM。


### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 至少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

Contoso 按如下所述设置帐户：

1. 在 vCenter 级别创建一个角色。
2. 然后向该角色分配所需权限。

**需要更多帮助？**

[了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery)如何创建和分配用于自动发现的角色。

### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要复制的 VM 上安装移动服务。

- 如果为 VM 启用了复制，Site Recovery 可以执行此组件的自动推送安装。
- 若要执行自动推送安装，需要准备一个由 Site Recovery 用于访问 VM 的帐户。
- 在 Azure 控制台中设置复制时指定此帐户。
- 需要一个在 VM 上拥有安装权限的域或本地帐户。

**需要更多帮助**

[了解](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation)如何创建用于推送安装移动服务安装的帐户。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

故障转移到 Azure 之后，Contoso 希望能够连接到 Azure 中复制的 VM。 为此，需在运行迁移之前在本地 VM 上执行几项操作： 

1. 为了通过 Internet 进行访问，公司在故障转移之前启用本地 VM 上的 RDP，同时确保为“公共”配置文件添加了 TCP 和 UDP 规则，并且“Windows 防火墙” > “允许的应用”中同意所有配置文件使用 RDP。
2. 为了通过站点到站点 VPN 进行访问，公司在本地计算机上启用了 RDP，并且在“Windows 防火墙” -> “允许的应用和功能”中同意域和专用网络使用 RDP。
3. 将本地 VM 上操作系统的 SAN 策略设置为“OnlineAll”。

此外，在运行故障转移时，公司需要检查以下项目：

- 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果存在，则在更新完成之前无法登录到虚拟机。
- 故障转移后，应选中“启动诊断”才能查看 VM 的屏幕截图。 如果此操作无效，应检查确保 VM 正在运行，并查看[疑难解答提示](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。


## <a name="step-5-replicate-the-on-premises-vms-to-azure-with-site-recovery"></a>步骤 5：使用 Site Recovery 将本地 VM 复制到 Azure

在运行到 Azure 迁移之前，Contoso 需要设置复制并为本地 VM 启用复制功能。

### <a name="set-a-replication-goal"></a>设置复制目标

1. 在保管库的保管库名称 (ContosoVMVault) 下，设置复制目标（“入门” > “Site Recovery” > “准备基础结构”）。
2. 指定计算机位于本地、是 VMware VM，且计划复制到 Azure。

    ![复制目标](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-goal.png)

### <a name="confirm-deployment-planning"></a>确认部署规划

需要选择“是，我已完成”来确认已完成部署计划，然后才能继续操作。 在此部署中，Contoso 仅计划迁移一个 VM，因此无需部署计划。

### <a name="set-up-the-source-environment"></a>设置源环境

现需配置源环境。 为此，下载 OVF 模板，然后使用它将配置服务器及其关联的组件部署为可用性高的本地 VMware VM。 服务器上的组件包括：

- 在本地与 Azure 之间协调通信和管理数据复制的配置服务器。
- 充当复制网关的进程服务器。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。
- 进程服务器还会将移动服务安装在要复制的 VM 上，并执行针对本地 VMware VM 的自动发现。
- 创建和启动配置服务器 VM 之后，Contoso 即可在保管库中注册此 VM。


Contoso 执行以下步骤：

1. 从 Azure 门户下载 OVF 模板（“准备基础结构” > “源” > “配置服务器”）。
    
    ![下载 OVF](./media/contoso-migration-rehost-vm-sql-managed-instance/add-cs.png)

2. 将模板导入 VMware 以创建和部署 VM。

    ![OVF 模板](./media/contoso-migration-rehost-vm-sql-managed-instance/vcenter-wizard.png)

3.  首次打开 VM 时，系统将进入 Windows Server 2016 安装体验。 此时接受许可协议并输入管理员密码。
4. 安装完成后，以管理员身份登录到 VM。 首次登录时，系统默认运行 Azure Site Recovery 配置工具。
5. 在此工具中，指定在保管库中注册配置服务器时要使用的名称。
6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器要注册到的保管库。 

    [注册配置服务器](./media/contoso-migration-rehost-vm-sql-managed-instance/config-server-register2.png)

7. 该工具将执行一些配置任务，然后重新启动。 再次登录到计算机之后，配置服务器管理向导会自动启动。
8. 在向导中，选择要接收复制流量的 NIC。 此设置一经配置便无法更改。
9. 选择订阅、资源组以及配置服务器要注册到的保管库。
        ![保管库](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz1.png) 

10. 然后下载并安装 MySQL 服务器和 VMWare PowerCLI。 接着，验证服务器设置。
11. 验证后，指定 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 保留默认端口，并在 Azure 中指定 vCenter 服务器的友好名称。
12. 需要指定之前创建的帐户，这样 Site Recovery 才能自动发现可用于复制的 VMware VM。 
13. 指定用于在启用复制时自动安装移动服务的凭据。 对于 Windows 计算机，帐户需在 VM 上具备本地管理员特权。 

    ![vCenter](./media/contoso-migration-rehost-vm-sql-managed-instance/cswiz2.png)

7. 在 Azure 门户中注册完成后，Contoso 仔细检查保管库的“源”页面上是否列出配置服务器和 VMware 服务器。 发现可能需要 15 分钟或更长时间。 
8. 然后，Site Recovery 通过指定的设置连接到 VMware 服务器并发现 VM。

### <a name="set-up-the-target"></a>设置目标

现在，Contoso 需要配置目标复制环境。

1. 在“准备基础结构” > “目标”中，选择目标设置。
2. Site Recovery 检查指定目标中是否具有 Azure 存储帐户和网络。

### <a name="create-a-replication-policy"></a>创建复制策略

源和目标设置完成后，Contoso 即可创建复制策略，并将其关联到配置服务器。

1. 在“准备基础结构” > “复制设置” > “复制策略” >  “创建和关联”中，创建策略“ContosoMigrationPolicy”。
2. 使用默认设置：
    - **RPO 阈值**：默认为 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
    - **恢复点保留期**。 默认为 24 小时。 此值指定每个恢复点的保留时长。 可以将复制的虚拟机恢复到窗口中的任何点。
    - **应用一致性快照频率**。 默认为 1 小时。 此值指定应用程序一致性快照的创建频率。
 
        ![创建复制策略](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy.png)

5. 此策略自动与配置服务器关联。 

    ![关联复制策略](./media/contoso-migration-rehost-vm-sql-managed-instance/replication-policy2.png)


**需要更多帮助？**

- 有关上述所有步骤的完整演练，可参阅 [针对本地 VMware VM 设置灾难恢复](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial)。
- 还可查看详细说明，了解如何[设置源环境](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source)、[部署配置服务器](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server)以及[配置复制设置](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication)。

### <a name="enable-replication"></a>启用复制

Contoso 现可开始复制 WebVM。

1. 在“复制应用程序” > “源” > “+复制”中，选择源设置。
2. 公司指出希望启用虚拟机并选择 vCenter 服务器和配置服务器。

 ![启用复制](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication1.png)
 
3. 接下来指定目标设置，包括 VM 在故障转移后所属的资源组和网络，以及复制数据要存储到的存储帐户。

     ![启用复制](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication2.png)

4. Contoso 选择 WebVM 进行复制。 为 VM 启用复制后，Site Recovery 会在每个 VM 上安装移动服务。 

    ![启用复制](./media/contoso-migration-rehost-vm-sql-managed-instance/enable-replication3.png)

5. Contoso 检查所选复制策略是否正确并为 WEBVM 启用复制功能。 在“作业”中跟踪复制进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。
6. 在 Azure 门户的“基本信息”中，Contoso 可查看复制到 Azure 的 VM 的结构。

    ![基础结构视图](./media/contoso-migration-rehost-vm-sql-managed-instance/essentials.png)


**需要更多帮助？**

有关上述所有步骤的完整演练，可参阅[启用复制](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication)。

## <a name="step-6-migrate-the-database-with-dms"></a>步骤 6：使用 DMS 迁移数据库

Contoso 需要创建 DMS 项目才可迁移数据库。

### <a name="create-a-dms-project"></a>创建 DMS 项目
1. 创建一个 DMS 项目。 指定 SQL Server 用作源服务器类型，并指定 Azure SQL 数据库托管实例作为目标。

     ![DMS 项目](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-project.png)

2. 创建项目后，迁移向导随即打开。

### <a name="migrate-the-database"></a>迁移数据库 

1. 在迁移向导中，Contoso 会指定本地数据库所在的源 VM 以及用于访问此 VM 的凭据。

    ![DMS 源](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-source.png)

2. 选择要迁移的数据库 (SmartHotel.Registration)。

    ![DMS 源数据库](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-wizard-sourcedb.png)

3. 在 Azure 中指定托管实例的名称作为目标，并访问凭据。

    ![DMS 目标](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-target-details.png)

4. 然后，在“+新建活动” > “运行迁移”中，指定以下设置以运行迁移：
    - 源和目标凭据。
    - 要迁移的数据库。
    - 在本地 VM 上创建的网络共享。 DMS 会将源备份放到此共享中。
        - 运行源 SQL Server 实例的服务帐户必须具有此共享的写权限。
        - 指定此共享的 FQDN 路径。
    - 向 DMS 提供访问权限的 SAS URI，使其能够访问服务因迁移而将备份文件加载到的存储帐户容器。

        ![DMS 设置](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-migration-settings.png)

5. 保存并运行迁移。
6. 在“概述”中监视迁移状态。

    ![DMS 监视](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor1.png)

7. 迁移完成后，检查托管实例上是否存在目标数据库。

    ![DMS 监视](./media/contoso-migration-rehost-vm-sql-managed-instance/dms-monitor2.png)

## <a name="step-7-migrate-the-vm-with-site-recovery"></a>步骤 7：使用 Site Recovery 迁移 VM

Contoso 运行快速测试故障转移，然后迁移 VM。

### <a name="run-a-test-failover"></a>运行测试故障转移

迁移 WEBVM 之前，可运行测试故障转移，保证一切如预期正常工作。 

1. 运行测试故障转移，将内容移动到最近可用的时间点（“最新处理”）。
2. 选择“在开始故障转移前关闭计算机”，以便 Site Recovery 在触发故障转移之前尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 
3. 运行测试故障转移： 

    - 运行先决条件检查，确保满足迁移所需的所有条件。
    - 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
    - 使用上一步中处理的数据创建 Azure VM。
3. 故障转移完成后，Azure VM 副本将在 Azure 门户中显示。 检查确保一切正常运行。 保证 VM 的大小适当、连接到正确的网络且正在运行。 
4. 在验证测试故障转移后，Contoso 会清理故障转移，并记录和保存所有观察结果。 

### <a name="migrate-the-vm"></a>迁移 VM

1. 验证测试故障转移如期工作后，Contoso 会创建迁移恢复计划， 并在计划中添加 WEBVM。

     ![恢复计划](./media/contoso-migration-rehost-vm-sql-managed-instance/recovery-plan.png)

2. 然后，针对计划运行故障转移。 选择最新恢复点，并指定 Site Recovery 应在触发故障转移前尝试关闭本地 VM。

    ![故障转移](./media/contoso-migration-rehost-vm-sql-managed-instance/failover1.png)

3. 故障转移后，Contoso 验证 Azure VM 是否按预期显示在 Azure 门户中。

   ![恢复计划](./media/contoso-migration-rehost-vm-sql-managed-instance/failover2.png)

4. 验证 Azure 中是否显示 VM 之后，完成迁移过程、停止 VM 的复制，并停止就 VM 收取 Site Recovery 使用费。

    ![故障转移](./media/contoso-migration-rehost-vm-sql-managed-instance/failover3.png)

### <a name="update-the-connection-string"></a>更新连接字符串

在迁移过程的最后，Contoso 更新应用程序的连接字符串，使其指向 SQL MI 上运行的迁移后的数据库。

1. 在 Azure 门户中，单击“设置” > “连接字符串”找到连接字符串。

    ![故障转移](./media/contoso-migration-rehost-vm-sql-managed-instance/failover4.png)  

2. 使用 SQL 托管实例的用户名和密码更新字符串。
3. 配置字符串后，替换应用程序的 web.config 文件中的当前连接字符串。
4. 更新并保存文件后，在 WEBVM 上重启 IIS。 操作方式是在 cmd 提示符处使用 IISRESET /RESTART。
5. 重启 IIS 后，应用将使用 SQL 托管实例上运行的数据库。
6. Contoso 此时可关闭本地 SQLVM 计算机完成迁移。

**需要更多帮助？**

- [了解](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure)如何运行测试故障转移。 
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans)如何创建恢复计划。
- [了解](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover)如何故障转移到 Azure。

## <a name="clean-up-after-migration"></a>迁移后清理

迁移完成后，SmartHotel 应用在 Azure VM 上运行，且 SmartHotel 数据库在 Azure SQL 托管实例上可用。  

接下来，Contoso 需要清理一些内容，如下所示：  

- 从 vCenter 清单中删除 WEBVM 计算机。
- 从 vCenter 清单中删除 SQLVM 计算机。
- 从本地备份作业中删除 WEBVM 和 SQLVM。
- 更新内部文档，并显示新位置以及 WEBVM 的 IP 地址。
- 从文档中删除 SQLVM。 或者，可将其标记为“已删除”且不再在 VM 清单中显示。
- 查看与已停用的 VM 交互的所有资源，并更新所有相关设置或文档以反映新配置。

## <a name="review-the-deployment"></a>查看部署

Azure 显示已迁移的资源后，Contoso 需要积极行动、全面保护新的基础结构。

### <a name="security"></a>“安全”

Contoso 安全团队会审查 Azure VM 和 SQL 托管实例，确定实施方面是否存在任何安全问题。

- 他们会审查 VM 的网络安全组 (NSG)，进而控制访问权限。 NSG 用于确保只放行允许到达应用的流量。
- 他们还在考虑使用 Azure 磁盘加密服务和 Azure KeyVault 保护磁盘上的数据。
- 该团队在 SQL 托管实例 (SQLMI) 上启用威胁检测功能。 如果检测到威胁，他们会将警报发送给安全团队/服务台系统以创建票证。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-threat-detection)。

     ![托管实例安全性](./media/contoso-migration-rehost-vm-sql-managed-instance/mi-security.png)  

[详细了解](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) VM 的安全做法。

### <a name="backups"></a>备份
Contoso 计划使用 Azure 备份服务备份 WEBVM 上的数据。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

### <a name="licensing-and-cost-optimization"></a>许可和成本优化

1. Contoso 目前拥有 WEBVM 许可证并将享有 Azure 混合权益。  公司将对现有的 Azure VM 进行转换，以利用此定价的优势。
2. Contoso 将启用由 Microsoft 子公司 Cloudyn 授权的 Azure 成本管理。 它是一个多云成本管理解决方案，可优化 Azure 和其他云资源的使用和管理。  [详细了解](https://docs.microsoft.com/azure/cost-management/overview) Azure 成本管理。 


## <a name="conclusion"></a>结束语

在本文中，Contoso 通过使用 Site Recovery 服务将应用前端 VM 迁移到 Azure，在 Azure 中重新托管了 SmartHotel 应用。 他们使用 DMS 将本地数据库迁移到了 Azure SQL 托管实例。

## <a name="next-steps"></a>后续步骤

在本系列的下一篇文章中，将展示 Contoso 如何在仅使用 Azure Site Recovery 服务的情况下将 SmartHotel 应用重新托管到 Azure VM。


---
title: 使用 Azure DMS 和 Site Recovery 将本地工作负荷迁移到 Azure | Microsoft Docs
description: 了解如何准备 Azure，以使用 Azure 数据库迁移服务和 Azure Site Recovery 服务迁移本地计算机。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: d6fc1af3c5a587ab62b86dcd4189165d96e6e3bc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32182237"
---
# <a name="scenario-2-lift-and-shift-migration-to-azure"></a>方案 2：直接迁移到 Azure

Contoso 公司正在考虑迁移到 Azure。 为尽量做到这一点，他们希望将一个小型的本地旅行应用迁移到 Azure 并对此进行评估。 此应用是一个双层旅行应用，在一个 VM 上运行 Web 应用，在另一个 VM 上运行 SQL Server 数据库。 应用程序部署在 VMware 中，环境由 vCenter Server 托管。 

在[方案 1：评估迁移到 Azure](migrate-scenarios-assessment.md) 中，他们使用了数据迁移助手 (DMA) 来评估应用的 SQL Server 数据库。 此外，他们还使用了 Azure Migrate 服务来评估应用 VM。 在此方案中成功完成评估后，他们想要使用 Azure 数据库迁移服务 (DMS) 将数据库迁移到 Azure SQL 托管实例，并使用 Azure Site Recovery 服务将本地计算机迁移到 Azure VM。

若要尝试执行[方案 1](https://github.com/Microsoft/SmartHotel360)，然后使用此演示旅行应用尝试执行本方案，可以从 [GitHub](migrate-scenarios-assessment.md) 下载该应用。



**服务** | **说明** | **成本**
--- | --- | ---
[数据库管理服务](https://docs.microsoft.com/azure/dms/dms-overview) | 使用 DMS 可从多个数据库源无缝迁移到 Azure 数据平台，且会尽量缩短停机时间。 | 该服务目前以公共预览版提供（截至 2018 年 4 月），在预览期可免费使用。<br/><br/> 请注意，公开预览版 DMS 的[区域数目](https://docs.microsoft.com/azure/dms/dms-overview)有限制。
[Azure SQL 托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) | 托管实例是一个托管数据库服务，表示 Azure 云中完全托管的 SQL Server 实例。 它与最新版本的 SQL Server 数据库引擎共享相同代码，并具有最新的功能、性能改进和安全修补。 | 使用 Azure 中运行的 Azure SQL 数据库托管实例会产生容量相关的费用。 [了解详细信息](https://azure.microsoft.com/pricing/details/sql-database/managed/)。 
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | 该服务协调和管理 Azure VM、本地 VM 与物理服务器的迁移和灾难恢复。  | 在复制到 Azure 的过程中，会产生 Azure 存储费用。  故障转移时会创建 Azure VM，因此会产生费用。 [详细了解](https://azure.microsoft.com/pricing/details/site-recovery/)费用和定价。

在此方案中，我们将设置站点到站点 VPN，使 DMS 能够连接到本地数据库、在 Azure 中创建 Azure SQL 托管实例并迁移数据库。 对于 Site Recovery，我们将准备本地 VMware 环境、设置复制，并将 VM 迁移到 Azure。  


> [!IMPORTANT]
> 需要在 SQL 托管实例的受限公共预览版中登记。 需有一个 Azure 订阅才能[注册](https://portal.azure.com#create/Microsoft.SQLManagedInstance)。 注册可能需要几天才能完成，因此，请确保在开始部署此方案之前注册。

## <a name="architecture"></a>体系结构

### <a name="site-recovery"></a>Site Recovery

![Site Recovery](media/migrate-scenarios-lift-and-shift/asr-architecture.png)  

### <a name="dms"></a>DMS
![DMS](media/migrate-scenarios-lift-and-shift/dms-architecture.png)  

在本方案中：

- Contoso 是表示一家典型企业组织的虚构名称。 Contoso 想要评估和迁移其本地双层旅行应用。
- Contoso 有一个本地数据中心 (contoso-datacenter)，其中包含一个本地域控制器 (**contosodc1**)。
- 内部旅行应用跨两个 VM（WEBVM 和 SQLVM）进行分层，位于 VMware ESXi 主机 (**contosohost1.contoso.com**) 上。
- VMware 环境由在 VM 上运行的 vCenter Server (**vcenter.contoso.com**) 托管。

## <a name="prerequisites"></a>先决条件

若要运行此方案，应做好以下准备。

要求 | 详细信息
--- | ---
**Azure 订阅** | 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。<br/><br/> 如果创建的是免费帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> 如需更加细化的权限，请查看[此文](../site-recovery/site-recovery-role-based-linked-access-control.md)。 
**Site Recovery（本地）** | 运行版本 5.5、6.0 或 6.5 的本地 vCenter 服务器。<br/><br/> 运行版本 5.5、6.0 或 6.5 的 ESXi 主机<br/><br/> ESXi 主机上运行的一个或多个 VMware VM。<br/><br/> VM 必须符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。<br/><br/> 支持的[网络](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network)和[存储](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage)配置。
**DMS** | 需在[兼容的本地 VPN 设备](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)上运行 DMS。<br/><br/> 必须能够配置本地 VPN 设备。 该设备必须使用面向外部的公共 IPv4 地址，并且该地址不能位于 NAT 设备的后面。<br/><br/> 确保有权访问本地 SQL Server 数据库。<br/><br/> Windows 防火墙应该能够访问源数据库引擎。 [了解详细信息](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。<br/><br/> 如果数据库计算机的前面有防火墙，请添加规则，以允许通过 SMB 端口 445 访问数据库和文件。<br/><br/> 用于连接源 SQL Server 和目标托管实例的凭据必须是 sysadmin 服务器角色的成员。<br/><br/> 需要在本地数据库中创建一个可供 DMS 用来备份源数据库的网络共享。<br/><br/> 确保运行源 SQL Server 实例的服务帐户对该网络共享拥有写入特权。<br/><br/> 记下在该网络共享中拥有完全控制特权的 Windows 用户（和密码）。 Azure 数据库迁移服务模拟这些用户凭据，将备份文件上传到 Azure 存储容器。<br/><br/> SQL Server Express 安装过程默认会将 TCP/IP 协议设置为“已禁用”。 请确保已启用该协议。


# <a name="scenario-overview"></a>方案概述

下面是我们将要执行的步骤：

> [!div class="checklist"]
> * **步骤 1：设置站点到站点 VPN 连接**：DMS 通过 VPN 站点到站点连接来与本地数据库建立连接。 我们将为 VPN 连接创建虚拟网络，稍后再将此网络用于 Site Recovery。 创建的网络必须位于在其中创建了恢复服务保管库的区域。
> * **步骤 2：设置 SQL Azure 托管实例**：需要预先创建托管实例，以便将本地 SQL Server 数据库迁移到其中。
> * **步骤 3：设置 DMS 的 SA URI**：共享访问签名 (SAS) 统一资源标识符 (URI) 提供存储帐户中资源的委托访问权限，以便可以授予对存储对象的受限权限。 请设置 SAS URI，以便 DMS 能够访问服务会将 SQL Server 备份文件上传到的存储帐户容器。
> * **步骤 4：准备 DMS**：注册数据库迁移提供程序、创建实例，然后创建 DMS 项目。
> * **步骤 5：为 Site Recovery 准备 Azure**：创建用于保存复制数据的 Azure 存储帐户。
> * **步骤 6：为 Site Recovery 准备本地 VMware**：为 VM 发现和代理安装准备帐户，并准备好在故障转移后连接到 Azure VM。 
> * **步骤 7：复制 VM**：配置 Site Recovery 源和目标环境、设置复制策略，然后开始将 VM 复制到 Azure 存储。
> * **步骤 8：使用 DMS 迁移数据库**：运行数据库迁移。
> * **步骤 9：使用 Site Recovery 迁移 VM**：运行故障转移，将 VM 迁移到 Azure。


## <a name="step-1-set-up-site-to-site-vpn-connection"></a>步骤 1：设置站点到站点 VPN 连接

若要准备 VPN 站点到站点连接，需设置虚拟网络和子网、创建虚拟网络的 VPN 网关，并设置本地网络网关，使 Azure 知道如何连接到本地 VPN。 然后，创建并验证站点到站点连接。

### <a name="set-up-a-virtual-network"></a>设置虚拟网络

创建一个 Azure 虚拟网络，在 DMS 与本地 SQL Server 之间建立站点到站点连接。


1. 在 [Azure 门户](https://portal.azure.com)中，单击“创建资源”。 选择“网络” > “虚拟网络”。
2. 在“虚拟网络” > “选择部署模型”中，选择“资源管理器” > “创建”
3. 在“创建虚拟网络” > “名称”中，输入唯一的网络名称。 本方案使用的名称为 **ContosoVNET**。
4. 在“地址空间”中添加 IP 地址范围。 该范围不能与你的本地地址空间重叠。
5. 在“资源组”中，创建新组或选择现有的组。 本方案使用 **ContosoRG**。
6. 在“位置”中，选择要在其中创建虚拟网络的区域。 本方案使用“美国东部 2”。
7. 在“子网”中，添加第一个子网名称和地址范围。 我们已创建“应用”子网。
8. 禁用服务终结点。

    ![创建虚拟网络](media/migrate-scenarios-lift-and-shift/vnet-create-network.png)

9. 选择“固定到仪表板”以方便今后找到该网络，然后单击“创建”。
10. 创建虚拟网络需要几秒钟的时间。 创建虚拟网络后，请在 Azure 门户仪表板中验证它。
11. 确保在虚拟网络中允许以下通信端口：443、53、9354、445、12000。


### <a name="set-up-subnets"></a>设置子网

我们将在 Azure 网络中设置四个子网：

![子网列表](media/migrate-scenarios-lift-and-shift/vnet-subnet-list.png)

创建网络时已设置第一个子网（“应用”）。 现在，我们创建剩余的子网。 VPN 网关连接使用网关子网，使 Azure 能够通过 VPN 连接将流量发送到本地站点。

1. 在虚拟网络中的“设置”下，单击“子网” > “+子网”。
2. 使用下图所示的地址范围设置子网 **Datamigration**，然后单击“确定”。

    ![数据子网](media/migrate-scenarios-lift-and-shift/vnet-subnet-data.png)  


3. 在“+子网”中，使用下图所示的地址范围设置子网 **Identity**，然后单击“确定”。
    ![Identity 子网](media/migrate-scenarios-lift-and-shift/vnet-subnet-identity.png)

4. 在“子网”中单击“网关子网”，然后单击“确定”。
    - 此子网包含 VPN 网关用于 VPN 连接的 IP 地址。
    - 此子网的名称由系统自动设置，使 Azure 能够识别它。
    - 调整自动填充的地址范围，使其与本地子网匹配。 

    ![网关子网](media/migrate-scenarios-lift-and-shift/vnet-subnet-gateway.png)
    
### <a name="set-up-a-virtual-network-gateway"></a>设置虚拟网络网关

1. 在门户页左侧单击 **+**，并在搜索框中键入“虚拟网关”。 在“结果”中，单击“虚拟网络网关”。
2. 在“虚拟网关”页底部，单击“创建”。
3. 在“创建虚拟网络网关” >  “名称”中，指定网关对象的名称。
4. 在“网关类型”中，选择“VPN”。
5. 在“VPN 类型”中，选择“基于路由”。
6. 在“SKU”中，从下拉列表中选择网关 SKU。 下拉列表中列出的 SKU 取决于选择的 VPN 类型。 不要启用主动-主动模式。 [详细了解](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) SKU。
7. 在“虚拟网络” > “选择虚拟网络”中，添加创建的网络。 网关将添加到此网络。
8. 在“第一个 IP 配置”中指定公共 IP 地址。  VPN 网关必须使用公共 IP 地址。 创建 VPN 网关时，此地址会动态分配到资源。 目前仅支持动态地址，但 IP 地址在分配后不会更改。
    - 单击“创建网关 IP 配置”。 在“选择公共 IP 地址”中，单击“+新建”。
    - 在“创建公共 IP 地址”中，指定公共 IP 地址的名称 (Contoso-Gateway)。 将“SKU”保留为“基本”，然后单击“确定”保存更改。
        
    ![虚拟网络网关](media/migrate-scenarios-lift-and-shift/vnet-create-vpn-gateway2.png) 

9. 单击“创建”开始创建 VPN 网关。 系统会验证这些设置，仪表板会显示“正在部署虚拟网络网关”。

    ![验证虚拟网络网关](media/migrate-scenarios-lift-and-shift/vnet-vpn-gateway-validate.png)
    
创建网关最多可能需要 45 分钟。 可能需要刷新门户页才能看到完成状态。

### <a name="set-up-a-local-network-gateway"></a>设置本地网络网关

在 Azure 中设置用于表示本地站点的本地网络网关。

1. 在门户中，单击“+创建资源”。
2. 在搜索框中键入“本地网关”，然后按 **Enter** 进行搜索。 在结果中，单击“本地网络网关” > “创建”。
3. 在“创建本地网络网关” > “名称”中，指定 Azure 对本地网络网关对象使用的名称。
4. 在“IP 地址”中，指定 Azure 要连接到的本地 VPN 设备的公共 IP 地址。 该 IP 地址不应经过 NAT 处理，并且 Azure 必须能够访问它。
5. 在“地址空间”中，键入要通过 VPN 网关路由到本地设备的本地网络地址范围。 请确保这些范围不与 Azure 虚拟网络上的范围重叠。
6. “配置 BGP 设置”与本方案无关。
7. 在“订阅”中，确认订阅正确。
8. 在“资源组”中，选择用于创建网络的资源组 (ContosoRG)。
9. 在“位置”中，选择在其中创建了虚拟网络的区域。

    ![本地网关](media/migrate-scenarios-lift-and-shift/vnet-create-local-gateway.png)

4. 单击“创建”以创建本地网关。

### <a name="configure-your-on-premises-vpn-device"></a>配置本地 VPN 设备

需要在本地 VPN 设备上配置以下设置：

- 刚刚创建的 Azure 虚拟网络网关的公共 IPv4 地址。
- 预先共享的密钥（创建 VPN 站点到站点连接时使用的密钥）。  

帮助设置本地 VPN 设备：

- 根据所用的本地 VPN 设备，有时可以下载 VPN 设备配置脚本。 [了解详细信息](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-download-vpndevicescript)。
- 查看[更多有用资源](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#VPNDevice)。

### <a name="create-the-vpn-connection"></a>创建 VPN 连接

现在，请在虚拟网络网关与本地 VPN 设备之间创建站点到站点 VPN 连接。

1. 在“虚拟网络” > “概述” > “连接的设备”中打开虚拟网络页。 
2. 单击“连接” > “+添加”。
3. 在“添加连接” > “名称”中，指定连接的名称。
4. 在“连接类型”中，选择“站点到站点(IPSec)”。
5. “虚拟网络网关”值是固定的，因为要从此网关建立连接。
6. 在“本地网络网关”中，指定创建的本地网络网关。
7. 在“共享密钥”中，指定与用于本地 VPN 设备的值匹配的密钥。 此示例使用“abc123”，但可以（而且应该）使用更复杂的。 重要的是，此处指定的值必须与配置 VPN 设备时指定的值相同。
8. “订阅”、“资源组”和“位置”值是固定的。

    ![VPN 连接](media/migrate-scenarios-lift-and-shift/vpn-connection.png) 

4. 单击“确定”以创建连接。 屏幕上会闪烁“正在创建连接”。
5. 创建连接后，在虚拟网络网关的“连接”页上查看该连接。 状态会从“未知”转换为“正在连接”，再转换为“成功”。

### <a name="verify-the-vpn-connection"></a>验证 VPN 连接

在 Azure 门户中，可通过导航到连接来查看 Resource Manager VPN 网关的连接状态。 

1. 单击“所有资源”，并导航到自己的虚拟网络网关。
2. 在虚拟网络网关页中，单击“连接”。 可查看每个连接的状态。
3. 单击连接的名称，在“概要”中查看更多信息。 成功连接后，状态将转换为“成功”和“已连接”。

现在，验证是否能够通过 VPN 连接到 SQL Server VM。 使用远程桌面连接并连接到 VM IP 地址。



## <a name="step-2-prepare-an-azure-sql-managed-instance"></a>步骤 2：准备 Azure SQL 托管实例

### <a name="set-up-a-virtual-network"></a>设置虚拟网络

对于此方案，需要创建另一个专用于 Azure SQL 托管实例的虚拟网络。  请注意以下要求：

- 若要创建该托管实例，需要专用的子网。
- 该子网必须为空，不能包含其他任何云服务，并且不能是网关子网。 创建托管实例后，不得将资源添加到该子网。
- 该子网不能有关联的 NSG。
- 该子网必须有一个用户路由表 (UDR)，并且向该路由表分配了 0.0.0.0/0 下一跃点 Internet 作为唯一路由。 
- 可选的自定义 DNS：如果在 VNet 中指定了自定义 DNS，则必须将 Azure 的递归解析程序 IP 地址（例如 168.63.129.16）添加到列表。 [了解详细信息](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-custom-dns)。
- 该子网不能有关联的服务终结点（存储或 SQL）。 应在虚拟网络上禁用服务终结点。
- 该子网必须至少有 16 个 IP 地址。 [详细了解](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-vnet-configuration#determine-the-size-of-subnet-for-managed-instances)如何调整托管实例子网的大小。

按如下所述设置虚拟网络：

1.  在 Azure 门户中，单击“创建资源”。 
2. 选择“网络” > “虚拟网络”。
3. 在“虚拟网络” > “选择部署模型”中，选择“资源管理器” > “创建”。
4. 在“创建虚拟网络” > “名称”中，输入唯一的网络名称。 本方案使用名称 **ContosoVNETSQLMI**。
5. 在“地址空间”中，添加如下所示的 IP 地址范围。 该范围不能与本地和 ContosoVNET 地址空间重叠。
6. 在“资源组”中，创建新组或选择现有的组。 本方案使用 **ContosoRG**。
7. 在“位置”中，选择要在其中创建虚拟网络的区域。 本方案使用“美国东部 2”。
8. 在“子网”中，添加第一个子网名称和地址范围。 我们已创建 **ManagedInstances** 子网。
9. 禁用服务终结点。

    ![托管实例网络](media/migrate-scenarios-lift-and-shift/network-mi.png)

10. 部署网络后，需要更改 DNS 设置。 在此方案中，DNS 首先使用静态专用 IP 地址 (172.16.3.4) 指向 Identity 子网中的域控制器，然后指向 Azure DNS 解析程序 168.63.129.16。

    ![托管实例网络](media/migrate-scenarios-lift-and-shift/network-mi2.png)


### <a name="set-up-routing"></a>设置路由

1. 在 Azure 门户的左上角单击“创建资源”。 在“路由表”页上单击“路由表” > “创建”。
2. 在“创建路由表” > “名称”中，指定表的名称。
3. 选择订阅、资源组和位置。 保留 BGP 的禁用状态，然后单击“创建”。
    ![路由表](media/migrate-scenarios-lift-and-shift/route-table.png)

4. 创建路由表后，将其打开，然后单击“路由” > “+添加”。
5. 在“添加路由” > “名称”中，指定路由名称。
6. 在“地址前缀”中，指定 0.0.0.0/0。
7. 在“下一跃点类型”中，选择“Internet”。 然后单击“确定”。

     ![路由表](media/migrate-scenarios-lift-and-shift/route-table2.png)


### <a name="apply-the-route-to-the-managed-instance-subnet"></a>将路由应用到托管实例子网

1. 打开创建的虚拟网络。 单击“子网”> 子网名称。
2. 单击“路由表”> 表名称。 然后单击“保存”。

     ![路由表](media/migrate-scenarios-lift-and-shift/route-table3.png)

### <a name="create-a-managed-instance"></a>创建托管实例

1. 单击“创建资源”，找到“托管实例”，然后选择“Azure SQL 数据库托管实例(预览版)”。
2. 单击“创建”。
3. 在“SQL 托管实例”中选择订阅，并确认“预览条款”是否显示“已接受”。
4. 在“托管实例名称”中指定名称。 
5. 指定实例管理员的用户名和密码。
6. 选择实例的资源组、位置和虚拟网络。
7. 单击“定价层”调整计算和存储的大小。 默认情况下，实例会免费获得 32 GB 的存储空间（截至 2018 年 4 月）。
8. 指定存储和虚拟核心数。
9. 单击“应用”保存设置，然后单击“创建”部署托管实例。 若要查看部署状态，请单击“通知”和“正在进行部署”。

    ![托管实例](media/migrate-scenarios-lift-and-shift/mi-1.png)

完成托管实例部署后，ContosoRG 资源组中会显示两个新资源：托管实例的虚拟群集，以及 SQL 托管实例。 两者都位于“美国东部 2”位置。

![托管实例](media/migrate-scenarios-lift-and-shift/managed-instance2.png)


## <a name="step-3-get-a-sas-uri-for-dms"></a>步骤 3：获取 DMS 的 SAS URI

获取 SAS URI，使 DMS 能够访问你的存储帐户容器，以便将用于迁移数据库的备份文件上传到 Azure SQL 数据库托管实例。 

1. 打开存储资源管理器（预览版）。
2. 在左窗格中，展开想要获取其 SAS 的 Blob 容器所在的存储帐户。 展开该存储帐户的“Blob 容器”。
3. 右键单击容器，并选择“获取共享访问签名”。

     ![SAS](media/migrate-scenarios-lift-and-shift/sas-1.png)

4. 在“共享访问签名”中，根据需要为资源指定策略、开始和过期日期、时区以及访问级别。 然后单击“创建”。

    ![SAS](media/migrate-scenarios-lift-and-shift/sas-2.png)

5. 随后会出现另一个对话框，其中显示了可用来访问存储资源的 Blob 容器以及 URL 和 QueryString。 选择“复制”以复制值。 请将这些值保存在安全的位置， 稍后设置 DMS 时需要用到它们。

      ![SAS](media/migrate-scenarios-lift-and-shift/sas-3.png)  

## <a name="step-4-prepare-dms"></a>步骤 4：准备 DMS

注册数据库迁移提供程序并创建实例。

### <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 在订阅中，选择“资源提供程序”。 
2. 搜索“migration”，然后在“Microsoft.DataMigration”的右侧选择“注册”。 


### <a name="create-an-instance"></a>创建实例

1. 选择“+ 创建资源”，搜索“Azure 数据库迁移服务”，然后从下拉列表选择“Azure 数据库迁移服务”。
2. 在“Azure 数据库迁移服务(预览版)”屏幕上，选择“创建”。
3. 指定服务名称、订阅、资源组、虚拟网络和定价层。
4. 选择“创建”来创建服务。

    ![DMS](media/migrate-scenarios-lift-and-shift/dms-instance.png)  




## <a name="step-5-prepare-azure-for-the-site-recovery-service"></a>步骤 5：为 Site Recovery 服务准备 Azure

### <a name="set-up-a-virtual-network"></a>设置虚拟网络

需要一个 Azure 网络，故障转移后创建 Azure VM 时，会将该 VM 放到该网络；另外还需要一个 Azure 存储帐户，复制的数据将存储在其中。

- 本方案使用前面为 DMS 创建的 Azure 网络。
- 请注意，使用的网络必须位于 Site Recovery 保管库所在的同一区域。


### <a name="create-an-azure-storage-account"></a>创建 Azure 存储帐户

Site Recovery 将 VM 数据复制到 Azure 存储。 在从本地故障转移到 Azure 时，会从该存储中创建 Azure VM。

1. 在 [Azure 门户](https://portal.azure.com)菜单中，选择“新建” > “存储” > “存储帐户”。
2. 在“创建存储帐户”中，输入帐户的名称。 对于这些教程，请使用名称 contosovmsacct1910171607。 该名称在 Azure 中必须唯一，长度介于 3-24 个字符，且仅包含数字和小写字母。
3. 在“部署模型”中，选择“资源管理器”。
4. 在“帐户类型”中，选择“常规用途”。 在“性能”中，选择“标准”。 请不要选择 blob 存储。
5. 在“复制”中，选择默认的“读取访问异地冗余存储”作为存储冗余。
6. 在“订阅”中，选择要在其中创建新存储帐户的订阅。
7. 在“资源组”中，输入新的资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 在本系列教程中，请使用名称“ContosoRG”。
8. 在“位置”中，选择存储帐户的地理位置。 存储帐户必须位于与恢复服务保管库相同的区域。 本方案使用“美国东部 2”区域。

   ![创建存储帐户](media/migrate-scenarios-lift-and-shift/create-storageacct.png)

9. 选择“创建”以创建存储帐户。

### <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

1. 在 Azure 门户中，选择“创建资源” > “监视 + 管理” > “备份和站点恢复”。
2. 在“名称”中，输入一个友好名称以标识此保管库。 在本教程中，请使用 ContosoVMVault。
3. 在“资源组”中，选择名为 contosoRG 的现有资源组。
4. 在“位置”中，输入 Azure 区域“美国东部 2”。
5. 若要从仪表板快速访问保管库，请选择“固定到仪表板” > “创建”。
新保管库显示在“仪表板” > “所有资源”中，以及“恢复服务保管库”主页上。

## <a name="step-6-prepare-on-premises-vmware-for-site-recovery"></a>步骤 6：为 Site Recovery 准备本地 VMware

若要为 Site Recovery 准备 VMware，需要执行以下操作：

- 在 vCenter 服务器或 vSphere ESXi 主机上准备一个帐户，以自动化 VM 发现
- 准备一个帐户用于在 VMware VM 上自动安装移动服务
- 若要在故障转移后连接到 Azure VM，请准备好本地 VM。


### <a name="prepare-an-account-for-automatic-discovery"></a>为自动发现准备帐户

Site Recovery 需要访问 VMware 服务器，才能够：

- 自动发现 VM。 至少需要一个只读帐户。
- 安排复制、故障转移和故障回复。 需要一个可以运行诸如创建和删除磁盘、打开 VM 等操作的帐户。

按如下所述创建此帐户：

1. 若要使用专用帐户，请 vCenter 级别创建一个角色。 为该角色指定一个名称，例如 Azure_Site_Recovery。
2. 为该角色分配下表中汇总的权限。
3. 在 vCenter 服务器或 vSphere 主机上创建一个用户。 向该用户分配角色。

**任务** | **角色/权限** | **详细信息**
--- | --- | ---
**VM 发现** | 至少一个只读用户<br/><br/> 数据中心对象 –> 传播到子对象、角色=只读 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。
**完全复制、故障转移、故障回复** |  创建一个拥有所需权限的角色 (Azure_Site_Recovery)，然后将该角色分配到 VMware 用户或组<br/><br/> 数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。

### <a name="prepare-an-account-for-mobility-service-installation"></a>准备一个帐户用于安装移动服务

必须在要复制的 VM 上安装移动服务。

- 如果为 VM 启用了复制，Site Recovery 可以执行此组件的自动推送安装。
- 若要执行自动推送安装，需要准备一个由 Site Recovery 用于访问 VM 的帐户。
- 在 Azure 控制台中设置复制时指定此帐户。
- 需要一个在 VM 上拥有安装权限的域或本地帐户。


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

故障转移到 Azure 后，可能需要通过本地网络连接到 Azure 中复制的 VM。

若要在故障转移后使用 RDP 连接到 Windows VM，请执行以下操作：

1. 若要通过 Internet 访问，请在故障转移之前在本地 VM 上启用 RDP。 请确保为“公共”配置文件添加了 TCP 和 UDP 规则，并确保在“Windows 防火墙” > “允许的应用”中针对所有配置文件允许 RDP。
2. 若要通过站点到站点 VPN 进行访问，请在本地计算机上启用 RDP。 应在“Windows 防火墙” -> “允许的应用和功能”中针对“域和专用”网络允许 RDP。
3. 检查操作系统的 SAN 策略是否已设置为 OnlineAll。 [了解详细信息](https://support.microsoft.com/kb/3031135)。
4. 触发故障转移时，VM 上不应存在待处理的 Windows 更新。 如果存在，则在更新完成之前无法登录到虚拟机。
5. 在 Windows Azure VM 上执行故障转移后，请选中“启动诊断”查看 VM 的屏幕截图。 如果无法连接，请检查 VM 是否正在运行，并查看这些[疑难解答提示](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)。



## <a name="step-7-replicate-vms-with-site-recovery"></a>步骤 7：使用 Site Recovery 复制 VM

### <a name="select-a-replication-goal"></a>选择复制目标

1. 在“恢复服务保管库”中，选择保管库名称“ContosoVMVault”。
2. 在“入门”中，选择“Site Recovery”， 然后选择“准备基础结构”。
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”。
5. 在“你的计算机是否已虚拟化”中，选择“是，带有 VMware vSphere 虚拟机监控程序”。 然后选择“确定”。

    ![OVF 模板](./media/migrate-scenarios-lift-and-shift/replication-goal.png)


### <a name="confirm-deployment-planning"></a>确认部署规划

在“部署规划”中，单击下拉列表中的“是，我已完成”。

### <a name="set-up-the-source-environment"></a>设置源环境


若要设置源环境，需要部署一台高度可用的本地计算机来托管本地 Site Recovery 组件。 组件包括配置服务器和进程服务器。

- 配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- 进程服务器充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将其发送到 Azure 存储。
- 进程服务器还会将移动服务安装在要复制的 VM 上，并执行针对本地 VMware VM 的自动发现。


将配置服务器设置为高可用性 VMware VM：

- 下载已准备好的开放虚拟化格式 (OVF) 模板。
- 将此模板导入 VMware 以创建 VM。
- 设置配置服务器，然后将其注册到保管库中。 

注册后，Site Recovery 可发现本地 VMware VM。



#### <a name="download-the-vm-template"></a>下载 VM 模板

1. 在保管库中，转到“准备基础结构” > “源”。
2. 在“准备源”中，选择“+配置服务器”。

    ![下载模板](./media/migrate-scenarios-lift-and-shift/new-cs.png)

4. 在“添加服务器”中，检查“VMware 的配置服务器”是否已显示在“服务器类型”中。
2. 下载配置服务器的 OVF 模板。

    ![下载 OVF](./media/migrate-scenarios-lift-and-shift/add-cs.png)

  > [!TIP]
  可以直接从 [Microsoft 下载中心](https://aka.ms/asrconfigurationserver)下载最新版本的配置服务器模板。

#### <a name="import-the-template-in-vmware"></a>在 VMware 中导入模板

1. 使用 VMWare vSphere 客户端登录到 VMware vCenter 服务器。
2. 在“文件”菜单中，选择“部署 OVF 模板”以启动“部署 OVF 模板”向导。 

     ![OVF 模板](./media/migrate-scenarios-lift-and-shift/vcenter-wizard.png)

3. 在“选择源”中，输入下载的 OVF 所在的位置。
4. 在“查看详细信息”中，选择“下一步”。
5. 在“选择名称和文件夹”和“选择配置”中，接受默认设置。
6. 在“选择存储”中，为获得最佳性能，请在“选择虚拟磁盘格式”中选择“Thick Provision Eager Zeroed”。
7. 在余下的向导页中，接受默认设置。
8. 在“准备完成”中：

    * 若要使用默认设置来设置 VM，请选择“部署后打开” > “完成”。

    * 若要添加其他网络接口，请清除“部署后打开”， 然后选择“完成”。 默认情况下，配置服务器模板是使用单个 NIC 部署的。 可以在部署后添加其他 NIC。



#### <a name="register-the-configuration-server"></a>注册配置服务器 

1. 通过 VMWare vSphere 客户端控制台打开 VM。
2. VM 将启动并进入 Windows Server 2016 安装体验。 接受许可协议，然后输入管理员密码。
3. 安装完成后，以管理员身份登录到 VM。
4. 首次登录时，会启动 Azure Site Recovery 配置工具。
5. 输入用于向 Site Recovery 注册配置服务器的名称。 然后，选择“下一步”。

    ![OVF 模板](./media/migrate-scenarios-lift-and-shift/config-server-register1.png)

6. 该工具会检查 VM 是否能够连接到 Azure。 建立连接后，选择“登录”以登录到 Azure 订阅。 使用的凭据必须有权访问配置服务器所要注册到的保管库。

    ![OVF 模板](./media/migrate-scenarios-lift-and-shift/config-server-register2.png)

7. 该工具将执行一些配置任务，然后重新启动。
8. 再次登录到计算机。 配置服务器管理向导会自动启动。

#### <a name="configure-settings-and-add-the-vmware-server"></a>配置设置并添加 VMware 服务器

1. 在配置服务器管理向导中选择“设置连接”，然后选择要接收复制流量的 NIC。 再选择“保存”。 配置后无法更改此设置。
2. 在“选择恢复服务保管库”中，选择自己的 Azure 订阅以及相关的资源组和保管库。

    ![保管库](./media/migrate-scenarios-lift-and-shift/cswiz1.png) 

3. 在“安装第三方软件”中，接受许可协议。 选择“下载并安装”，安装 MySQL 服务器。
4. 选择“安装 VMware PowerCLI”。 执行此操作之前，请确保所有浏览器窗口已关闭。 然后选择“继续”。
5. 在“验证设备配置”中验证先决条件，然后继续。
6. 在“配置 vCenter Server/vSphere ESXi 服务器”中，输入要复制的 VM 所在的 vCenter Server 或 vSphere 主机的 FQDN 或 IP 地址。 输入服务器侦听的端口。 为保管库中的 VMware 服务器输入一个可用的友好名称。
7. 输入配置服务器用来连接到 VMware 服务器的凭据。 Site Recovery 将使用这些凭据自动发现可复制的 VMware VM。 选择“添加”，然后选择“继续”。

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

8. 在“配置虚拟机凭据”中，输入用于在计算机上自动安装移动服务的用户名和密码（如果已启用复制）。 对于 Windows 计算机，该帐户在要复制的计算机上需有本地管理员特权。 

    ![vCenter](./media/migrate-scenarios-lift-and-shift/cswiz2.png)

7. 选择“完成配置”以完成注册。 
8. 注册完成后，请在 Azure 门户中确认配置服务器和 VMware 服务器是否已在保管库中的“源”页上列出。 然后，选择“确定”以配置目标设置。
9. Site Recovery 会使用指定的设置连接到 VMware 服务器，并且会发现 VM。

> [!NOTE]
> 帐户名出现在门户中可能需要 15 分钟或更长时间。 若要立即更新，请选择“配置服务器” > ***服务器名称*** > “刷新服务器”。

### <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 在“准备基础结构” > “目标”中，选择要使用的 Azure 订阅。
2. 对于目标部署模型，请选择“Azure 资源管理器”。

3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。


### <a name="create-a-replication-policy"></a>创建复制策略

1. 在“准备基础结构” > “复制设置” > “复制策略”中，单击“创建和关联”。
1. 在“创建复制策略”中，输入策略名称 VMwareRepPolicy。
2. 在“RPO 阈值”中，使用默认值 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
3. 在“恢复点保留期”中，针对每个恢复点的保留时间长度使用默认值 24 小时。 对于本教程，请使用 72 小时。 可以将复制的虚拟机恢复到窗口中的任何点。
4. 在“应用一致快照频率”中，对于创建应用一致快照的频率使用默认值 60 分钟。 选择“确定”以创建策略。

    ![创建复制策略](./media/migrate-scenarios-lift-and-shift/replication-policy.png)

5. 此策略自动与配置服务器关联。 

    ![关联复制策略](./media/migrate-scenarios-lift-and-shift/replication-policy2.png)



### <a name="enable-replication"></a>启用复制

现在开始复制 VM。 为 VM 启用复制后，Site Recovery 会在每个 VM 上安装移动服务。 


1. 选择“复制应用程序” > “源”。
2. 在“源”中选择配置服务器。
3. 在“计算机类型”中，选择“虚拟机”。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。
5. 选择进程服务器（配置服务器）。 然后选择“确定”。

    ![启用复制](./media/migrate-scenarios-lift-and-shift/enable-replication1.png)

1. 在“目标”中，选择要创建故障转移 VM 的订阅和资源组。 选择要在 Azure 中用于故障转移 VM 的部署模型（经典或 Resource Manager）。
2. 选择要用于复制数据的 Azure 存储帐户。
3. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。
4. 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。
5. 选择虚拟网络中的子网。 然后选择“确定”。

    ![启用复制](./media/migrate-scenarios-lift-and-shift/enable-replication2.png)

6. 在“虚拟机” > “选择虚拟机”中，选择所需的 VM (WEBVM)。 只能选择可以启用复制的计算机。 

    ![启用复制](./media/migrate-scenarios-lift-and-shift/enable-replication3.png)

7. 若要监视添加的 VM，请在“配置服务器” > “上次联系时间”中查看上次发现 VM 的时间。 若要添加 VM 而不想要等待计划的发现，请突出显示配置服务器（不要选择它），然后选择“刷新”。 可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。
8. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。 

    ![启用复制](./media/migrate-scenarios-lift-and-shift/enable-replication4.png)

9. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。
10. 选择“启用复制”。

    ![启用复制](./media/migrate-scenarios-lift-and-shift/enable-replication5.png)

11. 在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 
12. 在“完成保护”作业运行之后，计算机可进行故障转移，并会显示在“概述” > “概要”中。

    ![概要](./media/migrate-scenarios-lift-and-shift/essentials.png)


## <a name="step-8-migrate-the-database-with-dms"></a>步骤 8：使用 DMS 迁移数据库

创建一个 DMS 项目，并运行迁移。


### <a name="create-a-database-migration-project"></a>创建数据库迁移项目

1. 在 Azure 门户中找到 DMS 资源并将其打开。
2. 选择“+ 新建迁移项目”。
3. 在“新建迁移项目”中，指定项目的名称。
4. 在“源服务器类型”中选择“SQL Server”。 在“目标服务器类型”中，选择“Azure SQL 数据库托管实例”。
5. 单击“创建”以创建项目。
6. 在“源详细信息”中，指定本地源 SQL Server 的连接详细信息。 单击“ **保存**”。
7. 在“目标详细信息”中，指定目标的连接详细信息，这是本地数据库要迁移到的预配 Azure SQL 数据库托管实例。 然后单击“保存”。
8. 在“项目摘要”中，查看并确认与迁移项目关联的详细信息。

    ![DMS 项目](./media/migrate-scenarios-lift-and-shift/dms-project.png)

### <a name="migrate-the-database"></a>迁移数据库

1. 创建项目后，将显示迁移向导。
2. 指定源和目标服务器的凭据，单击“保存”。 向导会尝试登录到本地 SQL Server。

    ![DMS 向导](./media/migrate-scenarios-lift-and-shift/dms-wiz1.png)

3. 在“映射到目标数据库”中，选择要迁移的源数据库。 然后单击“保存”。

    ![DMS 向导](./media/migrate-scenarios-lift-and-shift/dms-wiz2.png)

4. 在“目标详细信息”中，选择“我知道自己的目标详细信息”。 提供 SQL 托管实例的 DNS 名称以及凭据。 然后单击“保存”。

    ![DMS 向导](./media/migrate-scenarios-lift-and-shift/dms-wiz3.png)

5. 在保存的项目中，选择“+新建活动”。 然后选择“运行迁移”。
6. 出现提示时，输入源服务器和目标服务器的凭据。 然后单击“保存”。
7. 在“映射到目标数据库”中，选择要迁移的源数据库。 然后单击“保存”
8. 在“配置迁移设置” > “服务器备份位置”中，指定在本地计算机上创建的网络共享。 DMS 会将源备份放到此共享中。  请记住，运行源 SQL Server 实例的服务帐户必须在此共享中拥有写入特权。
9. 指定 DMS 在将用于还原的备份文件上传到 Azure 存储容器时可以模拟的用户名和密码。
10. 指定 SAS URI，使 DMS 能够访问用于迁移到 Azure SQL 数据库托管实例的存储帐户容器。该服务会将备份文件上传到该容器。  然后单击“保存”。
11. 在“迁移摘要”中，指定迁移活动的名称，然后单击“运行迁移”。
12. 在项目的“概述”中监视迁移状态。 完成迁移后，检查目标数据库是否显示在托管实例上。


## <a name="step-9-migrate-vms-with-site-recovery"></a>步骤 9：使用 Site Recovery 迁移 VM

我们建议在运行完整迁移之前先运行测试故障转移，确保一切符合预期。 运行测试故障转移时需执行下列操作：

1. 运行先决条件检查，确保满足迁移所需的所有条件。
2. 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
3. 使用上一步中处理的数据创建 Azure VM。

### <a name="run-a-test-failover"></a>运行测试故障转移

1. 在“受保护的项”中，单击“复制的项”>“虚拟机”。
2. 在 VM 属性中，单击“+测试故障转移”。

    ![测试故障转移](./media/migrate-scenarios-lift-and-shift/test-failover.png)

3. 选择“最新处理”，将 VM 故障转移到最近的可用时间点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低。
2. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的目标 Azure 网络。
3. 单击“确定”开始故障转移。 可以在保管库 >“设置” > “作业” > “测试故障转移”中跟踪进度。
4. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保虚拟机的大小适当、已连接到正确的网络，并且正在运行。 现在应该能够连接到 Azure 中复制的虚拟机。
5. 若要删除在测试故障转移期间创建的 Azure VM，请在 VM 上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。

### <a name="migrate-the-machines"></a>迁移计算机

确认测试故障转移符合预期后，创建一个用于迁移到 Azure 的恢复计划。

### <a name="create-a-recovery-plan"></a>创建恢复计划

1. 在保管库中，选择“恢复计划 (Site Recovery)” > “+恢复计划”。
2. 在“创建恢复计划”中，为计划指定一个名称。
3. 选择源配置服务器，然后选择“Azure”作为目标。 选择“资源管理器”作为部署模型。 源位置必须具有已针对故障转移和恢复启用的计算机。 
4. 在“选择项”中，将计算机 (WEBVM) 添加到计划。 然后单击“确定”。
5. 单击“确定”以创建计划。

    ![恢复计划](./media/migrate-scenarios-lift-and-shift/recovery-plan1.png)

### <a name="run-a-failover"></a>运行故障转移

1. 在“恢复计划”中，右键单击该计划 >“故障转移”。
2. 在“故障转移” > “恢复点”中，选择最近的恢复点。
3. 加密密钥设置与此方案无关。
4. 选择“在开始故障转移前关闭计算机”。 在触发故障转移之前，Site Recovery 将尝试关闭源虚拟机。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。

    ![故障转移](./media/migrate-scenarios-lift-and-shift/failover1.png)

5. 检查 Azure VM 是否在 Azure 中按预期显示。

    ![故障转移](./media/migrate-scenarios-lift-and-shift/failover2.png)

6. 在“复制的项”中，右键单击 VM >“完成迁移”。 该操作将完成迁移过程、停止 VM 的复制，并停止对 VM 的 Site Recovery 计费。

    ![故障转移](./media/migrate-scenarios-lift-and-shift/failover3.png)

### <a name="update-the-connection-string"></a>更新连接字符串

迁移过程的最后一个步骤是将应用程序的连接字符串更新为指向 SQL MI 上运行的已迁移数据库。

1. 单击“设置” > “连接字符串”，在 Azure 门户中找到新的连接字符串。

    ![故障转移](./media/migrate-scenarios-lift-and-shift/failover4.png)  

2. 需要使用 SQL MI 的用户名和密码更新此字符串。
3. 配置字符串后，需要在应用程序的 web.config 文件中替换当前连接字符串。
4. 更新并保存文件后，在 WEBVM 上重启 IIS。 可以在命令提示符下使用 IISRESET /RESTART 执行此操作。
5. 重启 IIS 后，应用程序现在会使用 SQL MI 上运行的数据库。
6. 此时，可以关闭本地 SQLVM 计算机，并完成迁移。



## <a name="conclusion"></a>结束语

此方案完成了以下操作：

> [!div class="checklist"]
> * 使用 DMS 将本地数据库迁移到了 Azure SQL 托管实例。
> * 使用 Azure Site Recovery 服务将本地 VM 迁移到了 Azure VM。

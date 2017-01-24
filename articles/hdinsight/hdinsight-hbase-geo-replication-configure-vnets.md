---
title: "在两个虚拟网络之间配置 VPN 连接 | Microsoft Docs"
description: "了解如何在两个虚拟网络之间配置 VPN 连接和域名解析，以及如何配置 HBase 异地复制。"
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1f47b85e-d5e8-4a4a-843e-1881d969e49f
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/28/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: f9b2192dfb40e2dd3cd2afa308f1cf84491e0580


---
# <a name="configure-a-vpn-connection-between-two-azure-virtual-networks"></a>在两个 Azure 虚拟网络之间配置 VPN 连接
> [!div class="op_single_selector"]
> * [配置 VPN 连接](hdinsight-hbase-geo-replication-configure-vnets.md)
> * [配置 DNS](hdinsight-hbase-geo-replication-configure-dns.md)
> * [配置 HBase 复制](hdinsight-hbase-geo-replication.md) 
> 
> 

Azure 虚拟网络站点到站点连接使用 VPN 网关来通过 Ipsec/IKE 提供安全隧道。 VNet 可位于不同的订阅和不同的区域中。 你甚至可以将 VNet 到 VNet 通信与多站点配置组合使用。 建立 VNet 到 VNet 连接的原因有多方面：

* 跨区域地域冗余和地域存在 
* 具有强大隔离边界的区域多层应用程序 
* 在 Azure 中跨订阅进行组织间通信

有关详细信息，请参阅[配置 VNet 到 VNet 连接](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)。 

若要通过视频查看：

> [!VIDEO https://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks/player]
> 
> 

本教程是有关创建 HBase 异地复制的[系列][hdinsight-hbase-replication]教程的一部分。 

* 在两个虚拟网络之间配置 VPN 连接（本教程）
* [为虚拟网络配置 DNS][hdinsight-hbase-geo-replication-dns]
* [配置 HBase 异地复制][hdinsight-hbase-geo-replication]

下图说明将要在本教程中创建的两个虚拟网络：

![HDInsight HBase 复制虚拟网络示意图][img-vnet-diagram]

## <a name="prerequisites"></a>先决条件
开始阅读本教程前的必要准备：

* **一个 Azure 订阅**。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* **配备 Azure PowerShell 的工作站**。
  
    在运行 PowerShell 脚本之前，请确保已使用以下 cmdlet 连接到 Azure 订阅：
  
        Add-AzureAccount
  
    如果具有多个 Azure 订阅，请使用以下 cmdlet 设置当前订阅：
  
        Select-AzureSubscription <AzureSubscriptionName>
  
    [!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

> [!NOTE]
> Azure 服务名称和虚拟机名称必须是唯一的。 本教程中使用的名称是 Contoso-[Azure Service/VM name]-[EU/US]。 例如，Contoso-VNet-EU 是位于北欧数据中心的 Azure 虚拟网络；Contoso-DNS-US 是位于美国东部数据中心的 DNS 服务器 VM。 必须选择适合自己的名称。
> 
> 

## <a name="create-two-azure-vnets"></a>创建两个 Azure VNet
**在北欧创建名为 Contoso-VNet-EU 的虚拟网络**

1. 登录到 [Azure 经典门户][azure-portal]。
2. 单击“新建”、“网络服务”、“虚拟网络”、“自定义创建”。
3. 输入：
   
   * **名称**：Contoso-VNet-EU
   * **位置**：北欧
     
     本教程使用北欧和美国东部数据中心。 可以选择自己的数据中心。
4. 输入：
   
   * **DNS 服务器**：（保留空白） 
     
     需要使用自己的 DNS 服务器在虚拟网络中进行名称解析。 有关何时使用 Azure 提供的名称解析以及何时使用自己的 DNS 服务器的详细信息，请参阅[名称解析 (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 有关在 VNet 之间配置名称解析的说明，请参阅[在两个 Azure 虚拟网络之间配置 DNS][hdinsight-hbase-dns]。
   * **配置点到站点 VPN**：（未选中）
     
     点到站点连接不适用于本方案。
   * **配置站点到站点 VPN**：（未选中）
     
     将配置与美国东部数据中心的 Azure 虚拟网络的站点到站点 VPN 连接。
5. 输入：
   
   * **地址空间起始 IP**：10.1.0.0
   * **地址空间 CIDR**：/16
   * **Subnet-1 起始 IP**：10.1.0.0
   * **Subnet-1 CIDR**：/24
   
   地址空间不能与美国虚拟网络重叠。  

**在西欧创建名为 Contoso-VNet-EU 的虚拟网络**

* 使用以下值重复上一过程：
  
  * **名称**： Contoso-VNet-US
  * **位置**：美国东部
  * **DNS 服务器**：（保留空白）
  * **配置点到站点 VPN**：（未选中）
  * **配置站点到站点 VPN**：（未选中）
  * **地址空间起始 IP**：10.2.0.0
  * **地址空间 CIDR**：/16
  * **Subnet-1 起始 IP**：10.2.0.0
  * **Subnet-1 CIDR**：/24

## <a name="configure-a-vpn-connection-between-the-two-vnets"></a>在两个 VNet 之间配置 VPN 连接
### <a name="create-local-networks"></a>创建本地网络
创建 VNet 到 VNet 配置时，需要配置每个 VNet 以便彼此标识为本地网络站点。 在此部分中，要将每个 VNet 配置为本地网络。 本地网络与相应的 VNet 共享同一个 IP 地址空间。

![配置 Azure VPN 站点到站点配置 - Azure 本地网络][img-vnet-lnet-diagram]

**创建与 Contoso-VNet-EU 网络地址空间匹配的名为 Contoso-LNet-EU 的本地网络**

1. 在 Azure 经典门户中，单击“新建”、“网络服务”、“虚拟网络”、“添加本地网络”。
2. 输入：
   
   * **名称**：Contoso-LNet-EU
   * **VPN 设备 IP 地址**：192.168.0.1（稍后将会更新此地址）
     
       通常，应该使用 VPN 设备的实际外部 IP 地址。 对于 VNet 到 VNet 配置，要使用 VPN 网关 IP 地址。 如果尚未为两个 VNet 创建 VPN 网关，请输入任意 IP 地址，然后回过头来修复该地址。
3. 输入：
   
   * **地址空间起始 IP：**10.1.0.0
   * **地址空间 CIDR：**/16
   
   此值必须精确对应于你前面为 Contoso-VNet-EU 指定的范围。

**创建与 Contoso-VNet-US 网络地址空间匹配的名为 Contoso-LNet-US 的本地网络**

* 使用以下参数重复上一过程：
  
  * **名称**：Contoso-LNet-US
  * **VPN 设备 IP 地址**：192.168.0.1（稍后将会更新此地址）
  * **地址空间起始 IP**：10.2.0.0
  * **地址空间 CIDR**：/16

### <a name="create-vpn-gateways"></a>创建 VPN 网关
此配置包括两个部分。 首先需要配置与本地网络的 VNet 站点到站点连接，然后创建动态路由 VPN。 VNet 到 VNet 连接需要具有动态路由 VPN 的 Azure VPN 网关。 不支持 Azure 静态路由 VPN。

**在 Contoso-VNet-EU 与 Contoso-LNet-US 之间配置站点到站点连接**

1. 在 Azure 经典门户中，单击左侧窗格中的的“网络”，
2. 单击“Contoso-VNet-EU”。
3. 单击“配置”选项卡。
4. 选中“连接到本地网络”。
5. 在“本地网络”中，选择“Contoso-LNet-US”。
6. 在虚拟网络地址空间部分单击“添加网关子网”。
7. 单击“保存”。
8. 单击“确定”以确认。

**为 Contoso-VNet-EU 创建 VPN 网关**

1. 在 Azure 经典门户中，单击“仪表板”选项卡。
2. 在页面底部单击“创建网关”，然后单击“动态路由”。
3. 单击“是”确认。 请注意页面上的网关图形将更改为黄色，并显示“正在创建网关”。 创建网关通常需要大约 15 分钟时间。
   
   当网关状态更改为“正在连接”时，每个网关的 IP 地址将显示在仪表板中。 写下对应于每个 VNet 的 IP 地址，请注意不要混淆。 在“本地网络”中编辑 VPN 设备的占位符 IP 地址时，将使用这些 IP 地址。
4. 创建**网关 IP 地址**的副本。 在下一部分中，将使用它为 Contoso-VNet-EU 配置 VPN 网关 IP 地址。

**为 Contoso-VNet-EU 创建 VPN 网关**

* 重复前两个过程，以便在 Contoso-VNet-US 与 Contoso-LNet-EU 之间配置站点到站点连接，并为 Contoso-Vnet-US 创建 VPN 网关。 完成后，将获得 Contoso-VNet-US 的 VPN 网关 IP 地址。

### <a name="set-the-vpn-device-ip-addresses-for-local-networks"></a>为本地网络设置 VPN 设备 IP 地址
在上一部分中，你为每个 VNet 创建了一个 VPN 网关。 你已获得了 VPN 网关的 IP 地址。 现在，可以回过头来配置本地网络 VPN 设备 IP 地址。

**为 Contoso-LNet-EU 配置 VPN 设备 IP 地址** 

1. 在 Azure 经典门户中，单击左侧窗格中的的“网络”。
2. 在顶部单击“本地网络”。
3. 单击“Contoso-LNet-EU”，然后在底部单击“编辑”。
4. 更新 **VPN 设备 IP 地址**。  该地址是从 Contoso-VNET-EU 的“仪表板”选项卡获取的地址。
5. 单击右侧按钮。
6. 单击勾选按钮。

**为 Contoso-LNet-US 配置 VPN 设备 IP 地址** 

* 重复上一过程，为 Contoso-LNet-US 配置 VPN 设备 IP 地址。

### <a name="set-vnet-gateway-keys"></a>设置 VNet 网关密钥
VNet 网关使用共享密钥对虚拟网络之间的连接进行身份验证。 不能从 Azure 经典门户配置该密钥。 必须使用 PowerShell 或 .NET SDK 来配置。

**设置密钥**

1. 在工作站中，打开 **Windows PowerShell ISE** 或 Windows PowerShell 控制台。
2. 更新以下脚本中的参数，然后运行该脚本：
   
        Add-AuzreAccount
        Select-AzureSubscription -[AzureSubscriptionName]
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-EU -LocalNetworkSiteName Contoso-LNet-US -SharedKey A1b2C3D4
        Set-AzureVNetGatewayKey -VNetName ContosoVNet-US -LocalNetworkSiteName Contoso-LNet-EU -SharedKey A1b2C3D4 

## <a name="check-the-vpn-connection"></a>检查 VPN 连接
在未将任何 VM 部署到 VNet 的情况下，可在 Azure 经典门户中使用“VNet 仪表板”页上的虚拟网络可视示意图检查连接状态：

![HDInsight HBase 复制虚拟网络 VPN 连接状态][img-vpn-status]

## <a name="next-steps"></a>后续步骤
在本教程中，你已学习如何在两个 Azure 虚拟网络之间配置 VPN 连接。 本系列教程包括的其他两篇文章：

* [在两个 Azure 虚拟网络之间配置 DNS][hdinsight-hbase-geo-replication-dns]
* [配置 HBase 异地复制][hdinsight-hbase-geo-replication]

[hdinsight-hbase-geo-replication-dns]: hdinsight-hbase-geo-replication-configure-dns.md
[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com

[powershell-install]: ../install-configure-powershell.md



[hdinsight-hbase-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-dns]: hdinsight-hbase-geo-replication-configure-dns.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-diagram.png
[img-vnet-lnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-lnet-diagram.png
[img-vpn-status]: ./media/hdinsight-hbase-geo-replication-configure-vnets/hdinsight-hbase-vpn-status.png 



<!--HONumber=Dec16_HO2-->



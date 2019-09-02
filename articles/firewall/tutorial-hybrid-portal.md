---
title: 教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙
description: 本教程介绍如何使用 Azure 门户部署和配置 Azure 防火墙。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: f7dce448b01c211441fd1e0fd530ff6ad062c303
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114869"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>教程：使用 Azure 门户在混合网络中部署和配置 Azure 防火墙

将本地网络连接到 Azure 虚拟网络以创建混合网络时，必须能够控制对 Azure 网络资源的访问，这是整体安全计划的重要部分。

可以使用 Azure 防火墙通过规则来定义允许的和拒绝的网络流量，以便控制混合网络中的网络访问。

在本教程中，请创建三个虚拟网络：

- **VNet-Hub** - 防火墙在此虚拟网络中。
- **VNet-Spoke** - 分支虚拟网络代表 Azure 中的工作负荷。
- **VNet-Onprem** - 本地虚拟网络代表本地网络。 在实际部署中，可以使用 VPN 或 ExpressRoute 来连接它。 为简单起见，本教程将使用 VPN 网关连接，并使用 Azure 中的某个虚拟网络来代表本地网络。

![混合网络中的防火墙](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 声明变量
> * 创建防火墙中心虚拟网络
> * 创建分支虚拟网络
> * 创建本地虚拟网络
> * 配置和部署防火墙
> * 创建并连接 VPN 网关
> * 将中心和分支虚拟网络对等互连
> * 创建路由
> * 创建虚拟机
> * 测试防火墙

如果要使用 Azure PowerShell 完成此过程，请参阅[使用 Azure PowerShell 在混合网络中部署和配置 Azure 防火墙](tutorial-hybrid-ps.md)。

## <a name="prerequisites"></a>先决条件

若要正常开展此方案，必须符合三项关键要求：

- 分支子网中有一个指向 Azure 防火墙 IP 地址（用作默认网关）的用户定义的路由 (UDR)。 必须在此路由表中**禁用** BGP 路由传播。
- 中心网关子网中的 UDR 必须指向用作分支网络下一跃点的防火墙 IP 地址。

   无需在 Azure 防火墙子网中创建 UDR，因为它会从 BGP 探测路由。
- 在 VNet-Hub 与 VNet-Spoke 之间建立对等互连时，请务必设置 **AllowGatewayTransit**；在 VNet-Spoke 与 VNet-Hub 之间建立对等互连时，请务必设置 **UseRemoteGateways**。

请参阅本教程的[创建路由](#create-the-routes)部分了解如何创建这些路由。

>[!NOTE]
>Azure 防火墙必须具有直接的 Internet 连接。 如果 AzureFirewallSubnet 知道通过 BGP 的本地网络的默认路由，则必须将其替代为 0.0.0.0/0 UDR，将 NextHopType 值设置为 Internet 以保持 Internet 直接连接   。 默认情况下，Azure 防火墙不支持强制的安全加密链路连接到本地网络。
>
>但是，如果你的配置要求强制的安全加密链路连接到本地网络，Microsoft 将基于具体的情况提供支持。 请联系支持人员，以便我们可以查看你的情况。 如果接受，我们将允许你的订阅，确保保持所需的防火墙 Internet 连接。

>[!NOTE]
>即使 UDR 指向作为默认网关的 Azure 防火墙，也会直接路由直接对等互连 VNet 之间的流量。 若要在此方案中将子网到子网流量发送到防火墙，UDR 必须在这两个子网上显式地包含目标子网网络前缀。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-the-firewall-hub-virtual-network"></a>创建防火墙中心虚拟网络

首先，创建用于存储本教程资源的资源组：

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在 Azure 门户主页上，选择“资源组” > “添加”   。
3. 对于“资源组名称”，请键入 **FW-Hybrid-Test**。 
4. 对于“订阅”，请选择自己的订阅。 
5. 对于“区域”，请选择“美国东部”。   以后创建的所有资源必须位于同一位置。
6. 选择“查看 + 创建”  。
7. 选择“创建”  。

现在创建 VNet：

> [!NOTE]
> AzureFirewallSubnet 子网的大小为 /26。 有关子网大小的详细信息，请参阅 [Azure 防火墙常见问题解答](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size)。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在“网络”下，选择“虚拟网络”。  
4. 对于“名称”，请键入 **VNet-hub**。 
5. 对于“地址空间”，请键入 **10.5.0.0/16**。 
6. 对于“订阅”，请选择自己的订阅。 
7. 对于“资源组”，请选择“FW-Hybrid-Test”。  
8. 对于“位置”，请选择“美国东部”。  
9. 在“子网”下，为“名称”键入 **AzureFirewallSubnet**。   防火墙将位于此子网中，子网名称**必须**是 AzureFirewallSubnet。
10. 对于“地址范围”，请键入 **10.5.0.0/26**。  
11. 接受其他默认设置，然后选择“创建”。 

## <a name="create-the-spoke-virtual-network"></a>创建分支虚拟网络

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在“网络”下，选择“虚拟网络”。  
4. 对于“名称”，请键入 **VNet-Spoke**。 
5. 对于“地址空间”，请键入 **10.6.0.0/16**。 
6. 对于“订阅”，请选择自己的订阅。 
7. 对于“资源组”，请选择“Test-FW-RG”。  
8. 对于“位置”，请选择前面使用的同一位置。 
9. 在“子网”下，为“名称”键入 **SN-Workload**。  
10. 对于“地址范围”，请键入 **10.6.0.0/24**。 
11. 接受其他默认设置，然后选择“创建”。 

现在，为网关创建第二个子网。

1. 在“VNet-Spoke”页上，选择“子网”。  
2. 选择“+子网”。 
3. 对于“名称”，请键入 **GatewaySubnet**。 
4. 对于“地址范围(CIDR 块)”，请键入 **10.6.1.0/24**。 
5. 选择“确定”  。

## <a name="create-the-on-premises-virtual-network"></a>创建本地虚拟网络

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在“网络”下，选择“虚拟网络”。  
4. 对于“名称”，请键入 **VNet-OnPrem**。 
5. 对于“地址空间”，请键入 **192.168.0.0/16**。 
6. 对于“订阅”，请选择自己的订阅。 
7. 对于“资源组”，请选择“FW-Hybrid-Test”。  
8. 对于“位置”，请选择前面使用的同一位置。 
9. 在“子网”下，为“名称”键入 **SN-Corp**。  
10. 对于“地址范围”，请键入 **192.168.1.0/24**。 
11. 接受其他默认设置，然后选择“创建”。 

现在，为网关创建第二个子网。

1. 在“VNet-Onprem”页上，选择“子网”。  
2. 选择“+子网”。 
3. 对于“名称”，请键入 **GatewaySubnet**。 
4. 对于“地址范围(CIDR 块)”，请键入 **192.168.2.0/24**。 
5. 选择“确定”  。

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址

这是用于本地网关的公共 IP 地址。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在搜索文本框中，键入“公共 IP 地址”并按 **Enter**。 
3. 依次选择“公共 IP 地址”、“创建”。  
4. 对于“名称”，请键入 **VNet-Onprem-GW-pip**。
5. 对于“资源组”，请键入 **FW-Hybrid-Test**。
6. 对于“位置”，请选择前面使用的同一位置。 
7. 接受其他默认值，然后选择“创建”。 

## <a name="configure-and-deploy-the-firewall"></a>配置和部署防火墙

现在，将防火墙部署到防火墙中心虚拟网络中。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在左列中选择“网络”，然后选择“防火墙”。  
4. 在“创建防火墙”页上，使用下表配置防火墙： 

   |设置  |值  |
   |---------|---------|
   |Subscription     |\<用户的订阅\>|
   |Resource group     |**FW-Hybrid-Test** |
   |Name     |**AzFW01**|
   |位置     |选择前面使用的同一位置|
   |选择虚拟网络     |**使用现有项**：<br> **VNet-hub**|
   |公共 IP 地址     |新建： <br>“名称” - “fw-pip”。   |

5. 选择“查看 + 创建”  。
6. 查看摘要，然后选择“创建”以创建防火墙。 

   部署过程需要花费几分钟时间。
7. 部署完成后，转到“FW-Hybrid-Test”资源组，然后选择“AzFW01”防火墙。  
8. 记下专用 IP 地址。 稍后在创建默认路由时需要用到此地址。

### <a name="configure-network-rules"></a>配置网络规则

首先，添加一个网络规则以允许 Web 流量。

1. 在“AzFW01”页上，选择“规则”。  
2. 选择“网络规则集合”选项卡。 
3. 选择“添加网络规则集合”。 
4. 对于“名称”，请键入 **RCNet01**。 
5. 对于“优先级”，请键入 **100**。 
6. 对于“操作”，请选择“允许”。  
6. 在“规则”下，为“名称”键入 **AllowWeb**。  
7. 对于“协议”，请选择“TCP”。  
8. 对于“源地址”，请键入 **192.168.1.0/24**。 
9. 对于“目标地址”，请键入 **10.6.0.0/16**
10. 对于“目标端口”，请键入 **80**。 

现在添加一个规则以允许 RDP 流量。

在第二个规则行中键入以下信息：

1. 对于“名称”，请键入 **AllowRDP**。 
2. 对于“协议”，请选择“TCP”。  
3. 对于“源地址”，请键入 **192.168.1.0/24**。 
4. 对于“目标地址”，请键入 **10.6.0.0/16**
5. 对于“目标端口”，请键入 **3389**。 
6. 选择 **添加** 。

## <a name="create-and-connect-the-vpn-gateways"></a>创建并连接 VPN 网关

通过 VPN 网关连接中心和本地虚拟网络。

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>为中心虚拟网络创建 VPN 网关

现在，为中心虚拟网络创建 VPN 网关。 网络到网络配置需要 RouteBased VpnType。 创建 VPN 网关通常需要 45 分钟或更长时间，具体取决于所选 VPN 网关的 SKU。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在搜索文本框中，键入“虚拟网络网关”并按 **Enter**。 
3. 依次选择“虚拟网络网关”、“创建”。  
4. 对于“名称”，请键入 **GW-hub**。 
5. 对于“区域”，请选择前面使用的同一区域。 
6. 对于“网关类型”，请选择“VPN”。  
7. 对于“VPN 类型”，请选择“基于路由”。  
8. 对于“SKU”，请选择“基本”。  
9. 对于“虚拟网络”，请选择“VNet-hub”。  
10. 对于“公共 IP 地址”，请选择“新建”，然后键入 **VNet-hub-GW-pip** 作为名称。  
11. 接受剩余的默认值，然后选择“查看 + 创建”。 
12. 检查配置，然后选择“创建”。 

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>为本地虚拟网络创建 VPN 网关

现在，为本地虚拟网络创建 VPN 网关。 网络到网络配置需要 RouteBased VpnType。 创建 VPN 网关通常需要 45 分钟或更长时间，具体取决于所选 VPN 网关的 SKU。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在搜索文本框中，键入“虚拟网络网关”并按 **Enter**。 
3. 依次选择“虚拟网络网关”、“创建”。  
4. 对于“名称”，请键入 **GW-Onprem**。 
5. 对于“区域”，请选择前面使用的同一区域。 
6. 对于“网关类型”，请选择“VPN”。  
7. 对于“VPN 类型”，请选择“基于路由”。  
8. 对于“SKU”，请选择“基本”。  
9. 对于“虚拟网络”，请选择“VNet-Onprem”。  
10. 对于“公共 IP 地址”，请选择“新建”，然后键入 **VNet-Onprem-GW-pip** 作为名称。  
11. 接受剩余的默认值，然后选择“查看 + 创建”。 
12. 检查配置，然后选择“创建”。 

### <a name="create-the-vpn-connections"></a>创建 VPN 连接

现在，可在中心与本地网关之间创建 VPN 连接。

此步骤创建从中心虚拟网络到本地虚拟网络的连接。 示例中引用了共享密钥。 可以对共享密钥使用自己的值。 共享密钥必须与两个连接匹配，这一点非常重要。 创建连接可能需要简短的一段时间才能完成。

1. 打开“FW-Hybrid-Test”资源组并选择“GW-hub”网关。  
2. 在左栏中选择“连接”。 
3. 选择 **添加** 。
4. 键入 **Hub-to-Onprem** 作为连接名称。
5. 选择“VNet 到 VNet”作为“连接类型”。  
6. 对于“第二个虚拟网络网关”，请选择“GW-Onprem”。  
7. 对于“共享密钥(PSK)”，请键入 **AzureA1b2C3**。 
8. 选择“确定”  。

创建本地到中心虚拟网络连接。 此步骤类似于前一步骤，但这次是创建从 VNet-Onprem 到 VNet-hub 的连接。 确保共享密钥匹配。 几分钟后会建立连接。

1. 打开“FW-Hybrid-Test”资源组并选择“GW-Onprem”网关。  
2. 在左栏中选择“连接”。 
3. 选择 **添加** 。
4. 键入 **Onprem-to-Hub** 作为连接名称。
5. 选择“VNet 到 VNet”作为“连接类型”。  
6. 对于“第二个虚拟网络网关”，请选择“GW-hub”。  
7. 对于“共享密钥(PSK)”，请键入 **AzureA1b2C3**。 
8. 选择“确定”  。


#### <a name="verify-the-connection"></a>验证连接

大约五分钟后，这两个连接的状态应显示为“已连接”。 

![网关连接](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>将中心和分支虚拟网络对等互连

现在，将中心和分支虚拟网络对等互连。

1. 打开“FW-Hybrid-Test”资源组并选择“VNet-hub”虚拟网络。  
2. 在左栏中选择“对等互连”。 
3. 选择 **添加** 。
4. 对于“名称”，请键入 **HubtoSpoke**。 
5. 对于“虚拟网络”，请选择“VNet-spoke”。  
6. 键入 **SpoketoHub** 作为从 VNetSpoke 到 VNet-hub 的对等互连名称。
7. 选择“允许网关传输”。 
8. 选择“确定”  。

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>配置 SpoketoHub 对等互连的其他设置

需要对 SpoketoHub 对等互连启用“允许转发的流量”。 

1. 打开“FW-Hybrid-Test”资源组并选择“VNet-Spoke”虚拟网络。  
2. 在左栏中选择“对等互连”。 
3. 选择“SpoketoHub”对等互连。 
4. 在“允许从 VNet-hub 转发到 VNet-Spoke 的流量”下，选择“已启用”。  
5. 选择“保存”。 

## <a name="create-the-routes"></a>创建路由

接下来创建一对路由：

- 通过防火墙 IP 地址从中心网关子网连接到分支子网的路由
- 通过防火墙 IP 地址从分支子网连接的默认路由

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在搜索文本框中，键入“路由表”并按 **Enter**。 
3. 选择“路由表”。 
4. 选择“创建”  。
5. 对于“名称”，请键入 **UDR-Hub-Spoke**。
6. 选择“FW-Hybrid-Test”作为资源组。 
8. 对于“位置”，请选择前面使用的同一位置。 
9. 选择“创建”  。
10. 创建路由表后，请选择它以打开路由表页。
11. 在左栏中选择“路由”。 
12. 选择 **添加** 。
13. 键入 **ToSpoke** 作为路由名称。
14. 键入 **10.6.0.0/16** 作为地址前缀。
15. 选择“虚拟设备”作为下一跃点类型。 
16. 键入前面记下的防火墙专用 IP 地址作为下一跃点地址。
17. 选择“确定”  。

现在，将路由关联到子网。

1. 在“UDR-Hub-Spoke - 路由”页上，选择“子网”。  
2. 选择“关联”。 
3. 选择“选择虚拟网络”。 
4. 选择“VNet-hub”。 
5. 选择“GatewaySubnet”。 
6. 选择“确定”  。

现在，创建从辐射子网的默认路由。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在搜索文本框中，键入“路由表”并按 **Enter**。 
3. 选择“路由表”。 
5. 选择“创建”  。
6. 键入 **UDR-DG** 作为名称。
7. 选择“FW-Hybrid-Test”作为资源组。 
8. 对于“位置”，请选择前面使用的同一位置。 
4. 对于“虚拟网络网关路由传播”，请选择“已禁用”。  
1. 选择“创建”  。
2. 创建路由表后，请选择它以打开路由表页。
3. 在左栏中选择“路由”。 
4. 选择 **添加** 。
5. 键入 **ToSpoke** 作为路由名称。
6. 键入 **0.0.0.0/0** 作为地址前缀。
7. 选择“虚拟设备”作为下一跃点类型。 
8. 键入前面记下的防火墙专用 IP 地址作为下一跃点地址。
9. 选择“确定”  。

现在，将路由关联到子网。

1. 在“UDR-DG - 路由”页上，选择“子网”。  
2. 选择“关联”。 
3. 选择“选择虚拟网络”。 
4. 选择“VNet-spoke”。 
5. 选择“SN-Workload”。 
6. 选择“确定”  。

## <a name="create-virtual-machines"></a>创建虚拟机

现在，创建分支工作负荷与本地虚拟机，并将其放入相应的子网。

### <a name="create-the-workload-virtual-machine"></a>创建工作负荷虚拟机

在分支虚拟网络中，创建运行 IIS 且不使用公共 IP 地址的虚拟机。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在“常用”下，选择“Windows Server 2016 Datacenter”。  
3. 输入虚拟机的以下值：
    - **资源组** - 选择“FW-Hybrid-Test”。 
    - **虚拟机名称**：*VM-Spoke-01*。
    - **区域** - 前面使用的同一区域。
    - **用户名**：*azureuser*。
    - **密码**：*Azure123456!* -
4. 选择“下一步:磁盘”  。
5. 接受默认值，然后选择“下一步: 网络”。 
6. 选择“VNet-Spoke”作为虚拟网络；子网为“SN-Workload”。  
7. 对于“公共 IP”，请选择“无”。  
8. 对“公共入站端口”，请选择“允许所选端口”，然后选择“HTTP (80)”和“RDP (3389)”。    
9. 选择“下一步: 管理”。 
10. 对于“启动诊断”，请选择“关闭”。  
11. 选择“查看 + 创建”，检查摘要页上的设置，然后选择“创建”。  

### <a name="install-iis"></a>安装 IIS

1. 在 Azure 门户中打开 Cloud Shell，确保它设置为“PowerShell”。 
2. 运行以下命令以在虚拟机上安装 IIS：

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>创建本地虚拟机

你将使用此虚拟机通过远程桌面连接到公共 IP 地址。 然后，请在该虚拟机中通过防火墙连接到本地服务器。

1. 在 Azure 门户主页上，选择“创建资源”。 
2. 在“常用”下，选择“Windows Server 2016 Datacenter”。  
3. 输入虚拟机的以下值：
    - **资源组** - 选择现有资源组，然后选择“FW-Hybrid-Test”。 
    - **虚拟机名称** - *VM-Onprem*。
    - **区域** - 前面使用的同一区域。
    - **用户名**：*azureuser*。
    - **密码**：*Azure123456!* 。
4. 选择“下一步:磁盘”  。
5. 接受默认值，然后选择“下一步: 网络”。 
6. 选择“VNet-Onprem”作为虚拟网络；子网为“SN-Corp”。  
7. 对“公共入站端口”，请选择“允许所选端口”，然后选择“RDP (3389)”。   
8. 选择“下一步: 管理”。 
9. 对于“启动诊断”，请选择“关闭”。  
10. 选择“查看 + 创建”，检查摘要页上的设置，然后选择“创建”。  

## <a name="test-the-firewall"></a>测试防火墙

1. 首先，获取并记下 **VM-spoke-01** 虚拟机的专用 IP 地址。

2. 在 Azure 门户中，连接到 **VM-Onprem** 虚拟机。
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. 在 **VM-Onprem** 上打开 Web 浏览器并浏览到 http://\<VM-spoke-01 的专用 IP\>。

   应会看到“VM-spoke-01”网页：  ![VM-Spoke-01 网页](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. 在“VM-Onprem”虚拟机中，打开远程桌面并连接到 **VM-spoke-01** 的专用 IP 地址。 

   应可成功连接，并且可以登录。

现已验证防火墙规则可正常工作：

<!---- You can ping the server on the spoke VNet.--->
- 可以浏览分支虚拟网络中的 Web 服务器。
- 可以使用 RDP 连接到分支虚拟网络中的服务器。

接下来，将防火墙网络规则集合操作更改为“拒绝”，以验证防火墙规则是否按预期工作。 

1. 选择“AzFW01”防火墙。 
2. 选择“规则”。 
3. 选择“网络规则集合”选项卡，然后选择“RCNet01”规则集合。  
4. 对于“操作”，请选择“拒绝”。  
5. 选择“保存”。 

在测试更改的规则之前，请关闭所有现有的远程桌面。 现在再次运行测试。 这一次，这些规则应该全部失败。

## <a name="clean-up-resources"></a>清理资源

可以保留防火墙资源以便在下一篇教程中使用。不再需要时，请删除 **FW-Hybrid-Test** 资源组，以删除与防火墙相关的所有资源。

## <a name="next-steps"></a>后续步骤

接下来，可以监视 Azure 防火墙日志。

> [!div class="nextstepaction"]
> [教程：监视 Azure 防火墙日志](./tutorial-diagnostics.md)

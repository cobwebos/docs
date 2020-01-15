---
title: 使用 Azure 虚拟 WAN 与 Azure 建立点到站点连接 | Microsoft Docs
description: 本教程介绍如何使用 Azure 虚拟 WAN 与 Azure 建立点到站点 VPN 连接。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 02c8bf24d4ddb6408160da7a4c517d6c8c82de5f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450906"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建用户 VPN 连接

本教程介绍如何使用虚拟 WAN 通过 IPsec/IKE (IKEv2) 或 OpenVPN VPN 连接与 Azure 中的资源建立连接。 此类连接要求在客户端计算机上配置一个客户端。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 WAN
> * 创建中心
> * 创建 P2S 配置
> * 下载 VPN 客户端配置文件
> * 将 P2S 配置应用到中心
> * 将 VNet 连接到中心
> * 下载并应用 VPN 客户端配置
> * 查看虚拟 WAN
> * 查看资源运行状况

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="wan"></a>创建虚拟 WAN

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 导航到“虚拟 WAN”页。 在门户中，单击“+创建资源”  。 在搜索框中键入“虚拟 WAN”  ，然后选择 Enter。
2. 从结果中选择“虚拟 WAN”  。 在“虚拟 WAN”页上，单击“创建”以打开“创建 WAN”页  。
3. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段   ：

   ![虚拟 WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **订阅** - 选择要使用的订阅。
   * **资源组** - 新建资源组或使用现有的资源组。
   * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能更轻松地管理和查找所创建的 WAN 资源。
   * **名称** - 键入要用于称呼 WAN 的名称。
   * **类型：** 标准。 如果创建基本 WAN，则只能创建基本中心。 基本中心仅支持 VPN 站点到站点连接。
4. 填写完字段后，单击“审阅 + 创建”  。
5. 验证通过后，选择“创建”以创建虚拟 WAN  。

## <a name="site"></a>创建空虚拟中心

1. 在虚拟 WAN 下，选择“中心”，然后单击“+新建中心” 

   ![新建站点](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. 在“创建虚拟中心”页上，请填写以下字段。

   **区域** - 选择要在其中部署虚拟中心的区域。

   **名称** - 输入要用于称呼虚拟中心的名称。

   **中心专用地址空间** - 用 CIDR 表示法来表示的中心地址范围。

   ![新建站点](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. 单击“查看 + 创建” 
4. 在“验证已通过”  页上，单击“创建” 

## <a name="site"></a>创建 P2S 配置

P2S 配置定义连接远程客户端的参数。

1. 导航到“所有资源”  。
2. 单击创建的虚拟 WAN。
3. 单击页面顶部的“+创建用户 VPN 配置”  ，打开“创建新的用户 VPN 配置”页。 

   ![新建站点](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. 在“创建新的用户 VPN 配置”页上填写以下字段： 

   **配置名称** - 这是需要按其来引用配置的名称。

   **隧道类型** - 用于隧道的协议。

   **根证书名称** - 证书的说明性名称。

   **公用证书数据** - Base-64 编码的 X.509 证书数据。
  
   ![新建站点](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. 单击“创建”  以创建配置。

## <a name="hub"></a>编辑中心分配

1. 导航到虚拟 WAN 下的“中心”  边栏选项卡
2. 选择要将 VPN 服务器配置与之关联的中心，然后单击“...” 

   ![新建站点](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. 单击“编辑虚拟中心”  。
4. 选中“包括点到站点网关”  复选框，然后选择所需的网关缩放单元  。

   ![新建站点](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. 输入用于为 VPN 客户端分配 IP 地址的“地址池”  。
6. 单击“确认” 
7. 完成此操作最多需要 30 分钟。

## <a name="device"></a>下载 VPN 配置文件

使用 VPN 配置文件来配置客户端。

1. 在虚拟 WAN 的页面上，单击“用户 VPN 配置”  。
2. 在页面顶部，单击“下载用户 VPN 配置”  。
3. 完成创建文件后，可以单击相应的链接下载该文件。
4. 使用此配置文件配置 VPN 客户端。

### <a name="configure-user-vpn-clients"></a>配置用户 VPN 客户端
使用下载的配置文件配置远程访问客户端。 每个操作系统的过程并不相同，请按照下面的正确说明操作：

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. 从官方网站下载并安装 OpenVPN 客户端。
2. 下载网关的 VPN 配置文件。 这可以通过 Azure 门户中的“用户 VPN 配置”选项卡或 PowerShell 中的 New-AzureRmVpnClientConfiguration 来完成。
3. 解压缩该配置文件。 从记事本中的 OpenVPN 文件夹中打开 vpnconfig.ovpn 配置文件。
4. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，可以直接打开 .cer 文件并在证书标头之间复制 base64 密钥。 请参阅此处，了解[如何导出证书以获取已编码的公钥](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)。
5. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 请参阅此处，了解[如何提取私钥](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients#windows)。
6. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
7. 将 vpnconfig.ovpn 文件复制到 C:\Program Files\OpenVPN\config 文件夹。
8. 右键单击系统托盘中的 OpenVPN 图标，然后单击“连接”。

##### <a name="ikev2"></a>IKEv2

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。
2. 双击所需的包进行安装。 如果显示 SmartScreen 弹出窗口，请单击“更多信息”，并单击“仍要运行”。
3. 在客户端计算机上，导航到“网络设置”，并单击“VPN”。 VPN 连接显示所连接到的虚拟网络的名称。
4. 尝试连接前，请验证客户端计算机上是否已安装客户端证书。 使用本机 Azure 证书身份验证类型时，客户端证书是身份验证必需的。 有关生成证书的详细信息，请参阅[生成证书](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site)。 有关如何安装客户端证书的信息，请参阅“安装客户端证书”。

## <a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
2. 在“概述”页上，地图中的每个点表示一个中心。 将鼠标悬停在任一点上可以查看中心运行状况的摘要。
3. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="viewhealth"></a>查看资源运行状况

1. 导航到 WAN。
2. 在“WAN”页上的“支持 + 故障排除”部分，单击“运行状况”并查看资源。  


## <a name="cleanup"></a>清理资源

如果不再需要这些资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。

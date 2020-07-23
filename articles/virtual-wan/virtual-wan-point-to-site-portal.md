---
title: 使用 Azure 虚拟 WAN 与 Azure 建立点到站点连接 | Microsoft Docs
description: 本教程介绍如何使用 Azure 虚拟 WAN 与 Azure 建立点到站点 VPN 连接。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 06/29/2020
ms.author: alzam
ms.openlocfilehash: 9c93ad0357011008c45b2898260a655509b02dc2
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560739"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>教程：使用 Azure 虚拟 WAN 创建用户 VPN 连接

本教程介绍如何使用虚拟 WAN 通过 IPsec/IKE (IKEv2) 或 OpenVPN VPN 连接与 Azure 中的资源建立连接。 此类连接要求在客户端计算机上配置一个客户端。 有关虚拟 WAN 的详细信息，请参阅[虚拟 WAN 概述](virtual-wan-about.md)

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建 WAN
> * 创建 P2S 配置
> * 创建中心
> * 指定 DNS 服务器
> * 下载 VPN 客户端配置文件
> * 查看虚拟 WAN

![虚拟 WAN 示意图](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>开始之前

在开始配置之前，请验证你是否符合以下条件：

* 你拥有一个要连接到的虚拟网络。 确认本地网络的任何子网都不会与要连接到的虚拟网络重叠。 若要在 Azure 门户中创建虚拟网络，请参阅[快速入门](../virtual-network/quick-create-portal.md)。

* 虚拟网络不包含任何虚拟网络网关。 如果虚拟网络包含网关（VPN 或 ExpressRoute），则必须删除所有网关。 此配置要求将虚拟网络改为连接到虚拟 WAN 中心网关。

* 获取中心区域的 IP 地址范围。 该中心是虚拟 WAN 创建和使用的虚拟网络。 为中心指定的地址范围不能与要连接到的任何现有虚拟网络重叠。 此外，它也不能与本地连接到的地址范围重叠。 如果不熟悉本地网络配置中的 IP 地址范围，则咨询能够提供此类详细信息的人员。

* 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-wan"></a><a name="wan"></a>创建虚拟 WAN

从浏览器导航到 [Azure 门户](https://portal.azure.com)并使用 Azure 帐户登录。

1. 导航到“虚拟 WAN”页。 在门户中，选择“+创建资源”。 在搜索框中键入“虚拟 WAN”，然后选择“Enter” 。
1. 从结果中选择“虚拟 WAN”。 在“虚拟 WAN”页上，选择“创建”以打开“创建 WAN”页。
1. 在“创建 WAN”页的“基本信息”选项卡上，填写以下字段 ：

   ![虚拟 WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **订阅** - 选择要使用的订阅。
   * **资源组** - 新建资源组或使用现有的资源组。
   * **资源组位置** - 从下拉列表中选择资源位置。 WAN 是一个全局资源，不会驻留在某个特定区域。 但是，必须选择一个区域才能更轻松地管理和查找所创建的 WAN 资源。
   * **名称** - 键入要用于称呼 WAN 的名称。
   * **类型：** 标准。 如果创建基本 WAN，则只能创建基本中心。 基本中心仅支持 VPN 站点到站点连接。
1. 填写完字段后，单击“审阅 + 创建”。
1. 验证通过后，选择“创建”以创建虚拟 WAN。

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>创建 P2S 配置

P2S 配置定义连接远程客户端的参数。

1. 导航到“所有资源”。
1. 选择创建的虚拟 WAN。
1. 选择页面顶部的“+创建用户 VPN 配置”，打开“创建新的用户 VPN 配置”页 。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/p2s1.jpg" alt-text="用户 VPN 配置":::

1. 在“创建新的用户 VPN 配置”页上填写以下字段：

   * **配置名称** - 这是需要按其来引用配置的名称。
   * **隧道类型** - 用于隧道的协议。
   * **根证书名称** - 证书的说明性名称。
   * **公用证书数据** - Base-64 编码的 X.509 证书数据。
  
1. 选择“创建”以创建配置。

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>使用点到站点网关创建中心

1. 在虚拟 WAN 下选择“中心”，然后选择“+新建中心”。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub1.jpg" alt-text="新中心":::

1. 在“创建虚拟中心”页上，请填写以下字段。

   * **区域** - 选择要在其中部署虚拟中心的区域。
   * **名称** - 输入要用于称呼虚拟中心的名称。
   * **中心专用地址空间** - 用 CIDR 表示法来表示的中心地址范围。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub2.jpg" alt-text="创建虚拟中心":::

1. 在“点到站点”选项卡上填写以下字段：

   * **网关缩放单元** - 表示用户 VPN 网关的聚合容量。
   * **点到站点配置** - 已在上一步中创建。
   * **客户端地址池** - 用于远程用户。
   * **自定义 DNS 服务器 IP**。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/hub-with-p2s.png" alt-text="包含点到站点的中心":::

1. 选择“查看 + 创建”。
1. 在“验证已通过”页上，选择“创建” 。

## <a name="specify-dns-server"></a><a name="dns"></a>指定 DNS 服务器

虚拟 WAN 用户 VPN 网关允许指定最多 5 个 DNS 服务器。 可以在创建中心的过程中对其进行配置，也可以在以后对其进行修改。 若要执行此操作，请找到虚拟中心。 在“用户 VPN (点到站点)”下，单击“配置”，然后在“自定义 DNS 服务器”文本框中输入 DNS 服务器 IP 地址 。

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="自定义 DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>下载 VPN 配置文件

使用 VPN 配置文件来配置客户端。

1. 在虚拟 WAN 的页面上，选择“用户 VPN 配置”。
2. 在页面顶部，选择“下载用户 VPN 配置”。下载 WAN 级配置时，系统会提供内置的基于流量管理器的用户 VPN 配置文件。 有关全局配置文件或基于中心的配置文件的详细信息，请参阅此[中心配置文件](https://docs.microsoft.com/azure/virtual-wan/global-hub-profile)。   使用全局配置文件可简化故障转移方案。

   如果中心由于某种原因而不可用，则该服务提供的内置流量管理可确保通过不同的中心连接到点到站点用户的 Azure 资源。 始终可以通过导航到特定的中心来下载特定于中心的 VPN 配置。 在“用户 VPN (点到站点)”下，下载虚拟中心用户 VPN 配置文件 。

1. 完成创建文件后，可以选择相应的链接下载该文件。
1. 使用此配置文件配置 VPN 客户端。

### <a name="configure-user-vpn-clients"></a>配置用户 VPN 客户端

使用下载的配置文件配置远程访问客户端。 每个操作系统的过程并不相同，请按照下面的正确说明操作：

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. 从官方网站下载并安装 OpenVPN 客户端。
1. 下载网关的 VPN 配置文件。 这可以通过 Azure 门户中的“用户 VPN 配置”选项卡或 PowerShell 中的 New-AzureRmVpnClientConfiguration 来完成。
1. 解压缩该配置文件。 从记事本中的 OpenVPN 文件夹中打开 vpnconfig.ovpn 配置文件。
1. 使用 base64 中的 P2S 客户端证书公钥填写 P2S 客户端证书部分。 在 PEM 格式的证书中，可以直接打开 .cer 文件并在证书标头之间复制 base64 密钥。 有关步骤，请参阅[如何导出证书以获取已编码的公钥](certificates-point-to-site.md)。
1. 使用 base64 中的 P2S 客户端证书私钥填写私钥部分。 有关步骤，请参阅[如何提取私钥](howto-openvpn-clients.md#windows)。
1. 不要更改任何其他字段。 使用客户端输入中的已填充的配置连接到 VPN。
1. 将 vpnconfig.ovpn 文件复制到 C:\Program Files\OpenVPN\config 文件夹。
1. 右键单击系统托盘中的 OpenVPN 图标，然后选择“连接”。

##### <a name="ikev2"></a>IKEv2

1. 根据 Windows 计算机的体系结构选择 VPN 客户端配置文件。 对于 64 位处理器体系结构，请选择“VpnClientSetupAmd64”安装程序包。 对于 32 位处理器体系结构，请选择“VpnClientSetupX86”安装程序包。
1. 双击所需的包进行安装。 如果看到弹出 SmartScreen，选择“详细信息”，然后选择“仍要运行” 。
1. 在客户端计算机上，导航到“网络设置”，并选择“VPN” 。 VPN 连接显示所连接到的虚拟网络的名称。
1. 尝试连接前，请验证客户端计算机上是否已安装客户端证书。 使用本机 Azure 证书身份验证类型时，客户端证书是身份验证必需的。 有关生成证书的详细信息，请参阅[生成证书](certificates-point-to-site.md)。 有关如何安装客户端证书的信息，请参阅[安装客户端证书](../vpn-gateway/point-to-site-how-to-vpn-client-install-azure-cert.md)。

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>查看虚拟 WAN

1. 导航到虚拟 WAN。
1. 在“概述”页上，地图中的每个点表示一个中心。
1. 在“中心和连接”部分，可以查看中心状态、站点、区域、VPN 连接状态和传入与传出字节数。

## <a name="clean-up-resources"></a><a name="cleanup"></a>清理资源

如果不再需要这些资源，可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组及其包含的所有资源。 将“myResourceGroup”替换为资源组的名称，并运行以下 PowerShell 命令：

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)页。

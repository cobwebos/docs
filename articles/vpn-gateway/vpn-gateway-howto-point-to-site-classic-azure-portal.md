---
title: 使用 P2S 将计算机连接到虚拟网络：证书身份验证： Azure 门户经典
titleSuffix: Azure VPN Gateway
description: 使用 Azure 门户创建典型的点到站点 VPN 网关连接。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: a66b76350da6f3b3804dac73a7aeb9f54d2e34eb
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91938366"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>使用证书身份验证（经典）配置点到站点连接

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

本文介绍如何创建具有点到站点连接的 VNet。 使用 "Azure 门户"，可以使用经典部署模型创建此 VNet。 此配置使用证书（不管是自签名的还是 CA 颁发的）来验证正在进行连接的客户端。 还可以使用以下文章中描述的选项，通过不同的部署工具或模型创建此配置：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure 门户（经典）](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

使用点到站点链接 (P2S) VPN 网关创建从单个客户端计算机到虚拟网络的安全连接。 要从远程位置连接到 VNet，可使用点到站点 VPN 连接。 如果只有一些客户端需要连接到 VNet，则可使用 P2S VPN 这种解决方案来代替站点到站点 VPN。 可通过从客户端计算机启动连接来建立 P2S VPN 连接。

> [!IMPORTANT]
> 经典部署模型仅支持 Windows VPN 客户端，并使用安全套接字隧道协议 (SSTP)，一种基于 SSL 的 VPN 协议。 为了支持非 Windows VPN 客户端，必须使用资源管理器部署模型创建 VNet。 除了 SSTP，资源管理器部署模型还支持 IKEv2 VPN。 有关详细信息，请参阅[关于 P2S 连接](point-to-site-about.md)。
>
>

![点到站点连接示意图](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>设置和要求

### <a name="requirements"></a>要求

点到站点证书身份验证连接需要以下各项。 本文中的步骤可帮助你创建它们。

* 动态 VPN 网关。
* 适用于根证书的公钥（.cer 文件），已上传到 Azure。 此公钥被视为可信证书，用于身份验证。
* 从根证书生成的客户端证书，安装在每个要连接的客户端计算机上。 此证书用于客户端身份验证。
* 必须生成 VPN 客户端配置包，并将其安装在每个进行连接的客户端计算机上。 客户端配置包配置本机 VPN 客户端，该客户端已经位于操作系统中，且具有连接到 VNet 所需的信息。

点到站点连接不需要 VPN 设备或面向公众的本地 IP 地址。 VPN 连接基于 SSTP（安全套接字隧道协议）创建。 在服务器端，我们支持 SSTP 1.0、1.1 和 1.2 版。 客户端决定要使用的版本。 对于 Windows 8.1 及更高版本，SSTP 默认使用 1.2。

有关详细信息，请参阅 [关于点到站点连接](point-to-site-about.md) 和 [常见问题解答](#faq)。

### <a name="example-settings"></a>示例设置

使用以下值创建测试环境，或参考这些值以更好地理解本文中的示例：

* **资源组：** TestRG
* **VNet 名称：** VNet1
* **地址空间：** 192.168.0.0/16 <br>对于此示例，我们只使用一个地址空间。 VNet 可以有多个地址空间。
* **子网名称：** 前端
* **子网地址范围：** 192.168.1.0/24
* **GatewaySubnet：** 10.11.255.0/27
* **区域：** (us) 美国东部
* **客户端地址空间：** 172.16.201.0/24 <br> 使用此点到站点连接连接到 VNet 的 VPN 客户端接收来自指定池的 IP 地址。
* **连接类型**：选择 " **点到站点**"。
* **GatewaySubnet 地址范围 (CIDR 块) ：** 192.168.200.0/24

开始之前，请确保拥有 Azure 订阅。 如果还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。

## <a name="create-a-virtual-network"></a><a name="vnet"></a>创建虚拟网络

如果已有一个 VNet，请验证这些设置是否与 VPN 网关设计兼容。 请特别注意任何可能与其他网络重叠的子网。

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>创建 VPN 网关

1. 导航到已创建的 VNet。
1. 在 VNet 页的 "设置" 下，选择 " **网关**"。 在 " **网关** " 页上，可以查看虚拟网络的网关。 此虚拟网络尚无网关。 单击 " **单击此处以添加连接和网关**" 便笺。
1. 在 " **配置 VPN 连接和网关** " 页上，选择以下设置：

   * 连接类型：点到站点
   * 客户端地址空间：添加 VPN 客户端在连接时从中接收 IP 地址的 IP 地址范围。 使用专用 IP 地址范围时，该范围不得与要通过其进行连接的本地位置重叠，也不得与连接到其中的 VNet 重叠。
1. 选中 " **不要在此时配置网关** " 复选框。 我们将创建一个网关。
1. 在页面底部，选择 " **下一步：网关 >**。
1. 在 " **网关** " 选项卡上，选择以下值：

   * **大小：** 大小是虚拟网络网关的网关 SKU。 在 Azure 门户中，默认 SKU 为“默认”****。 有关网关 Sku 的详细信息，请参阅 [关于 VPN 网关设置](vpn-gateway-about-vpn-gateway-settings.md#gwsku)。
   * **路由类型：** 对于点到站点配置，必须选择 " **动态** "。 静态路由将不起作用。
   * **网关子网：** 此字段已 autofilled。 不能更改名称。 如果尝试使用 PowerShell 或其他任何方法更改名称，则网关将无法正常工作。
   * **地址范围 (CIDR 块) ：** 尽管创建的网关子网最小可为/29，但建议通过选择 "至少"/28 或/27，创建包含更多地址的更大子网。 这样便可以留出足够多的地址，满足将来可能需要使用的其他配置。 处理网关子网时，请避免将网络安全组 (NSG) 关联到网关子网。 将网络安全组与此子网关联可能会导致 VPN 网关不再按预期方式工作。
1. 选择“查看 + 创建”以验证自己的设置  。
1. 验证通过后，选择 " **创建**"。 VPN 网关可能需要长达 45 分钟的时间才能完成，具体取决于所选网关 SKU。

## <a name="create-certificates"></a><a name="generatecerts"></a>创建证书

Azure 使用证书对点到站点 VPN 的 VPN 客户端进行身份验证。 请将根证书的公钥信息上传到 Azure， 然后即可将该公钥视为“可信”公钥**。 必须根据可信根证书生成客户端证书，并将其安装在每个客户端计算机的 Certificates-Current User\Personal\Certificates 证书存储中。 客户端连接到 VNet 时，使用证书进行身份验证。 

如果使用自签名证书，这些证书必须使用特定的参数创建。 可以按照 [PowerShell 和 Windows 10](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 的说明，创建自签名证书。 在使用自签名根证书以及从自签名根证书生成客户端证书时，必须按这些说明中的步骤操作，这一点很重要。 否则，创建的证书将与 P2S 连接不兼容，你将收到“连接错误”。

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>获取根证书的公钥 (.cer)

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>生成客户端证书

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>上传根证书 .cer 文件

创建网关之后，将可信根证书的 .cer 文件（包含公钥信息）上传到 Azure 服务器。 请勿上传根证书私钥。 上传证书后，Azure 使用该证书对已安装客户端证书（根据可信根证书生成）的客户端进行身份验证。 之后可根据需要上传更多可信根证书文件（最多 20 个）。

1. 导航到所创建的虚拟网络。
1. 在 " **设置**" 下，选择 " **点到站点连接**"。
1. 选择 " **管理证书**"。
1. 选择“上传”。
1. 在 " **上传证书** " 窗格中，选择文件夹图标，然后导航到要上传的证书。
1. 选择“上传”。
1. 成功上载证书后，可以在 "管理证书" 页上查看该证书。 可能需要选择 " **刷新** " 才能查看刚上传的证书。

## <a name="configure-the-client"></a>配置客户端

要通过点到站点 VPN 连接到 VNet，每个客户端都必须安装一个用于配置本机 Windows VPN 客户端的包。 配置包使用连接到虚拟网络所需的设置配置本机 Windows VPN 客户端。

只要版本与客户端的体系结构匹配，就可以在每台客户端计算机上使用相同的 VPN 客户端配置包。 有关支持的客户端操作系统列表，请参阅 [关于点到站点连接](point-to-site-about.md) 和 [常见问题解答](#faq)。

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>生成和安装 VPN 客户端配置包

1. 导航到 VNet 的 **点到站点连接** 设置。
1. 在页面顶部，选择与要在其中安装的客户端操作系统对应的下载包：

   * 对于64位客户端，选择 " **VPN 客户端 (64 位) **。
   * 对于32位客户端，选择 " **VPN 客户端 (32 位) **。

1. Azure 会生成一个包，其中包含客户端所需的特定设置。 每次对 VNet 或网关进行更改时，需要下载新的客户端配置包，并将其安装在客户端计算机上。
1. 生成包后，选择 " **下载**"。
1. 在客户端计算机上安装客户端配置包。 在安装时，如果看到一个弹出窗口，指出 Windows 保护了你的计算机，请选择 " **详细信息**"，然后选择 " **仍要运行**"。 也可将要安装的包保存在其他客户端计算机上。

### <a name="install-a-client-certificate"></a>安装客户端证书

在此练习中，当您生成客户端证书时，该证书会自动安装在您的计算机上。 若要从其他客户端计算机（而不是用于生成客户端证书的计算机）创建 P2S 连接，则必须在该计算机上安装生成的客户端证书。

安装客户端证书时，需要使用导出客户端证书时创建的密码。 通常，只需双击证书即可安装。 有关详细信息，请参阅 [安装已导出的客户端证书](vpn-gateway-certificates-point-to-site.md#install)。

## <a name="connect-to-your-vnet"></a>连接到 VNet

>[!NOTE]
>在要从其进行连接的客户端计算机上，你必须拥有管理员权限。
>

1. 在客户端计算机上，请参阅 "VPN 设置"。
1. 选择所创建的 VPN。 如果使用了示例设置，则连接将被标记为 **Group TestRG VNet1**。
1. 选择“连接”  。
1. 在 "Microsoft Azure 虚拟网络" 框中，选择 " **连接**"。 如果出现有关证书的弹出消息，请选择 " **继续** " 以使用提升的权限，选择 **"是"** 以接受配置更改。
1. 当连接成功时，你将看到 **连接** 的通知。

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>验证 VPN 连接

1. 验证 VPN 连接是否激活。 在客户端计算机上打开提升的命令提示符，并运行 ipconfig/all****。
1. 查看结果。 请注意，收到的 IP 地址是点到站点连接地址范围中的一个地址，该范围是你在创建 VNet 时指定的。 结果应类似于以下示例：

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>连接到虚拟机

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>添加或删除受信任的根证书

可以在 Azure 中添加和删除受信任的根证书。 删除根证书时，具有从该根生成的证书的客户端不能再进行身份验证和连接。 为了让这些客户端再次进行身份验证并连接，必须安装新的客户端证书，该证书由 Azure 信任的根证书生成。

### <a name="add-a-trusted-root-certificate"></a>添加受信任的根证书

你可以使用添加第一个受信任的根证书时所用的相同过程，将最多20个受信任的根证书 .cer 文件添加到 Azure。

### <a name="remove-a-trusted-root-certificate"></a>删除受信任的根证书

1. 在 VNet 页的 " **点到站点连接** " 部分中，选择 " **管理证书**"。
1. 选择要删除的证书旁边的省略号，然后选择 " **删除**"。

## <a name="to-revoke-a-client-certificate"></a>吊销客户端证书

如有必要，可以吊销客户端证书。 证书吊销列表用于选择性地拒绝基于单个客户端证书的点到站点连接。 此方法不同于删除可信根证书。 如果从 Azure 中删除受信任的根证书 .cer，它会吊销由吊销的根证书生成/签名的所有客户端证书的访问权限。 如果吊销客户端证书而非根证书，则可继续使用从根证书生成的其他证书，以便进行点到站点连接所需的身份验证。

常见的做法是使用根证书管理团队或组织级别的访问权限，并使用吊销的客户端证书针对单个用户进行精细的访问控制。

可以通过将指纹添加到吊销列表来吊销客户端证书。

1. 检索客户端证书指纹。 有关详细信息，请参阅[如何：检索证书的指纹](https://msdn.microsoft.com/library/ms734695.aspx)。
1. 将信息复制到文本编辑器，删除其中的空格，使之成为连续的字符串。
1. 导航到 **点到站点 VPN 连接**，然后选择 " **管理证书**"。
1. 选择“吊销列表”，打开“吊销列表”页********。
1. 在“指纹”页中，将证书指纹以连续文本行的形式进行粘贴，不留空格****。
1. 选择 " **+ 添加到列表** "，将指纹添加到 (CRL) 的证书吊销列表。

更新完成后，不再可以使用证书来连接。 客户端在尝试使用此证书进行连接时，会收到一条消息，指出证书不再有效。

## <a name="faq"></a><a name="faq"></a>常见问题解答

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>后续步骤

* 连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/)。

* 要详细了解网络和 Linux 虚拟机，请参阅 [Azure 和 Linux VM 网络概述](../virtual-machines/linux/network-overview.md)。

* 有关 P2S 故障排除信息，请参阅[排查 Azure 点到站点连接问题](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)。

---
title: "使用点到站点连接将计算机连接到 Azure 虚拟网络：PowerShell | Microsoft 文档"
description: "通过创建点到站点 VPN 网关连接，安全地将计算机连接到 Azure 虚拟网络。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3eddadf6-2e96-48c4-87c6-52a146faeec6
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: cf72197aba2c6e6c7a51f96d1161cf1fbe88a0c5
ms.openlocfilehash: fe3bb0a5faee806e7956acba23c22b9aefd1f0a8
ms.lasthandoff: 02/18/2017


---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-powershell"></a>使用 PowerShell 配置与 VNet 的点到站点连接
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 门户](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [经典 - Azure 门户](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

通过点到站点 (P2S) 配置，可以使单台客户端计算机与虚拟网络建立安全的连接。 如果要从远程位置（例如，从家里或会议室）连接到 VNet，或者只有少数几台客户端计算机需要连接到虚拟网络，P2S 连接将非常有用。

点到站点连接不需要 VPN 设备或面向公众的 IP 地址即可运行。 可通过从客户端计算机启动连接来建立 VPN 连接。 有关点到站点连接的详细信息，请参阅本文末尾的[点到站点常见问题解答](#faq)。 

本文介绍如何在 Resource Manager 部署模型中使用 PowerShell 通过点到站点连接来创建 VNet。

### <a name="deployment-models-and-methods-for-p2s-connections"></a>P2S 连接的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表显示 P2S 配置的两个部署模型和可用部署方法。 当有配置步骤相关的文章发布时，我们会直接从此表格链接到该文章。

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="basic-workflow"></a>基本工作流
![将计算机连接到 Azure VNet - 点到站点连接示意图](./media/vpn-gateway-howto-point-to-site-rm-ps/point-to-site-diagram.png)

在此方案中，你将使用点到站点连接创建虚拟网络。 这些说明还将帮助用户生成此配置所需的证书。 P2S 连接由以下项组成：具有 VPN 网关的 VNet、根证书 .cer 文件（公钥）、客户端证书和客户端上的 VPN 配置。 

针对此配置使用以下值。 在本文的第 [1](#declare) 部分中设置了变量。 可以将这些步骤用作演练并使用这些值而不更改它们，也可以更改这些值以反映自己的环境。 

### <a name="a-nameexampleaexample-values"></a><a name="example"></a>示例值
* **名称：VNet1**
* **地址空间：192.168.0.0/16** 和 **10.254.0.0/16**<br>本示例中使用了多个地址空间，说明此配置可与多个地址空间一起工作。 但是，对于此配置，多个地址空间并不必要。
* **子网名称：FrontEnd**
  * **子网地址范围：192.168.1.0/24**
* **子网名称：BackEnd**
  * **子网地址范围：10.254.1.0/24**
* **子网名称：GatewaySubnet**<br>要使 VPN 网关正常工作，必须使用子网名称 GatewaySubnet。
  * **子网地址范围：192.168.200.0/24** 
* **VPN 客户端地址池：172.16.201.0/24**<br>使用此点到站点连接连接到 VNet 的 VPN 客户端接收来自 VPN 客户端地址池的 IP 地址。
* **订阅：** 如果有多个订阅，请验证是否正在使用正确的订阅。
* **资源组：TestRG**
* **位置：美国东部**
* **DNS 服务器**：要用于名称解析的 DNS 服务器的 IP 地址。
* **GW 名称：Vnet1GW**
* **公共 IP 名称：VNet1GWPIP**
* **VpnType：RouteBased**

## <a name="before-beginning"></a>开始之前
* 确保你拥有 Azure 订阅。 如果你还没有 Azure 订阅，可以激活 [MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)或注册获取[免费帐户](https://azure.microsoft.com/pricing/free-trial)。
* 安装最新版本的 Azure Resource Manager PowerShell cmdlet。 有关安装 PowerShell cmdlet 的详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。 使用 PowerShell 进行此配置时，请确保以管理员身份运行。 

## <a name="a-namedeclareapart-1---log-in-and-set-variables"></a><a name="declare"></a>第 1 部分 - 登录并设置变量
在本部分中，将登录并声明用于此配置的值。 声明的值将在示例脚本中使用。 更改这些值以反映自己的环境。 也可以使用声明的值完成这些步骤作为练习。

1. 在 PowerShell 控制台中，登录到你的 Azure 帐户。 该 cmdlet 将提示你提供 Azure 帐户的登录凭据。 登录后它会下载你的帐户设置，以便这些信息可供 Azure PowerShell 使用。
   
        Login-AzureRmAccount 
2. 获取 Azure 订阅的列表。
   
        Get-AzureRmSubscription
3. 指定要使用的订阅。 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"
4. 声明要使用的值。 使用以下示例，在必要时将值替换为自己的值。
   
        $VNetName  = "VNet1"
        $FESubName = "FrontEnd"
        $BESubName = "Backend"
        $GWSubName = "GatewaySubnet"
        $VNetPrefix1 = "192.168.0.0/16"
        $VNetPrefix2 = "10.254.0.0/16"
        $FESubPrefix = "192.168.1.0/24"
        $BESubPrefix = "10.254.1.0/24"
        $GWSubPrefix = "192.168.200.0/26"
        $VPNClientAddressPool = "172.16.201.0/24"
        $RG = "TestRG"
        $Location = "East US"
        $DNS = "8.8.8.8"
        $GWName = "VNet1GW"
        $GWIPName = "VNet1GWPIP"
        $GWIPconfName = "gwipconf"

## <a name="a-nameconfigurevnetapart-2---configure-a-vnet"></a><a name="ConfigureVNet"></a>第 2 部分 - 配置 VNet
1. 创建资源组。
   
        New-AzureRmResourceGroup -Name $RG -Location $Location
2. 为虚拟网络创建子网配置，并将其命名为 FrontEnd、BackEnd 和 GatewaySubnet。 这些前缀必须是已声明的 VNet 地址空间的一部分。
   
        $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
        $besub = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
        $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
3. 创建虚拟网络。 指定的 DNS 服务器应该是可以解析所连接的资源名称的 DNS 服务器。 在此示例中，使用公共 IP 地址。 请务必使用自己的值。
   
        New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer $DNS
4. 指定所创建的虚拟网络的变量。
   
        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
5. 请求动态分配的公共 IP 地址。 网关需要此 IP 地址才能正常运行。 稍后要将网关连接到网关 IP 配置。
   
        $pip = New-AzureRmPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

## <a name="a-namecertificatesapart-3---certificates"></a><a name="Certificates"></a>第 3 部分 - 证书
Azure 使用证书对点到站点 VPN 的 VPN 客户端进行身份验证。 可从企业证书解决方案生成的根证书或自签名根证书中将公共证书数据（非密钥）导出为 Base-64 编码的 X.509 .cer 文件。 然后，将公共证书数据从根证书导入 Azure。 此外，需要从客户端的根证书生成客户端证书。 要使用 P2S 连接连接到虚拟网络的每个客户端必须安装从根证书生成的客户端证书。

### <a name="a-namecerastep-1---obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>步骤 1 - 获取根证书的 .cer 文件
将需要获取要使用的根证书的公共证书数据。

* 如果使用企业证书系统，请获取要使用的根证书的 .cer 文件。 
* 如果使用的不是企业证书解决方案，则需要生成自签名根证书。 创建供 P2S 使用的自签名证书时，建议使用 makecert 方法。 虽然可以使用 PowerShell 创建自签名证书，但是使用 PowerShell 生成的证书不包含进行 P2S 连接所需的字段。 有关适用于 Windows 10 的步骤，请参阅 [为点到站点配置使用自签名根证书](vpn-gateway-certificates-point-to-site.md)。

1. 若要从证书中获取 .cer 文件，请打开 **certmgr.msc** 并查找根证书。 右键单击自签名根证书，单击“所有任务”，然后单击“导出”。 此操作将打开“证书导出向导”。
2. 在向导中，单击“下一步”，选择“否，不导出私钥”，然后单击“下一步”。
3. 在“导出文件格式”页上，选择“Base-64 编码的 X.509 (.CER)”。 然后单击“下一步”。 
4. 在“要导出的文件”中，“浏览”到要将证书导出的目标位置。 在“文件名”中，为证书文件命名。 。
5. 单击“完成”导出证书。

### <a name="a-namegenerateastep-2---generate-the-client-certificate"></a><a name="generate"></a>步骤 2 - 生成客户端证书
接下来，生成客户端证书。 可以为要连接的每个客户端生成唯一证书，也可以在多台客户端上使用同一个证书。 生成唯一客户端证书的优点是能够在需要时撤销单个证书。 如果所有客户端都使用同一个客户端证书，那么当需要为一台客户端撤销证书时，就需要为使用该证书进行身份验证的所有客户端生成并安装新的证书。 本练习中，稍后将在每台客户端计算机上安装客户端证书。


####<a name="enterprise-certificate"></a>企业证书
- 如果使用的是企业证书解决方案，请使用通用名称值格式 'name@yourdomain.com', 生成客户端证书，而不要使用“域名\用户名”格式。
- 请确保颁发的客户端证书基于“用户”证书模板，该模板使用“客户端身份验证”作为使用列表中的第一项，而不是智能卡登录等。可以通过双击客户端证书，然后查看“详细信息”>“增强型密钥用法”来检查证书。

####<a name="self-signed-certificate"></a>自签名证书 
如果使用自签名证书，请参阅 [为点到站点配置使用自签名根证书](vpn-gateway-certificates-point-to-site.md) ，生成客户端证书。

### <a name="a-nameexportclientcertastep-3---export-the-client-certificate"></a><a name="exportclientcert"></a>步骤 3 - 导出客户端证书
进行身份验证需要客户端证书。 生成客户端证书后，将其导出。 稍后将在每台客户端计算机上安装导出的客户端证书。

1. 若要导出客户端证书，可以使用 *certmgr.msc*。 右键单击要导出的客户端证书，单击“所有任务”，然后单击“导出”。
2. 导出带私钥的客户端证书。 这是一个 *.pfx* 文件。 请确保记录或记住为此证书设置的密码（密钥）。

### <a name="a-nameuploadastep-4---upload-the-root-certificate-cer-file"></a><a name="upload"></a>步骤 4 - 上载根证书 .cer 文件
为证书名称声明变量，将值替换为自己的值：

        $P2SRootCertName = "Mycertificatename.cer"

将根证书的公共证书数据添加到 Azure。 最多可以上载 20 个根证书的文件。 请勿将根证书私钥上传到 Azure。 上载 .Cer 文件后，Azure 将使用它来对连接到虚拟网络的客户端进行身份验证。 

将文件路径替换为自己的路径，然后运行 cmdlet。

        $filePathForCert = "C:\cert\Mycertificatename.cer"
        $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
        $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
        $p2srootcert = New-AzureRmVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64

## <a name="a-namecreategatewayapart-4---create-the-vpn-gateway"></a><a name="creategateway"></a>第 4 部分 - 创建 VPN 网关
为 VNet 配置和创建虚拟网络网关。 -GatewayType 必须是 **Vpn**，且 -VpnType 必须是 **RouteBased**。 此步骤可能最多需要 45 分钟才能完成。

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
        -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
        -VpnType RouteBased -EnableBgp $false -GatewaySku Standard `
        -VpnClientAddressPool $VPNClientAddressPool -VpnClientRootCertificates $p2srootcert

## <a name="a-nameclientconfigapart-5---download-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>第 5 部分 - 下载 VPN 客户端配置包
使用 P2S 连接到 Azure 的客户端必须已安装客户端证书和 VPN 客户端配置包。 Windows 客户端有可用的 VPN 客户端配置包。

VPN 客户端包中含有用于配置 Windows 内置 VPN 客户端软件的配置信息。 该程序包不安装额外的软件。 这些设置特定于要连接到的虚拟网络。 有关支持的客户端操作系统列表，请参阅本文末尾的[点到站点连接常见问题解答](#faq)。

1. 创建网关后，可下载客户端配置包。 此示例下载 64 位客户端包。 若要下载 32 位客户端，将“Amd64”替换为“x86”。 还可以使用 Azure 门户下载 VPN 客户端。
   
        Get-AzureRmVpnClientPackage -ResourceGroupName $RG `
        -VirtualNetworkGatewayName $GWName -ProcessorArchitecture Amd64
2. PowerShell cmdlet 将返回 URL 链接。 以下是返回的 URL 的外观示例：
   
        "https://mdsbrketwprodsn1prod.blob.core.windows.net/cmakexe/4a431aa7-b5c2-45d9-97a0-859940069d3f/amd64/4a431aa7-b5c2-45d9-97a0-859940069d3f.exe?sv=2014-02-14&sr=b&sig=jSNCNQ9aUKkCiEokdo%2BqvfjAfyhSXGnRG0vYAv4efg0%3D&st=2016-01-08T07%3A10%3A08Z&se=2016-01-08T08%3A10%3A08Z&sp=r&fileExtension=.exe"
3. 复制并粘贴返回到 Web 浏览器的链接以下载包。 然后将该包安装在客户端计算机上。 如果出现 SmartScreen 弹出窗口，依次单击“详细信息”、“仍要运行”以安装包。
4. 在客户端计算机上，导航到“网络设置”，然后单击“VPN”。 此时将看到列出的连接。 它将显示要连接到的虚拟网络的名称，如以下示例所示： 
   
    ![VPN 客户端](./media/vpn-gateway-howto-point-to-site-rm-ps/vpn.png)

## <a name="a-nameclientcertificateapart-6---install-the-client-certificate"></a><a name="clientcertificate"></a>第 6 部分 - 安装客户端证书
每个客户端计算机必须有一个客户端证书才能执行身份验证。 安装客户端证书时，需要使用导出客户端证书时创建的密码。

1. 将 .pfx 文件复制到客户端计算机。
2. 双击 .pfx 文件将它安装。 请勿修改安装位置。

## <a name="a-nameconnectapart-7---connect-to-azure"></a><a name="connect"></a>第 7 部分：连接到 Azure
1. 要连接到 VNet，请在客户端计算机上，导航到 VPN 连接，找到创建的 VPN 连接。 其名称与虚拟网络的名称相同。 单击“连接”。 可能会出现与使用证书相关的弹出消息。 如果出现此消息，请单击“继续”  以使用提升的权限。 
2. 在“连接”状态页上，单击“连接”以启动连接。 如果看到“选择证书”屏幕，请确保所显示的客户端证书是要用来连接的证书。 如果不是，请使用下拉箭头选择正确的证书，然后单击“确定”。
   
    ![VPN 客户端连接到 Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. 现在应已建立连接。
   
    ![已建立连接](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

> [!NOTE]
> 如果使用的是通过企业 CA 解决方案颁发的证书，并且无法进行身份验证，请检查客户端证书上的身份验证顺序。 可以通过双击客户端证书，并转到“详细信息”>“增强型密钥用法”来检查身份验证列表顺序。 请确保此列表显示的第一项是“客户端身份验证”。 如果不是，则需要基于将“客户端身份验证”作为列表中第一项的用户模板颁发客户端证书。 
>
>

## <a name="a-nameverifyapart-8---verify-your-connection"></a><a name="verify"></a>第 8 部分 - 验证连接
1. 若要验证你的 VPN 连接是否处于活动状态，请打开提升的命令提示符，然后运行 *ipconfig/all*。
2. 查看结果。 请注意，你收到的 IP 地址是在配置中指定的点到站点 VPN 客户端地址池中的地址之一。 结果应大致如下所示：
   
        PPP adapter VNet1:
            Connection-specific DNS Suffix .:
            Description.....................: VNet1
            Physical Address................:
            DHCP Enabled....................: No
            Autoconfiguration Enabled.......: Yes
            IPv4 Address....................: 172.16.201.3(Preferred)
            Subnet Mask.....................: 255.255.255.255
            Default Gateway.................:
            NetBIOS over Tcpip..............: Enabled

## <a name="a-nameaddremovecertato-add-or-remove-a-trusted-root-certificate"></a><a name="addremovecert"></a>添加或删除受信任的根证书
证书用于对点到站点 VPN 的 VPN 客户端进行身份验证。 以下步骤将引导用户添加和删除根证书。 在将 Base64 编码 X.509 (.cer) 文件添加到 Azure 时，则是在告诉 Azure 信任该文件所代表的根证书。 

可以使用 PowerShell 或在 Azure 门户中添加或删除受信任的根证书。 若要使用 Azure 门户执行此操作，请转到“虚拟网络网关”>“设置”>“点到站点配置”>“根证书”。 以下步骤将引导用户使用 PowerShell 完成这些任务。 

### <a name="add-a-trusted-root-certificate"></a>添加受信任的根证书
最多可以将 20 个受信任的根证书 .cer 文件添加到 Azure。 请遵循以下步骤来添加根证书。

1. 创建并准备要添加到 Azure 的新根证书。 将公钥导出为 Base-64 编码 X.509 (.CER) 文件并使用文本编辑器打开它。 然后，仅复制下面所示部分。 
   
    复制值，如以下示例所示：
   
    ![证书](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

    > [!NOTE]
    > 复制证书数据时，请确保将文本复制为一个无回车符或换行符的连续行。 可能需要在文本编辑器中将视图修改为“显示符号/显示所有字符”以查看回车符和换行符。                                                                                                                                                                            
    >


2. 将证书名称和密钥信息指定为变量。 将该信息替换为自己的信息，如以下示例所示：
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
3. 添加新的根证书。 一次只能添加一个证书。
   
        Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
4. 可以使用以下 cmdlet 来验证是否已已正确添加新证书。
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

### <a name="to-remove-a-trusted-root-certificate"></a>删除受信任的根证书
可以从 Azure 中删除受信任的根证书。 删除受信任的根证书后，从该根证书生成的客户端证书将不再能够通过点到站点配置连接到 Azure。 如果希望客户端连接，需要安装新客户端证书，该证书应从 Azure 中受信任的证书生成。

1. 若要删除受信任的根证书，请修改以下示例：
   
    声明变量。
   
        $P2SRootCertName2 = "ARMP2SRootCert2.cer"
        $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
2. 删除证书。
   
        Remove-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
3. 使用以下 cmdlet 来验证是否已已成功删除证书。 
   
        Get-AzureRmVpnClientRootCertificate -ResourceGroupName "TestRG" `
        -VirtualNetworkGatewayName "VNet1GW"

## <a name="a-namerevokeato-manage-the-list-of-revoked-client-certificates"></a><a name="revoke"></a>管理吊销的客户端证书列表
你可以吊销客户端证书。 证书吊销列表可让你选择性地拒绝基于单个客户端证书的点到站点连接。 如果从 Azure 中删除根证书 .cer，它会撤销由吊销的根证书生成/签名的所有客户端证书的访问权限。 如果要吊销特定客户端证书（而不是根证书），可以执行该操作。 如果那样做，已从根证书生成的其他证书将仍然有效。

常见的做法是使用根证书管理团队或组织级别的访问权限，然后使用吊销的客户端证书针对单个用户进行精细的访问控制。

### <a name="revoke-a-client-certificate"></a>吊销客户端证书
1. 获取要吊销的客户端证书的指纹。
   
        $RevokedClientCert1 = "ClientCert1"
        $RevokedThumbprint1 = "?ef2af033d0686820f5a3c74804d167b88b69982f"
2. 将指纹添加到吊销的指纹列表。
   
        Add-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
        -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
3. 确认指纹已添加到证书吊销列表。 必须一次添加一个指纹。
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG

### <a name="reinstate-a-client-certificate"></a>恢复客户端证书
可以通过从吊销的客户端证书列表中删除指纹来恢复客户端证书。

1. 从吊销的客户端证书指纹列表中删除指纹。
   
       Remove-AzureRmVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
       -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
2. 检查指纹是否已从吊销列表中删除。
   
        Get-AzureRmVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
## <a name="a-namefaqapoint-to-site-faq"></a><a name="faq"></a>点到站点常见问题解答

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-point-to-site-faq-include.md)]

## <a name="next-steps"></a>后续步骤
连接完成后，即可将虚拟机添加到虚拟网络。 有关详细信息，请参阅[虚拟机](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)。



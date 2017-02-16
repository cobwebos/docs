---
title: "使用 makecert 为点到站点虚拟网络跨界连接创建自签名证书 | Microsoft 文档"
description: "本文包含在 Windows 10 上使用 makecert 创建自签名证书的步骤。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 64c6e8fa5288ce9a25bcb434217511425aebfe21
ms.openlocfilehash: beb873c99d956eafebecc69f9afc480c558efabb


---
# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>为点到站点连接使用自签名证书
本文介绍如何使用 **makecert** 创建自签名证书，然后从自签名证书中生成客户端证书。 对于 P2S 连接，生成证书的首选方法是使用企业证书解决方案。 请确保使用常见名称值格式 'name@yourdomain.com', 颁发客户端证书，不要使用“NetBIOS 域名\用户名”的格式。

如果没有企业解决方案，则需要自签名证书以允许 P2S 客户端连接到虚拟网络。 虽然可以使用 PowerShell 创建证书，但使用 PowerShell 生成的证书不包含 Azure 进行 P2S 身份验证所需的必需字段。 已对 makecert 进行了验证，以创建与 P2S 连接相兼容的证书。 在 P2S 配置中 Makecert 并未弃用。

## <a name="create-a-self-signed-certificate"></a>创建自签名证书
以下步骤将演示如何使用 makecert 创建自签名证书。 这些步骤并非特定于部署模型。 它们同样适用于 Resource Manager 和经典部署模型。

### <a name="to-create-a-self-signed-certificate"></a>创建自签名证书
1. 从运行 Windows 10 的计算机中下载并安装[用于 Windows 10 的 Windows 软件开发包 (SDK)](https://dev.windows.com/en-us/downloads/windows-10-sdk)。
2. 安装之后，可以在以下路径中找到 makecert.exe 实用工具：C:\Program Files (x86)\Windows Kits\10\bin\<arch>。 
   
    示例：`C:\Program Files (x86)\Windows Kits\10\bin\x64`
3. 接下来，在计算机上的“个人”证书存储中创建并安装证书。 以下示例将创建一个相应的 *.cer* 文件，在配置 P2S 时需要将此文件上传到 Azure。 以管理员身份运行以下命令。 使用想要用于证书的名称替换 *ARMP2SRootCert* 和* ARMP2SRootCert.cer*。<br><br>该证书位于“证书”-“当前用户\个人\证书”中。
   
        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"

### <a name="a-namerootpublickeyato-obtain-the-public-key"></a><a name="rootpublickey"></a>获取公钥
根证书的公钥作为点到站点连接 VPN 网关配置的一部分上载到 Azure。

1. 若要获取证书 .cer 文件，请打开 **certmgr.msc**。 右键单击自签名根证书，单击“所有任务”，然后单击“导出”。 此操作将打开“证书导出向导”。
2. 在向导中，单击“下一步”，选择“否，不导出私钥”，然后单击“下一步”。
3. 在“导出文件格式”页上，选择“Base-64 编码的 X.509 (.CER)”。 然后单击“下一步”。 
4. 在“要导出的文件”中，“浏览”到要将证书导出的目标位置。 在“文件名”中，为证书文件命名。 。
5. 单击“完成”导出证书。

### <a name="export-the-self-signed-certificate-optional"></a>导出自签名证书（可选）
可能需要导出自签名证书，并将它安全存储。 如果需要，可以稍后在另一台计算机上安装此自签名证书，然后生成更多客户端证书，或导出另一个 .cer 文件。 已安装客户端证书并设置适当的 VPN 客户端设置的任何计算机，都可以通过 P2S 连接到虚拟网络。 因此，需要确保仅在需要时生成和安装客户端证书，并且需要安全地存储此自签名证书。

若要将自签名证书导出为 .pfx，请选择根证书，然后使用[导出客户端证书](#clientkey)中所述的步骤导出。

## <a name="create-and-install-client-certificates"></a>创建和安装客户端证书
不要直接在客户端计算机上安装自签名的证书。 您需要从自签名证书生成客户端证书。 然后导出客户端证书并安装到客户端计算机上。 以下步骤并非特定于部署模型。 它们同样适用于 Resource Manager 和经典部署模型。

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>第 1 部分 - 从自签名证书生成客户端证书
以下步骤将演示从自签名证书生成客户端证书的其中一种方法。 可以从相同证书生成多个客户端证书。 然后每个客户端证书可以导出及安装在客户端计算机上。 

1. 在用于创建自签名证书的同一台计算机上，以管理员身份打开命令提示符。
2. 在本示例中，"ARMP2SRootCert" 是指生成的自签名证书。 
   
   * 将 *"ARMP2SRootCert"* 更改为生成客户端证书所用的自签名根证书。 
   * 将 *ClientCertificateName* 更改为生成客户端证书所用的名称。 

    修改并运行示例，生成客户端证书。 如果未经修改就运行以下示例，个人证书存储中将有一个从根证书 ARMP2SRootCert 生成的客户端证书，名为 ClientCertificateName。

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

1. 所有证书都存储在计算机上的“证书”-“当前用户\个人\证书”存储中。 你可以按照此过程生成所需数目的客户端证书。

### <a name="a-nameclientkeyapart-2---export-a-client-certificate"></a><a name="clientkey"></a>第 2 部分 - 导出客户端证书
1. 若要导出客户端证书，请打开 **certmgr.msc**。 右键单击要导出的客户端证书，单击“所有任务”，然后单击“导出”。 此操作将打开“证书导出向导”。
2. 在向导中，单击“**下一步**”，选择“**是，导出私钥**”，然后单击“**下一步**”。
3. 在“**导出文件格式**”页上，可以保留选择默认值。 。 
4. 在“**安全性**”页上，必须保护私钥。 如果选择使用密码，请务必记下或牢记为此证书设置的密码。 。
5. 在“要导出的文件”中，“浏览”到要将证书导出的目标位置。 在“文件名”中，为证书文件命名。 。
6. 单击“完成”导出证书。    

### <a name="part-3---install-a-client-certificate"></a>第 3 部分 - 安装客户端证书
想要使用点到站点连接与虚拟网络连接的每个客户端都必须安装客户端证书。 除了必需的 VPN 配置包以外，还必须安装此证书。 以下步骤将演示如何手动安装客户端证书。

1. 找到 *.pfx* 文件并将其复制到客户端计算机。 在客户端计算机上，双击 *.pfx* 文件以进行安装。 将“**存储位置**”保留为“**当前用户**”，然后单击“**下一步**”。
2. 在“要导入的**文件**”页上，不要进行任何更改。 单击“下一步”。
3. 在“**私钥保护**”页上，如果使用了密码，请输入证书的密码，或验证安装证书的安全主体是否正确，然后单击“**下一步**”。
4. 在“**证书存储**”页上，保留默认位置，然后单击“**下一步**”。
5. 单击“**完成**”。 在证书安装的“**安全警告**”上，单击“**是**”。 可随时单击“是”，因为证书已生成。 现已成功导入证书。

## <a name="next-steps"></a>后续步骤
继续使用点到站点配置。 

* 有关 **Resource Manager** 部署模型步骤，请参阅[使用 PowerShell 配置与 VNet 的点到站点连接](vpn-gateway-howto-point-to-site-rm-ps.md)。 
* 有关**经典**部署模型步骤，请参阅[使用经典门户配置与 VNet 的点到站点 VPN 连接](vpn-gateway-point-to-site-create.md)。




<!--HONumber=Jan17_HO3-->



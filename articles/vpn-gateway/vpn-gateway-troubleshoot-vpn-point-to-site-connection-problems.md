---
title: 排查 Azure 点到站点连接问题 | Microsoft Docs
description: 了解如何排查点到站点连接问题。
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: cd89c41b43be1da339ca7dcc64110e7145a93903
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857324"
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>故障排除：Azure 点到站点连接问题

本文列举了可能会出现的常见点到站点连接问题。 此外，还介绍了这些问题的可能原因和解决方案。

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN 客户端错误：找不到证书

### <a name="symptom"></a>症状

尝试使用 VPN 客户端连接到 Azure 虚拟网络时，看到以下错误消息：

**找不到可用于此可扩展身份验证协议的证书。(错误 798)**

### <a name="cause"></a>原因

如果 **Certificates - Current User\Personal\Certificates** 中缺少客户端证书，便会发生此问题。

### <a name="solution"></a>解决方案

若要解决该问题，请执行以下步骤：

1. 打开证书管理器：单击“开始”，键入“管理计算机证书”，然后单击搜索结果中的“管理计算机证书”。

2. 请确保已正确的位置安装下列证书：

    | 证书 | 位置 |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-GUID.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-GUID.cloudapp.net、AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

3. 转到 Users\<UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>，在用户和计算机的存储上手动安装证书（*.cer 文件）。

若要详细了解如何安装客户端证书，请参阅[为点到站点连接生成并导出证书](vpn-gateway-certificates-point-to-site.md)。

> [!NOTE]
> 导入客户端证书时，请勿选择“启用强私钥保护”选项。

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN 客户端错误：接收到的消息异常，或格式不正确

### <a name="symptom"></a>症状

尝试使用 VPN 客户端连接到 Azure 虚拟网络时，看到以下错误消息：

**收到意外或格式不当的消息。(错误 0x80090326)**

### <a name="cause"></a>原因

如果下列一项条件为 true，则会发生此问题：

- 网关子网上使用默认路由的用户定义路由 (UDR) 设置不正确。
- 根证书公钥未上传到 Azure VPN 网关。 
- 密钥已损坏或过期。

### <a name="solution"></a>解决方案

若要解决该问题，请执行以下步骤：

1. 删除网关子网上的 UDR。 请确保 UDR 正确地转发所有流量。
2. 请在 Azure 门户中检查根证书的状态，确定它是否已吊销。 如果未吊销，请尝试删除并重新上传根证书。 有关详细信息，请参阅[创建证书](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)。

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN 客户端错误：已处理证书链，但被终止 

### <a name="symptom"></a>症状 

尝试使用 VPN 客户端连接到 Azure 虚拟网络时，看到以下错误消息：

**已处理证书链，但是在不受信任提供程序信任的根证书中终止。**

### <a name="solution"></a>解决方案

1. 请确保已正确的位置安装下列证书：

    | 证书 | 位置 |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-GUID.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-GUID.cloudapp.net、AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

2. 如果相应位置上已有证书，请尝试删除并重新安装证书。 **azuregateway-*GUID*.cloudapp.net** 证书位于从 Azure 门户下载的 VPN 客户端配置包中。 可以使用文件存档程序从配置包中提取文件。

## <a name="file-download-error-target-uri-is-not-specified"></a>文件下载错误：未指定目标 URI

### <a name="symptom"></a>症状

看到以下错误消息：

**文件下载错误。未指定目标 URI。**

### <a name="cause"></a>原因 

导致此问题发生的原因是网关类型不正确。 

### <a name="solution"></a>解决方案

VPN 网关类型必须是 **VPN**，VPN 类型必须是 **RouteBased**。

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN 客户端错误：Azure VPN 自定义脚本失败 

### <a name="symptom"></a>症状

尝试使用 VPN 客户端连接到 Azure 虚拟网络时，看到以下错误消息：

**用于更新路由表的自定义脚本失败。(错误 8007026f)**

### <a name="cause"></a>原因

如果尝试使用快捷方式启用站点到点 VPN 连接，可能会出现此问题。

### <a name="solution"></a>解决方案 

直接打开 VPN 包，而不是通过快捷方式打开。

## <a name="cannot-install-the-vpn-client"></a>无法安装 VPN 客户端

### <a name="cause"></a>原因 

必须有其他证书，才能信任虚拟网络的 VPN 网关。 证书包含在通过 Azure 门户生成的 VPN 客户端配置包中。

### <a name="solution"></a>解决方案

提取 VPN 客户端配置包，并找到 .cer 文件。 若要安装证书，请执行以下步骤：

1. 打开 mmc.exe。
2. 添加“证书”管理单元。
3. 选择本地计算机的“计算机”帐户。
4. 右键单击“受信任的根证书颁发机构”节点。 单击“所有任务” > “导入”，浏览到从 VPN 客户端配置包中提取的 .cer 文件。
5. 重新启动计算机。 
6. 尝试安装 VPN 客户端。

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure 门户错误：无法保存 VPN 网关，数据无效

### <a name="symptom"></a>症状

尝试在 Azure 门户中保存 VPN 网关的更改时，看到以下错误消息：

无法保存虚拟网络网关 &lt;网关名称&gt;**。证书 &lt;证书 ID&gt; 的数据无效**。

### <a name="cause"></a>原因 

如果上传的根证书公钥包含空格等无效字符，可能会出现此问题。

### <a name="solution"></a>解决方案

请确保证书中的数据不包含换行符（回车符）等无效字符。 整个值应为一长行。 下面为证书示例文本：

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure 门户错误：无法保存 VPN 网关，资源名称无效

### <a name="symptom"></a>症状

尝试在 Azure 门户中保存 VPN 网关的更改时，看到以下错误消息： 

无法保存虚拟网络网关 &lt;网关名称&gt;**。资源名称 &lt;尝试上传的证书名称 无效&gt;**。

### <a name="cause"></a>原因

导致此问题发生的原因是证书名称包含空格等无效字符。 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure 门户错误：VPN 包文件下载错误 503

### <a name="symptom"></a>症状

尝试下载 VPN 客户端配置包时，看到以下错误消息：

**无法下载文件。错误详细信息:错误 503。服务器正忙。**
 
### <a name="solution"></a>解决方案

导致此错误发生的原因是临时网络问题。 几分钟后，再次尝试下载 VPN 包。

## <a name="azure-vpn-gateway-upgrade-all-point-to-site-clients-are-unable-to-connect"></a>Azure VPN 网关升级：所有点到站点的客户端都无法连接

### <a name="cause"></a>原因

如果证书的生存期已过半，证书会进行滚动更新。

### <a name="solution"></a>解决方案

要解决此问题，请重新部署所有客户端上点到站点的包。

## <a name="too-many-vpn-clients-connected-at-once"></a>一次性连接的 VPN 客户端过多

对于每个 VPN 网关，最多可以连接 128 个客户端。 可以在 Azure 门户中查看连接的客户端总数。

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>点到站点 VPN 将 10.0.0.0/8 路由错误添加到路由表

### <a name="symptom"></a>症状

在点到站点客户端上进行 VPN 拨号连接时，VPN 客户端应该将一个路由添加到 Azure 虚拟网络。 IP 帮助程序服务应添加 VPN 客户端子网的路由。 

VPN 客户端范围属于 10.0.0.0/8 的较小子网（如 10.0.12.0/24）。 添加了具有更高优先级的 10.0.0.0/8 的路由，而不是 10.0.12.0/24 的路由。 

这种错误的路由会断开与可能属于 10.0.0.0/8 范围内另一子网（如未定义特定路由的 10.50.0.0/24）的其他本地网络的连接。 

### <a name="cause"></a>原因

此行为专为 Windows 客户端设计。 客户端使用 PPP IPCP 协议时，会从服务器（在本例中为 VPN 网关）中获取隧道接口的 IP 地址。 不过，由于协议有限制，因此客户端没有子网掩码。 因为无法通过其他方式获取子网掩码，所以客户端会尝试根据隧道接口 IP 地址的种类来猜测子网掩码。 

因此，路由的添加依据为以下静态映射： 

如果地址属于 A 类 --> 应用 /8

如果地址属于 B 类 --> 应用 /16

如果地址属于 C 类 --> 应用 /24

### <a name="solution"></a>解决方案

将其他网络的路由注入具有最长前缀匹配或指标低于点到站点（因此具有更高优先级）的路由表中。 

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN 客户端无法访问网络文件共享

### <a name="symptom"></a>症状

VPN 客户端已连接到 Azure 虚拟网络。 不过，客户端无法访问网络共享。

### <a name="cause"></a>原因

SMB 协议用于文件共享访问。 连接启动时，VPN 客户端添加了会话凭据并发生失败。 建立连接后，将强制客户端使用缓存凭据进行 Kerberos 身份验证。 此过程会启动查询密钥发行中心（域控制器），以获取令牌。 由于客户端通过 Internet 进行连接，因此可能无法访问域控制器。 所以，客户端无法从 Kerberos 故障转移到 NTLM。 

仅当客户端具有已加入的域颁发的有效证书（其中 SAN=UPN），才会提示客户端提供凭据。 客户端还必须以物理方式连接到域网络。 在这种情况下，客户端尝试使用证书并访问域控制器。 然后，密钥分发中心返回“KDC_ERR_C_PRINCIPAL_UNKNOWN”错误。 客户端被强制故障转移到 NTLM。 

### <a name="solution"></a>解决方案

若要解决此问题，请禁止从以下注册表子项缓存域凭据： 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>重新安装 VPN 客户端后，在 Windows 中找不到点到站点 VPN 连接

### <a name="symptom"></a>症状

先删除了点到站点 VPN 连接，再重新安装 VPN 客户端。 在这种情况下，VPN 连接未成功配置。 在 Windows 的“网络连接”设置中看不到 VPN 连接。

### <a name="solution"></a>解决方案

要解决此问题，请从 C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId> 删除旧的 VPN 客户端配置文件，再重新运行 VPN 客户端安装程序。

## <a name="point-to-site-vpn-client-cannot-resolve-the-fqdn-of-the-resources-in-the-local-domain"></a>点到站点 VPN 客户端无法解析本地域中的资源的 FQDN

### <a name="symptom"></a>症状

当客户端使用点到站点 VPN 连接来连接到 Azure 时，它无法解析本地域中的资源的 FQND。

### <a name="cause"></a>原因

点到站点 VPN 客户端使用在 Azure 虚拟网络中配置的 Azure DNS 服务器。 Azure DNS 服务器优先于在客户端中配置的本地 DNS 服务器，因此所有 DNS 查询都被发送到 Azure DNS 服务器。 如果 Azure DNS 服务器中没有本地资源的记录，则查询失败。

### <a name="solution"></a>解决方案

若要解决此问题，请确保在 Azure 虚拟网络上使用的 Azure DNS 服务器可以解析本地资源的 DNS 记录。 为此，可以使用 DNS 转发器或条件转发器。 有关详细信息，请参阅[使用自己的 DNS 服务器进行名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。

## <a name="the-point-to-site-vpn-connection-is-established-but-you-still-cannot-connect-to-azure-resources"></a>点到站点 VPN 连接已建立，但仍然无法连接到 Azure 资源 

### <a name="cause"></a>原因

如果 VPN 客户端没有从 Azure VPN 网关获得路由，则可能会发生此问题。

### <a name="solution"></a>解决方案

若要解决此问题，请[重置 Azure VPN 网关](vpn-gateway-resetgw-classic.md)。

## <a name="error-the-revocation-function-was-unable-to-check-revocation-because-the-revocation-server-was-offlineerror-0x80092013"></a>错误：“吊销功能无法检查吊销，因为吊销服务器已脱机。(错误 0x80092013)”

### <a name="causes"></a>原因
如果客户端无法访问 http://crl3.digicert.com/ssca-sha2-g1.crl 和 http://crl4.digicert.com/ssca-sha2-g1.crl，则会发生此错误消息。  进行吊销检查需要访问这两个站点。  此问题通常发生在配置了代理服务器的客户端上。 在某些环境中，如果请求不通过代理服务器，则在边缘防火墙处会被拒绝。

### <a name="solution"></a>解决方案

请检查代理服务器设置，请确保客户端可以访问 http://crl3.digicert.com/ssca-sha2-g1.crl 和 http://crl4.digicert.com/ssca-sha2-g1.crl。

## <a name="vpn-client-error-the-connection-was-prevented-because-of-a-policy-configured-on-your-rasvpn-server-error-812"></a>VPN 客户端错误：由于 RAS/VPN 服务器上配置的某个策略，连接被阻止。 (错误 812)

### <a name="cause"></a>原因

如果用于对 VPN 客户端进行身份验证的 RADIUS 服务器具有不正确的设置，或者 Azure 网关无法连接到 Radius 服务器，则会发生此错误。

### <a name="solution"></a>解决方案

确保正确配置 RADIUS 服务器。 有关详细信息，请参阅[将 RADIUS 身份验证与 Azure 多重身份验证服务器集成](../active-directory/authentication/howto-mfaserver-dir-radius.md)。

## <a name="error-405-when-you-download-root-certificate-from-vpn-gateway"></a>从 VPN 网关下载根证书时出现“错误 405”

### <a name="cause"></a>原因

根证书尚未安装。 根证书安装在客户端的**可信证书**存储中。

## <a name="vpn-client-error-the-remote-connection-was-not-made-because-the-attempted-vpn-tunnels-failed-error-800"></a>VPN 客户端错误：未进行远程连接，因为尝试的 VPN 隧道失败。 （错误 800） 

### <a name="cause"></a>原因

网卡驱动程序已过时。

### <a name="solution"></a>解决方案

更新网卡驱动程序：

1. 单击“开始”，键入“设备管理器”，然后从结果列表中选择它。 如果系统提示需要管理员密码或确认，请键入密码或进行确认。
2. 在“网络适配器”类别中，找到要更新的 NIC。  
3. 双击设备名称，选择“更新驱动程序”，选择“自动搜索更新的驱动程序软件”。
4. 如果 Windows 找不到新的驱动程序，可以尝试在设备制造商的网站上查找，并按照说明执行操作。
5. 重启计算机并再次尝试连接。

## <a name="error-file-download-error-target-uri-is-not-specified"></a>错误：“文件下载错误，未指定目标 URI”

### <a name="cause"></a>原因

这是因为配置了不正确的网关类型。

### <a name="solution"></a>解决方案

Azure VPN 网关类型必须是 VPN，VPN 类型必须是 RouteBased。

## <a name="vpn-package-installer-doesnt-complete"></a>VPN 包安装程序未完成

### <a name="cause"></a>原因

此问题可能由以前的 VPN 客户端安装引起。 

### <a name="solution"></a>解决方案

从 C:\users\username\AppData\Microsoft\Network\Connections\<VirtualNetworkId> 删除旧的 VPN 客户端配置文件，再次运行 VPN 客户端安装程序。 

## <a name="the-vpn-client-hibernates-or-sleep-after-some-time"></a>VPN 客户端在一段时间后进入休眠状态或睡眠状态

### <a name="solution"></a>解决方案

在 VPN 客户端运行的计算机中检查睡眠和休眠设置。

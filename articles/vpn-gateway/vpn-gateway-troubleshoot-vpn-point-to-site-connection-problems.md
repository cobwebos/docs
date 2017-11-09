---
title: "排查 Azure 点到站点连接问题 | Microsoft Docs"
description: "了解如何排查点到站点连接问题。"
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: genli
ms.openlocfilehash: 76ab1600903705aad7f18f48f41cb7119c3c09bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
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

1. 请确保已正确的位置安装下列证书：

    | 证书 | 位置 |
    | ------------- | ------------- |
    | AzureClient.pfx  | Current User\Personal\Certificates |
    | Azuregateway-GUID.cloudapp.net  | Current User\Trusted Root Certification Authorities|
    | AzureGateway-GUID.cloudapp.net、AzureRoot.cer    | Local Computer\Trusted Root Certification Authorities|

2. 转到 Users\<UserName>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>，在用户和计算机的存储上手动安装证书（*.cer 文件）。

若要详细了解如何安装客户端证书，请参阅[为点到站点连接生成并导出证书](vpn-gateway-certificates-point-to-site.md)。

> [!NOTE]
> 导入客户端证书时，请勿选择“启用强私钥保护”选项。

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN 客户端错误：接收到的消息异常，或格式不正确

### <a name="symptom"></a>症状

尝试使用 VPN 客户端连接到 Azure 虚拟网络时，看到以下错误消息：

**收到意外或格式不当的消息。(错误 0x80090326)**

### <a name="cause"></a>原因

如果根证书公钥未上传到 Azure VPN 网关，则会发生此问题。 如果密钥已损坏或过期，也会发生此问题。

### <a name="solution"></a>解决方案

若要解决此问题，请在 Azure 门户中检查根证书的状态，确定是否已吊销。 如果未吊销，请尝试删除并重新上传根证书。 有关详细信息，请参阅[创建证书](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)。

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

**无法保存虚拟网络网关 &lt;*网关名称*&gt;。 证书 &lt;*证书 ID*&gt; 的数据无效。**

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

**无法保存虚拟网络网关 &lt;*网关名称*&gt;。 资源名称 &lt;*尝试上传的证书名称*&gt; 无效。**

### <a name="cause"></a>原因

导致此问题发生的原因是证书名称包含空格等无效字符。 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure 门户错误：VPN 包文件下载错误 503

### <a name="symptom"></a>症状

尝试下载 VPN 客户端配置包时，看到以下错误消息：

**无法下载文件。错误详细信息:错误 503。服务器正忙。**
 
### <a name="solution"></a>解决方案

导致此错误发生的原因是临时网络问题。 几分钟后，再次尝试下载 VPN 包。

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Azure VPN 网关升级：所有 P2S 客户端都无法连接

### <a name="cause"></a>原因

如果证书的生存期已过半，证书会进行滚动更新。

### <a name="solution"></a>解决方案

若要解决此问题，请新建证书并向 VPN 客户端重新分发。 

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

若要解决此问题，请从 C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections 删除旧的 VPN 客户端配置文件，再重新运行 VPN 客户端安装程序。

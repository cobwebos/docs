---
title: Azure VPN 网关：关于 P2S VPN 客户端配置文件
description: 这可帮助你使用客户端配置文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151624"
---
# <a name="about-p2s-vpn-client-profiles"></a>关于 P2S VPN 客户端配置文件

下载的配置文件包含配置 VPN 连接所需的信息。 本文将帮助你获取并了解 VPN 客户端配置文件所需的信息。

## <a name="1-download-the-file"></a>1. 下载文件

运行以下命令。 将结果 URL 复制到浏览器，以便下载配置文件 zip 文件。

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. 提取 zip 文件

解压缩 zip 文件。 文件包含以下文件夹：

* Azurevpn.bgpsettings.asn
* 泛型
* OpenVPN （如果已在网关上启用 OpenVPN 和 Azure AD 身份验证设置。 请参阅[创建租户](openvpn-azure-ad-tenant.md)。）

## <a name="3-retrieve-information"></a>3. 检索信息

在**azurevpn.bgpsettings.asn**文件夹中，导航到***azurevpnconfig***文件并用记事本打开它。 记下以下标记之间的文本。

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>配置文件详细信息

添加连接时，请使用在上一步骤中收集的有关 "配置文件详细信息" 页面的信息。 字段与以下信息相对应：

   * **受众：** 标识用于标记的接收方资源
   * **颁发者：** 标识发出令牌的安全令牌服务（STS）和 Azure AD 租户
   * **租户：** 包含已颁发令牌的目录租户的不可变、唯一标识符
   * **FQDN：** Azure VPN 网关上的完全限定的域名（FQDN）
   * **ServerSecret：** VPN 网关预共享密钥

## <a name="folder-contents"></a>文件夹内容

* **OpenVPN 文件夹**包含需要修改以包括密钥和证书的*ovpn*配置文件。 有关详细信息，请参阅为[AZURE VPN 网关配置 OpenVPN 客户端](vpn-gateway-howto-openvpn-clients.md#windows)。

* **通用文件夹**包含公共服务器证书和 vpnsettings.xml 文件。 Vpnsettings.xml 文件包含配置泛型客户端所需的信息。

* 下载的 zip 文件可能还包含**WindowsAmd64**和**WindowsX86**文件夹。 这些文件夹包含适用于 Windows 客户端的 SSTP 和 IKEv2 的安装程序。 需要对客户端具有管理权限才能安装它们。

## <a name="next-steps"></a>后续步骤

有关点到站点的详细信息，请参阅[关于点到站点](point-to-site-about.md)。

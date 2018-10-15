---
title: 为点到站点连接生成和导出证书：Linux：CLI：Azure | Microsoft Docs
description: 使用 Linux (strongSwan) CLI 创建自签名根证书、导出公钥和生成客户端证书。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305720"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>使用 Linux strongSwan CLI 为点到站点连接生成和导出证书

点到站点连接使用证书进行身份验证。 本文说明如何使用 Linux CLI 和 strongSwan 创建自签名根证书以及生成客户端证书。 如果正在查找不同的证书说明，请参阅 [Powershell](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 文章。

> [!NOTE]
> 本文中的步骤需要使用 strongSwan。
>

用于本文步骤的计算机配置如下：

| | |
|---|---|
|**计算机**| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
|**依赖项**| apt-get install strongswan-ikev2 strongswan-plugin-eap-tls<br>apt-get install libstrongswan-standard-plugins |

## <a name="install-strongswan"></a>安装 strongSwan

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

有关如何使用 GUI 安装 strongSwan 的信息，请参阅[客户端配置](point-to-site-vpn-client-configuration-azure-cert.md#install)一文中的步骤。

## <a name="generate-keys-and-certificate"></a>生成密钥和证书

1. 生成 CA 证书。

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. 打印 base64 格式的 CA 证书。 这是 Azure 支持的格式。 稍后需将其作为 P2S 配置的一部分上传到 Azure。

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. 生成用户证书。

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. 生成包含用户证书的 p12 捆绑包。 在后续步骤中使用[客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)时将使用此捆绑包。

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>后续步骤

继续进行点到站点配置以[创建和安装 VPN 客户端配置文件](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。
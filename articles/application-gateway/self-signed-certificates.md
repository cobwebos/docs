---
title: 使用自定义根 CA 生成自签名证书
titleSuffix: Azure Application Gateway
description: 了解如何使用自定义根 CA 生成 Azure 应用程序网关自签名证书
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: de580d8f94292ae65769c696aa232f5b660bf414
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84806764"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>使用自定义根 CA 生成 Azure 应用程序网关自签名证书

应用程序网关 v2 SKU 介绍了如何使用受信任的根证书以允许后端服务器。 这会删除 v1 SKU 中所需的身份验证证书。 根证书是来自后端证书服务器的 Base-64 编码的 X.509(.CER) 格式根证书。 它标识颁发服务器证书的根证书颁发机构 (CA)，服务器证书随后将用于 TLS/SSL 通信。

如果你网站的证书是由知名 CA（例如 GoDaddy 或 DigiCert）签名的，则默认情况下，应用程序网关将信任该证书。 在这种情况下，不需要显式上传根证书。 有关详细信息，请参阅[应用程序网关的 TLS 终止和端到端 TLS 概述](ssl-overview.md)。 但如果你已有开发/测试环境，并且不想购买由已验证的 CA 签名的证书，则可以创建自己的自定义 CA，然后使用该 CA 创建自签名证书。 

> [!NOTE]
> 自签名证书默认不受信任，并且可能难以维护。 另外，它们可能使用过时的哈希以及不够可靠的加密套件。 为了提高安全性，请购买由知名证书颁发机构签名的证书。

本文介绍如何执行以下操作：

- 创建自己的自定义证书颁发机构
- 创建由自定义 CA 签名的自签名证书
- 将自签名的根证书上传到应用程序网关，以便对后端服务器进行身份验证

## <a name="prerequisites"></a>先决条件

- 在运行 Windows 或 Linux 的计算机上安装 [OpenSSL](https://www.openssl.org/) 

   本教程将使用 OpenSSL，不过，其他工具可能也可用于证书管理。 你可能会发现，许多 Linux 分发版（如 Ubuntu）中已捆绑 OpenSSL。
- **一个 Web 服务器**

   例如，用于测试证书的 Apache、IIS 或 NGINX。

- **一个应用程序网关 v2 SKU**
   
  如果没有现有的应用程序网关，请参阅[快速入门：使用 Azure 应用程序网关定向 Web 流量 - Azure 门户](quick-create-portal.md)

## <a name="create-a-root-ca-certificate"></a>创建根 CA 证书

使用 OpenSSL 创建根 CA 证书。

### <a name="create-the-root-key"></a>创建根密钥

1. 登录到安装了 OpenSSL 的计算机并运行以下命令。 这会创建一个受密码保护的密钥。

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. 在提示符下键入强密码。 例如，使用大写字母、小写字母、数字和符号至少输入 9 个字符。

### <a name="create-a-root-certificate-and-self-sign-it"></a>创建根证书并进行自签名

1. 使用以下命令生成 CSR 和证书。

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   上述命令将创建根证书。 稍后你将使用此证书来为服务器证书签名。

1. 出现提示时，请键入根密钥的密码，以及自定义 CA 的组织信息，例如国家/地区、省/市/自治区、组织、组织单位和完全限定的域名（颁发者的域）。

   ![创建根证书](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>创建服务器证书

接下来，使用 OpenSSL 创建服务器证书。

### <a name="create-the-certificates-key"></a>创建证书的密钥

使用以下命令生成服务器证书的密钥。

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>创建 CSR（证书签名请求）

CSR 是请求证书时向 CA 提供的公钥。 CA 将针对此特定请求颁发证书。

> [!NOTE]
> 服务器证书的 CN（公用名）必须与颁发者的域不同。 例如，在本例中，颁发者的 CN 是 `www.contoso.com`，服务器证书的 CN 是 `www.fabrikam.com`


1. 使用以下命令生成 CSR：

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 出现提示时，请键入根密钥的密码，以及自定义 CA 的组织信息：国家/地区、省/市/自治区、组织、组织单位和完全限定的域名。 这是网站的域，它应该不同于颁发者。

   ![服务器证书](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>使用 CSR 和密钥生成证书，并使用 CA 的根密钥为该证书签名

1. 使用以下命令以创建证书：

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>验证新建的证书

1. 使用以下命令列显 CRT 文件的输出，并验证其内容：

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![证书验证](media/self-signed-certificates/verify-cert.png)

1. 验证目录中的文件，确保其中具有以下文件：

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>在 Web 服务器的 TLS 设置中配置证书

在 Web 服务器中，使用 fabrikam.crt 和 fabrikam.key 文件配置 TLS。 如果 Web 服务器无法接收这两个文件，你可以使用 OpenSSL 命令将其合并成单个 .pem 或 .pfx 文件。

### <a name="iis"></a>IIS

有关如何导入证书并将其上传为 IIS 上的服务器证书的说明，请参阅[如何：在 Windows Server 2003 中的 Web 服务器上安装导入的证书](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)。

有关 TLS 绑定说明，请参阅[如何在 IIS 7 上设置 SSL](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)。

### <a name="apache"></a>Apache

以下配置是在 Apache 中 [为 SSL 配置的虚拟主机](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) 示例：

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

以下配置是 [NGINX 服务器块](https://nginx.org/docs/http/configuring_https_servers.html)与 TLS 配置的示例：

![具有 TLS 的 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>访问服务器以验证配置

1. 将根证书添加到计算机的受信任根存储中。 访问网站时，请确保浏览器中显示整个证书链。

   ![受信任的根证书](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > 假设 DNS 已配置为将 Web 服务器名称（在本示例中为 www.fabrikam.com）指向 Web 服务器的 IP 地址。 否则，可以编辑[主文件](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)来解析名称。
1. 浏览到你的网站，然后单击浏览器地址框中的锁定图标来验证站点和证书信息。

## <a name="verify-the-configuration-with-openssl"></a>使用 OpenSSL 验证配置

或者，可以使用 OpenSSL 来验证证书。

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL 证书验证](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>将根证书上传到应用程序网关的 HTTP 设置

若要在应用程序网关中上传证书，必须将 .crt 证书导出为 Base-64 编码的 .cer 格式。 由于 .crt 已包含 Base-64 编码格式的公钥，因此，只需将文件扩展名从 .crt 重命名为 .cer 即可。 

### <a name="azure-portal"></a>Azure 门户

若要从门户上传受信任的根证书，请选择“HTTP 设置”，然后选择“HTTPS”协议 。

![使用门户添加证书](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

或者，可以使用 Azure CLI 或 Azure PowerShell 上传根证书。 以下代码是一个 Azure PowerShell 示例。

> [!NOTE]
> 下面的示例将受信任的根证书添加到应用程序网关，创建新的 HTTP 设置并添加新的规则（假设后端池和侦听器已存在）。

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>验证应用程序网关后端运行状况

1. 单击应用程序网关的“后端运行状况”视图，检查探测是否正常。
1. 应会看到，HTTPS 探测的状态为“正常”。

![HTTPS 探测](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>后续步骤

若要了解有关应用程序网关上的 SSL\TLS 的详细信息，请参阅[应用程序网关的 TLS 终止和端到端 TLS 概述](ssl-overview.md)。


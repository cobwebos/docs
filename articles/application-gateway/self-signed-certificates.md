---
title: 使用自定义根 CA 生成自签名证书
titleSuffix: Azure Application Gateway
description: 了解如何使用自定义根 CA 生成 Azure 应用程序网关自签名证书
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: fb3d2e70d9485c63d6de156abe9d192afa818814
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075084"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>使用自定义根 CA 生成 Azure 应用程序网关自签名证书

应用程序网关 v2 SKU 引入了允许后端服务器的受信任根证书。 这样，就无需像在 v1 SKU 中那样使用身份验证证书。 该根证书是来自后端证书服务器的 Base-64 编码的 X.509(.CER) 格式根证书。 它可以识别颁发服务器证书的根证书颁发机构 (CA)，然后，服务器证书可用于 SSL 通信。

如果网站的证书由已知的 CA（例如 GoDaddy 或 DigiCert） 签名，则应用程序网关默认会信任该证书。 在这种情况下，无需显式上传根证书。 有关详细信息，请参阅 [SSL 终止和应用程序网关的端到端 SSL 概述](ssl-overview.md)。 但是，如果你有一个开发/测试环境，但不想要购买由已验证的 CA 签名的证书，可以创建自己的自定义 CA，然后使用该 CA 创建自签名证书。 

> [!NOTE]
> 自签名证书默认不受信任，并且可能难以维护。 另外，它们可能使用过时的哈希，以及不够可靠的加密套件。 为了提高安全性，请购买由知名证书颁发机构签名的证书。

本文介绍如何执行以下操作：

- 创建自己的自定义证书颁发机构
- 创建由自定义 CA 签名的自签名证书
- 将自签名的根证书上传到应用程序网关，以便对后端服务器进行身份验证

## <a name="prerequisites"></a>先决条件

- **在运行 Windows 或 Linux 的计算机上安装 [OpenSSL](https://www.openssl.org/)** 

   本教程将使用 OpenSSL，不过，其他工具可能也可用于证书管理。 你可能会发现，许多 Linux 分发版（例如 Ubuntu）中已捆绑 OpenSSL。
- **一个 Web 服务器**

   例如，用于测试证书的 Apache、IIS 或 NGINX。

- **一个应用程序网关 v2 SKU**
   
  如果没有现有的应用程序网关，请参阅[快速入门：使用 Azure 应用程序网关的直接 web 流量-Azure 门户](quick-create-portal.md)。

## <a name="create-a-root-ca-certificate"></a>创建根 CA 证书

使用 OpenSSL 创建根 CA 证书。

### <a name="create-the-root-key"></a>创建根密钥

1. 登录到安装了 OpenSSL 的计算机并运行以下命令。 这会创建密码保护的密钥。

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. 在提示符下键入强密码。 例如，使用大写字母、小写字母、数字和符号至少输入 9 个字符。

### <a name="create-a-root-certificate-and-self-sign-it"></a>创建根证书并自签名

1. 使用以下命令生成 CSR 和证书。

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   以上命令创建根证书。 稍后你将使用此证书来为服务器证书签名。

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
> 服务器证书的 CN（公用名）必须与颁发者的域不同。 例如，在这种情况下，将 `www.contoso.com` 颁发者的 CN，并 `www.fabrikam.com`服务器证书的 CN。


1. 使用以下命令生成 CSR：

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 出现提示时，请键入根密钥的密码以及自定义 CA 的组织信息：国家/地区、省/市/自治区、Org、OU 和完全限定域名。 这是网站的域，它应该不同于颁发者。

   ![服务器证书](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>使用 CSR 和密钥生成证书，并使用 CA 的根密钥为该证书签名

1. 使用以下命令以创建证书：

   ```
   openssl x509 -req -in fabrikam.csr -CA public.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>验证新建的证书

1. 使用以下命令列显 CRT 文件的输出，并验证其内容：

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![证书验证](media/self-signed-certificates/verify-cert.png)

1. 验证目录中的文件，确保其中包含以下文件：

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>在 Web 服务器的 SSL 设置中配置证书

在 Web 服务器中，使用 fabrikam.crt 和 fabrikam.key 文件配置 SSL。 如果 Web 服务器无法接收两个文件，你可以使用 OpenSSL 命令将其合并成单个 .pem 或 .pfx 文件。

### <a name="iis"></a>IIS

有关如何导入证书并在 IIS 上将其上传为服务器证书的说明，请参阅[如何：在 Windows server 2003 中的 Web 服务器上安装导入的证书](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)。

有关 SSL 绑定说明，请参阅[如何在 IIS 7 上设置 SSL](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)。

### <a name="apache"></a>Apache

以下配置是在 Apache 中[为 SSL 配置的虚拟主机](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts)示例：

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

以下配置是使用 SSL 配置的 [NGINX 服务器块](https://nginx.org/docs/http/configuring_https_servers.html)示例：

![使用 SSL 的 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>访问服务器以验证配置

1. 将根证书添加到计算机的受信任根存储中。 访问网站时，请确保浏览器中显示整个证书链。

   ![受信任的根证书](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > 假设 DNS 已配置为将 Web 服务器名称（在本示例中为 www.fabrikam.com）指向 Web 服务器的 IP 地址。 否则，可以编辑 [hosts 文件](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)来解析名称。
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

若要从门户上传受信任的根证书，请选择“HTTP 设置”，然后选择“HTTPS”协议。

![使用门户添加证书](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

或者，可以使用 Azure CLI 或 Azure PowerShell 上传根证书。 以下代码是一个 Azure PowerShell 示例。

> [!NOTE]
> 以下示例将受信任的根证书添加到应用程序网关，创建新的 HTTP 设置并添加新的规则（假设后端池和侦听器已存在）。

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

## Add the configuration to the HTTP Setting and don’t forget to set the “hostname” field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server’s certificate. Note that SSL handshake will
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
1.  应会看到，HTTPS 探测的状态为“正常”。

    ![HTTPS 探测](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>后续步骤

若要详细了解应用程序网关中的 SSL\TLS，请参阅[应用程序网关的 SSL 终止和端到端 SSL 概述](ssl-overview.md)。


---
title: 使用自定义根 CA 生成 Azure 应用程序网关自签名证书
description: 了解如何使用自定义根 CA 生成 Azure 应用程序网关自签名证书
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 0b97f2f6df87255e10faaf58c40ea9136354bff6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386299"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>使用自定义根 CA 生成 Azure 应用程序网关自签名证书

应用程序网关 v2 SKU 介绍了如何使用受信任的根证书以允许后端服务器。 这会删除 v1 SKU 中所需的身份验证证书。 *根证书*是64编码的 x.509 (。CER) 从后端证书服务器中设置根证书的格式。 它标识颁发服务器证书的根证书颁发机构 (CA), 然后使用服务器证书进行 SSL 通信。

应用程序网关在默认情况下信任你的网站的证书 (如果该证书由已知 CA 签名 (例如, GoDaddy 或 DigiCert)。 在这种情况下, 不需要显式上传根证书。 有关详细信息, 请参阅[ssl 终止概述以及应用程序网关的端到端 ssl](ssl-overview.md)。 但是, 如果你有开发/测试环境, 但又不想购买已验证的 CA 签名证书, 则可以创建自己的自定义 CA 并使用它创建自签名证书。 

> [!NOTE]
> 自签名证书默认情况下是不受信任的, 因此很难维护。 而且, 它们可能使用过时的哈希和密码套件, 它们可能并不强。 为了获得更好的安全性, 请购买由知名证书颁发机构签名的证书。

本文介绍如何执行以下操作：

- 创建你自己的自定义证书颁发机构
- 创建由您的自定义 CA 签名的自签名证书
- 将自签名的根证书上传到应用程序网关, 对后端服务器进行身份验证

## <a name="prerequisites"></a>先决条件

- **运行 Windows 或 Linux 的计算机上的[OpenSSL](https://www.openssl.org/)** 

   尽管有其他工具可用于证书管理, 但本教程使用 OpenSSL。 你可以找到与许多 Linux 分发 (如 Ubuntu) 捆绑在一起的 OpenSSL。
- **Web 服务器**

   例如, Apache、IIS 或 NGINX 用于测试证书。

- **应用程序网关 v2 SKU**
   
  如果没有现有的应用程序网关, 请[参阅快速入门:使用 Azure 应用程序网关定向 Web 流量 - Azure 门户](quick-create-portal.md)

## <a name="create-a-root-ca-certificate"></a>创建根 CA 证书

使用 OpenSSL 创建根 CA 证书。

### <a name="create-the-root-key"></a>创建根密钥

1. 登录到安装了 OpenSSL 的计算机, 并运行以下命令。 这会创建受密码保护的密钥。

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. 在提示符下, 键入强密码。 例如, 使用大写字母、小写字母、数字和符号的至少9个字符。

### <a name="create-a-root-certificate-and-self-sign-it"></a>创建根证书并自行签署

1. 使用以下命令生成 csr 和证书。

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   上述命令创建根证书。 将使用此签名来对服务器证书进行签名。

1. 出现提示时, 请键入根密钥的密码以及自定义 CA (如国家/地区、省/市/自治区、Org、OU 和完全限定域名 (这是颁发者的域) 的组织信息。

   ![创建根证书](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>创建服务器证书

接下来, 你将使用 OpenSSL 创建服务器证书。

### <a name="create-the-certificates-key"></a>创建证书的密钥

使用以下命令生成服务器证书的密钥。

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>创建 CSR (证书签名请求)

CSR 是申请证书时向 CA 提供的公钥。 CA 颁发此特定请求的证书。

> [!NOTE]
> 服务器证书的 CN (公用名) 必须与颁发者的域不同。 例如, 在本例中, 颁发者的 CN 是 www.contoso.com, 服务器证书的 CN 是 www.fabrikam.com


1. 使用以下命令生成 CSR:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. 出现提示时, 键入根密钥的密码和自定义 CA 的组织信息:国家/地区、省/市/自治区、Org、OU 和完全限定的域名。 这是网站的域, 它应该不同于颁发者。

   ![服务器证书](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>生成包含 CSR 和密钥的证书, 并将其与 CA 的根密钥一起签署

1. 使用以下命令创建证书:

   ```
   openssl x509 -req -in fabrikam.csr -CA public.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>验证新创建的证书

1. 使用以下命令打印 CRT 文件的输出, 并验证其内容:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![证书验证](media/self-signed-certificates/verify-cert.png)

1. 验证目录中的文件, 并确保具有以下文件:

   - contoso. crt
   - contoso. 密钥
   - fabrikam
   - fabrikam。键

## <a name="configure-the-certificate-in-your-web-servers-ssl-settings"></a>在 web 服务器的 SSL 设置中配置证书

在 web 服务器中, 使用 fabrikam 和 fabrikam 文件配置 SSL。 如果 web 服务器不能接受两个文件, 则可以使用 OpenSSL 命令将它们合并到单个 pem 或 .pfx 文件。

### <a name="iis"></a>IIS

有关如何导入证书并在 IIS 上将其上传为服务器证书的[说明, 请参阅如何:在 Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server)中的 Web 服务器上安装导入的证书。

有关 SSL 绑定说明, 请参阅[如何在 IIS 7 上设置 SSL](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1)。

### <a name="apache"></a>Apache

以下配置是在 Apache 中[为 SSL 配置的示例虚拟主机](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts):

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

以下配置是[NGINX server block](http://nginx.org/docs/http/configuring_https_servers.html) with SSL 配置的示例:

![带 SSL 的 NGINX](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>访问服务器来验证配置

1. 将根证书添加到计算机的受信任根存储中。 当你访问网站时, 请确保在浏览器中显示整个证书链。

   ![受信任的根证书](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > 假设 DNS 已配置为将 web 服务器名称 (在本示例中为 www.fabrikam.com) 指向 web 服务器的 IP 地址。 如果不是, 则可以编辑[主机文件](https://answers.microsoft.com/windows/forum/windows_10-other_settings-winpc/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d)来解析名称。
1. 浏览到你的网站, 然后单击浏览器的 "地址" 框中的锁定图标以验证站点和证书信息。

## <a name="verify-the-configuration-with-openssl"></a>用 OpenSSL 验证配置

或者, 你可以使用 OpenSSL 来验证证书。

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![OpenSSL 证书验证](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>将根证书上传到应用程序网关的 HTTP 设置

若要在应用程序网关上上载证书, 必须将 .crt 证书导出为 .cer 格式的 Base-64 编码。 由于 crt 已经包含采用64编码格式的公钥, 因此, 只需将文件扩展名从 .crt 重命名为 .cer 即可。 

### <a name="azure-portal"></a>Azure 门户

若要从门户上传受信任的根证书, 请选择 " **HTTP 设置**", 并选择 " **HTTPS**协议"。

![使用门户添加证书](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

或者, 你可以使用 Azure CLI 或 Azure PowerShell 上传根证书。 下面的代码是 Azure PowerShell 示例。

> [!NOTE]
> 下面的示例将受信任的根证书添加到应用程序网关, 创建新的 HTTP 设置并添加新的规则 (假设后端池和侦听器已经存在)。

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

1. 单击应用程序网关的**后端运行状况**视图, 检查探测是否正常。
1.  应会看到 HTTPS 探测的状态为 "**正常**"。

    ![HTTPS 探测](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>后续步骤

若要了解有关应用程序网关上 SSL\TLS 的详细信息, 请参阅[ssl 终止概述和应用程序网关的端到端 ssl](ssl-overview.md)。


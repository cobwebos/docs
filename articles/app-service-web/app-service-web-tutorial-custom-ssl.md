---
title: "将现有的自定义 SSL 证书绑定到 Azure Web 应用 | Microsoft Docs"
description: "了解如何将自定义 SSL 证书绑定到 Azure 应用服务中的 Web 应用、移动应用后端或 API 应用。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: f344ef59c3d6f9f99989a37e78f161b8be948916
ms.contentlocale: zh-cn
ms.lasthandoff: 06/26/2017

---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>将现有的自定义 SSL 证书绑定到 Azure Web 应用

Azure Web 应用提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何将从受信任证书颁发机构那里购买的自定义 SSL 证书绑定到 [Azure Web 应用](app-service-web-overview.md)。 完成本教程后，你便可以访问自定义 DNS 域的 HTTPS 终结点上的 Web 应用。

![包含自定义 SSL 证书的 Web 应用](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

本教程介绍如何：

> [!div class="checklist"]
> * 升级应用的定价层
> * 将自定义 SSL 证书绑定到应用服务
> * 为应用实施 HTTPS
> * 使用脚本自动执行 SSL 证书绑定

> [!NOTE]
> 如果需要获取自定义 SSL 证书，可以直接在 Azure 门户中获取，然后将其绑定到 Web 应用。 请遵循[应用服务证书教程](web-sites-purchase-ssl-web-site.md)。

## <a name="prerequisites"></a>先决条件

完成本教程：

- [创建应用服务应用](/azure/app-service/)
- [将自定义 DNS 名称映射到 Web 应用](app-service-web-tutorial-custom-domain.md)
- 从受信任的证书颁发机构获取 SSL 证书

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>SSL 证书的要求

若要在应用服务中使用证书，该证书必须满足以下所有要求：

* 已由受信任的证书颁发机构签名
* 已导出为受密码保护的 PFX 文件
* 包含长度至少为 2048 位的私钥
* 包含证书链中的所有中间证书

> [!NOTE]
> 椭圆曲线加密 (ECC) 证书可用于应用服务，但本文不予讨论。 请咨询证书颁发机构，了解有关创建 ECC 证书的确切步骤。

## <a name="prepare-your-web-app"></a>准备 Web 应用

若要将自定义 SSL 证书绑定到 Web 应用，[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于“基本”、“标准”或“高级”层。 在此步骤中，请确保 Web 应用位于受支持的定价层。

### <a name="log-in-to-azure"></a>登录 Azure

打开 [Azure 门户](https://portal.azure.com)。

### <a name="navigate-to-your-web-app"></a>导航到你的 Web 应用

在左侧菜单中单击“应用服务”，然后单击 Web 应用的名称。

![选择 Web 应用](./media/app-service-web-tutorial-custom-ssl/select-app.png)

登录到 Web 应用的管理页。  

### <a name="check-the-pricing-tier"></a>检查定价层

在 Web 应用页的左侧导航窗格中，滚动到“设置”部分，然后选择“增加(应用服务计划)”。

![扩展菜单](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

检查以确保 Web 应用不在“免费”或“共享”层中。 深蓝色的框突出显示了 Web 应用的当前层。

![检查定价层](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

“免费”或“共享”层不支持自定义 SSL。 如果需要增加，请按照下一部分中的步骤进行操作。 否则，请关闭“选择定价层”页并跳转到[上传和绑定 SSL 证书](#upload)。

### <a name="scale-up-your-app-service-plan"></a>纵向扩展应用服务计划

选择“基本”、“标准”或“高级”层。

单击“选择”。

![选择定价层](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

看到以下通知时，说明缩放操作已完成。

![扩展通知](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>绑定 SSL 证书

现已准备好将 SSL 证书上传到 Web 应用。

### <a name="merge-intermediate-certificates"></a>合并中间证书

如果证书颁发机构在证书链中提供了多个证书，则需按顺序合并证书。 

若要执行此操作，请在文本编辑器中打开收到的所有证书。 

创建名为 mergedcertificate.crt 的合并证书文件。 在文本编辑器中，将每个证书的内容复制到此文件。 证书顺序应如以下模板所示：

```
-----BEGIN CERTIFICATE-----
<your Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>将证书导出为 PFX

导出合并的 SSL 证书（其中包含生成证书请求时所用的私钥）。

如果使用 OpenSSL 生成证书请求，则已创建私钥文件。 若要将证书导出为 PFX，请运行以下命令。 替换占位符 &lt;private-key-file> 和 &lt;merged-certificate-file>。

```
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

出现提示时，定义导出密码。 稍后将 SSL 证书上传到应用服务时需使用此密码。

如果使用 IIS 或 Certreq.exe 生成证书请求，请将证书安装到本地计算机，然后[将证书导出为 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。

### <a name="upload-your-ssl-certificate"></a>上传 SSL 证书

若要上传 SSL 证书，请在 Web 应用的左侧导航窗格中单击“SSL 证书”。

单击“上传证书”。

在“PFX 证书文件”中选择 PFX 文件。 在“证书密码”中，键入导出 PFX 文件时创建的密码。

单击“上传” 。

![上传证书](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

应用服务上传完证书后，该证书将显示在“SSL 证书”页中。

![上传的证书](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>绑定 SSL 证书

在“SSL 绑定”部分中，单击“添加绑定”。

在“添加 SSL 绑定”页中，使用下拉列表选择要保护的域名，然后选择要使用的证书。

> [!NOTE]
> 如果已上传证书，但未在“主机名”下拉列表中看到域名，请尝试刷新浏览器页面。
>
>

在“SSL 类型”中，选择是要使用**[服务器名称指示 (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** 还是使用基于 IP 的 SSL。

- 基于 SNI 的 SSL - 可添加多个基于 SNI 的 SSL 绑定。 选择此选项可以使用多个 SSL 证书来保护同一 IP 地址上的多个域。 大多数新式浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI（在[服务器名称指示](http://wikipedia.org/wiki/Server_Name_Indication)中了解更全面的浏览器支持信息）。
- 基于 IP 的 SSL - 只能添加一个基于 IP 的 SSL 绑定。 选择此选项只能使用一个 SSL 证书来保护专用公共 IP 地址。 若要保护多个域，必须使用同一个 SSL 证书来保护所有这些域。 这是 SSL 绑定的传统选项。

单击“添加绑定”。

![绑定 SSL 证书](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

应用服务上传完证书后，该证书将显示在“SSL 绑定”部分中。

![证书已绑定到 Web 应用](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>重新映射 IP SSL 的 A 记录

如果不在 Web 应用中使用基于 IP 的 SSL，请跳到[针对自定义域测试 HTTPS](#test)。

默认情况下，Web 应用使用共享的公共 IP 地址。 将证书与基于 IP 的 SSL 绑定时，应用服务会为 Web 应用创建新的专用 IP 地址。

如果已将 A 记录映射到 Web 应用，请使用这个新的专用 IP 地址更新域注册表。

将使用新的专用 IP 地址更新 Web 应用的“自定义域”页。 [复制此 IP 地址](app-service-web-tutorial-custom-domain.md#info)，然后[将 A 记录重新映射](app-service-web-tutorial-custom-domain.md#map-an-a-record)到此新 IP 地址。

<a name="test"></a>

## <a name="test-https"></a>测试 HTTPS

接下来只需确保 HTTPS 适用于自定义域。 在不同的浏览器中浏览到 `https://<your.custom.domain>`，查看是否能够打开你的 Web 应用。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> 如果 Web 应用显示证书验证错误，可能是因为使用了自签名证书。
>
> 如果不是这样，可能是在将证书导出为 PFX 文件时遗漏了中间证书。

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>实施 HTTPS

应用服务*不*强制实施 HTTPS，因此任何人仍可使用 HTTP 访问你的 Web 应用。 如果想要对 Web 应用强制实施 HTTPS，请在 Web 应用的 web.config 文件中定义重写规则。 无论 Web 应用的语言框架如何，应用服务都会使用此文件。

> [!NOTE]
> 存在语言特定的请求重定向。 ASP.NET MVC 可使用 [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) 筛选器，而非 web.config 中的重写规则。

如果你是 .NET 开发人员，应该比较熟悉此文件。 此文件位于解决方案的根目录中。

或者，如果你使用 PHP、Node.js、Python 或 Java 进行开发，我们可能会在应用服务中替你生成此文件。

遵循[使用 FTP/S 将应用部署到 Azure 应用服务](app-service-deploy-ftp.md)中的说明连接到 Web 应用的 FTP 终结点。

此文件应位于 _/home/site/wwwroot_ 中。 如果不是，请使用以下 XML 在此文件夹中创建 web.config 文件：

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule ELEMENT FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule ELEMENT FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

对于现有的 web.config 文件，请将整个 `<rule>` 元素复制到 web.config 的 `configuration/system.webServer/rewrite/rules` 元素中。 如果 web.config 中已有其他 `<rule>` 元素，请将复制的 `<rule>` 元素放置在其他 `<rule>` 元素的前面。

每当用户对你的 Web 应用发出 HTTP 请求时，此规则都会将 HTTP 301（永久重定向）返回到 HTTPS 协议。 例如，将请求从 `http://contoso.com` 重定向到 `https://contoso.com`。

有关 IIS URL 重写模块的详细信息，请参阅 [URL 重写](http://www.iis.net/downloads/microsoft/url-rewrite)文档。

## <a name="enforce-https-for-web-apps-on-linux"></a>对 Linux 上的 Web 应用强制实施 HTTPS

Linux 上的应用服务不会强制实施 HTTPS，因此所有人仍可使用 HTTP 访问你的 Web 应用。 若要对 Web 应用强制实施 HTTPS，请在 Web 应用的 .htaccess 文件中定义重写规则。 

遵循[使用 FTP/S 将应用部署到 Azure 应用服务](app-service-deploy-ftp.md)中的说明连接到 Web 应用的 FTP 终结点。

在 /home/site/wwwroot 中，使用以下代码创建 .htaccess 文件：

```
RewriteEngine On
RewriteCond %{HTTP:X-ARR-SSL} ^$
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

每当用户对你的 Web 应用发出 HTTP 请求时，此规则都会将 HTTP 301（永久重定向）返回到 HTTPS 协议。 例如，将请求从 `http://contoso.com` 重定向到 `https://contoso.com`。

## <a name="automate-with-scripts"></a>使用脚本自动化

可以在 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview) 中使用脚本自动完成 Web 应用的 SSL 绑定。

### <a name="azure-cli"></a>Azure CLI

以下命令上传已导出的 PFX 文件并获取指纹。

```bash
thumbprint=$(az appservice web config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

以下命令使用前一命令获取的指纹添加基于 SNI 的 SSL 绑定。

```bash
az appservice web config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

以下命令上传已导出的 PFX 文件并添加基于 SNI 的 SSL 绑定。

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 升级应用的定价层
> * 将自定义 SSL 证书绑定到应用服务
> * 为应用实施 HTTPS
> * 使用脚本自动执行 SSL 证书绑定

继续学习下一教程，了解如何使用 Azure 内容交付网络。

> [!div class="nextstepaction"]
> [向 Azure 应用服务添加内容交付网络 (CDN)](app-service-web-tutorial-content-delivery-network.md)


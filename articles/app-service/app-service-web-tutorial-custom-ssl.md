---
title: 将现有的自定义 SSL 证书绑定到 Azure Web 应用 | Microsoft Docs
description: 了解如何将自定义 SSL 证书绑定到 Azure 应用服务中的 Web 应用、移动应用后端或 API 应用。
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 7c14b241155e10f0bb325b50819e2277622e4dff
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>教程：将现有的自定义 SSL 证书绑定到 Azure Web 应用

Azure Web 应用提供高度可缩放的自修补 Web 托管服务。 本教程介绍如何将从受信任证书颁发机构那里购买的自定义 SSL 证书绑定到 [Azure Web 应用](app-service-web-overview.md)。 完成本教程后，便可以访问自定义 DNS 域的 HTTPS 终结点上的 Web 应用。

![包含自定义 SSL 证书的 Web 应用](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

本教程介绍如何执行下列操作：

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
- 拥有用于对 SSL 证书请求进行签名的私钥

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

要将自定义 SSL 证书绑定到 Web 应用，[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于“基本”、“标准”或“高级”层。 在此步骤中，请确保 Web 应用位于受支持的定价层。

### <a name="log-in-to-azure"></a>登录 Azure

打开 [Azure 门户](https://portal.azure.com)。

### <a name="navigate-to-your-web-app"></a>导航到 Web 应用

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

创建名为 mergedcertificate.crt 的合并证书文件。 在文本编辑器中，将每个证书的内容复制到此文件。 证书的顺序应遵循证书链中的顺序，以你的证书开头，以根证书结尾， 如以下示例所示：

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>将证书导出为 PFX

导出合并的 SSL 证书（其中包含生成证书请求时所用的私钥）。

如果使用 OpenSSL 生成证书请求，则已创建私钥文件。 若要将证书导出为 PFX，请运行以下命令。 将占位符 _&lt;private-key-file>_ 和 _&lt;merged-certificate-file>_ 分别替换为私钥和合并证书文件的路径。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

出现提示时，定义导出密码。 稍后将 SSL 证书上传到应用服务时需使用此密码。

如果使用 IIS 或 Certreq.exe 生成证书请求，请将证书安装到本地计算机，然后[将证书导出为 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。

### <a name="upload-your-ssl-certificate"></a>上传 SSL 证书

若要上传 SSL 证书，请在 Web 应用的左侧导航窗格中单击“SSL 证书”。

单击“上传证书”。 

在“PFX 证书文件”中选择 PFX 文件。 在“证书密码”中，键入导出 PFX 文件时创建的密码。

单击“上传” 。

![上传证书](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

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

接下来只需确保 HTTPS 适用于自定义域。 在不同的浏览器中浏览到 `https://<your.custom.domain>`，查看是否能够打开 Web 应用。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> 如果 Web 应用显示证书验证错误，可能是因为使用了自签名证书。
>
> 如果不是这样，可能是在将证书导出为 PFX 文件时遗漏了中间证书。

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>实施 HTTPS

默认情况下，任何人都仍可使用 HTTP 访问 Web 应用。 可以将所有 HTTP 请求都重定向到 HTTPS 端口。

在 Web 应用页的左侧导航窗格中，选择“自定义域”。 然后，在“仅 HTTPS”中，选择“启用”。

![实施 HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

该操作完成后，将导航到指向应用的任一 HTTP URL。 例如：

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="automate-with-scripts"></a>使用脚本自动化

可以在 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview) 中使用脚本自动完成 Web 应用的 SSL 绑定。

### <a name="azure-cli"></a>Azure CLI

以下命令上传已导出的 PFX 文件并获取指纹。

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

以下命令使用前一命令获取的指纹添加基于 SNI 的 SSL 绑定。

```bash
az webapp config ssl bind \
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
## <a name="public-certificates-optional"></a>公用证书（可选）
可向自己的 Web 应用上传[公用证书](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/)。 还可以对应用服务环境中的应用使用公用证书。 若要将证书存储在 LocalMachine 证书存储中，需要在应用服务环境中使用 Web 应用。 有关详细信息，请参阅[如何将公用证书配置到 Web 应用](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer)。

![上传公用证书](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

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

有关详细信息，请参阅[在 Azure 应用服务的应用程序代码中使用 SSL 证书](app-service-web-ssl-cert-load.md)。
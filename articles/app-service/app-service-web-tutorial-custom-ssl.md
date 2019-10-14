---
title: 上传并绑定 SSL 证书 - Azure 应用服务 | Microsoft Docs
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
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c5095bc8c274ef0985b00459b0d088371ab24d88
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177046"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>教程：将 SSL 证书上传并绑定到 Azure 应用服务

[Azure 应用服务](overview.md)提供高度可缩放、自修补的 Web 托管服务。 本教程介绍了如何使用从受信任的证书颁发机构处购买的证书来确保应用服务中自定义域的安全。 此外还介绍了如何上传应用所需的任何专用证书和公共证书。 完成本教程后，便可以访问自定义 DNS 域的 HTTPS 终结点上的应用。

![包含自定义 SSL 证书的 Web 应用](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 升级应用的定价层
> * 使用证书确保自定义域的安全
> * 上传私有证书
> * 上传公用证书
> * 续订证书
> * 实施 HTTPS
> * 强制实施 TLS 1.1/1.2
> * 使用脚本自动完成 TLS 管理

## <a name="prerequisites"></a>先决条件

完成本教程：

- [创建应用服务应用](/azure/app-service/)
- [将自定义 DNS 名称映射到应用服务应用](app-service-web-tutorial-custom-domain.md)（若要确保自定义域的安全）
- 从受信任的证书颁发机构获取证书
- 拥有用于对证书请求进行签名的私钥（针对私有证书）

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>准备私有证书

若要确保域的安全，证书必须满足下列所有要求：

* 配置用于服务器身份验证
* 已由受信任的证书颁发机构签名
* 已导出为受密码保护的 PFX 文件
* 包含长度至少为 2048 位的私钥
* 包含证书链中的所有中间证书

> [!TIP]
> 如果需要获取自定义 SSL 证书，可以直接在 Azure 门户中获取，然后将其导入应用。 请遵循[应用服务证书教程](web-sites-purchase-ssl-web-site.md)。

> [!NOTE]
> 椭圆曲线加密 (ECC) 证书  可用于应用服务，但本文不予讨论。 请咨询证书颁发机构，了解有关创建 ECC 证书的确切步骤。

从证书提供者处获得证书以后，请执行此部分的步骤，使证书可供应用服务使用。

### <a name="merge-intermediate-certificates"></a>合并中间证书

如果证书颁发机构在证书链中提供了多个证书，则需按顺序合并证书。

若要执行此操作，请在文本编辑器中打开收到的所有证书。

创建名为 mergedcertificate.crt  的合并证书文件。 在文本编辑器中，将每个证书的内容复制到此文件。 证书的顺序应遵循证书链中的顺序，以你的证书开头，以根证书结尾， 如以下示例所示：

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

如果使用 IIS 或 Certreq.exe  生成证书请求，请将证书安装到本地计算机，然后[将证书导出为 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。

现在可以将证书上传到应用服务了。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>确保自定义域的安全

> [!TIP]
> 如果需要获取自定义 SSL 证书，可以直接在 Azure 门户中获取，然后将其绑定到应用。 请遵循[应用服务证书教程](web-sites-purchase-ssl-web-site.md)。

若要使用第三方证书确保[自定义域](app-service-web-tutorial-custom-domain.md)的安全，请上传[准备好的私有证书](#prepare-a-private-certificate)，然后将其绑定到自定义域，不过应用服务为你简化了此过程。 请执行以下步骤：

单击应用左侧导航栏中的“自定义域”，然后针对需要保护的域单击“添加绑定”。   如果看不到与某个域对应的“添加绑定”选项，则表明该域已受到保护，其 SSL 状态应该为“安全”。  

![为域添加绑定](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

单击“上传证书”。 

在“PFX 证书文件”  中选择 PFX 文件。 在“证书密码”  中，键入导出 PFX 文件时创建的密码。

单击“上传” 。 

![为域上传证书](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

等待 Azure 上传证书并启动“SSL 绑定”对话框。

在“SSL 绑定”对话框中选择已上传的证书和 SSL 类型，然后单击“添加绑定”  。

> [!NOTE]
> 支持以下 SSL 类型：
>
> - **[基于 SNI 的 SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - 可添加多个基于 SNI 的 SSL 绑定。 选择此选项可以使用多个 SSL 证书来保护同一 IP 地址上的多个域。 大多数新式浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI（在[服务器名称指示](https://wikipedia.org/wiki/Server_Name_Indication)中了解更全面的浏览器支持信息）。
> - 基于 IP 的 SSL  - 只能添加一个基于 IP 的 SSL 绑定。 选择此选项只能使用一个 SSL 证书来保护专用公共 IP 地址。 若要保护多个域，必须使用同一个 SSL 证书来保护所有这些域。 这是 SSL 绑定的传统选项。

![将 SSL 绑定到域](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

域的 SSL 状态现在应更改为“安全”。 

![受保护的域](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> “自定义域”中的状态为“安全”意味着已使用证书保护该域，但应用服务并未检查该证书是自签名证书还是已过期证书，这可能也会导致浏览器异常，例如显示错误或警告。  

## <a name="remap-a-record-for-ip-ssl"></a>重新映射 IP SSL 的 A 记录

如果不在应用中使用基于 IP 的 SSL，请跳到[针对自定义域测试 HTTPS](#test)。

默认情况下，应用使用共享的公共 IP 地址。 将证书与基于 IP 的 SSL 绑定时，应用服务会为应用创建新的专用 IP 地址。

如果已将 A 记录映射到应用，请使用这个新的专用 IP 地址更新域注册表。

将使用新的专用 IP 地址更新应用的“自定义域”页。  [复制此 IP 地址](app-service-web-tutorial-custom-domain.md#info)，然后[将 A 记录重新映射](app-service-web-tutorial-custom-domain.md#map-an-a-record)到此新 IP 地址。

<a name="test"></a>

## <a name="test-https"></a>测试 HTTPS

接下来只需确保 HTTPS 适用于自定义域。 在各种浏览器中，浏览到 `https://<your.custom.domain>` 以查看其是否适合应用。

![在门户中导航到 Azure 应用](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> 如果应用显示证书验证错误，可能是因为使用自签名证书。
>
> 如果不是这样，可能是在将证书导出为 PFX 文件时遗漏了中间证书。

## <a name="renew-certificates"></a>续订证书

在删除某个绑定时，即使该绑定是基于 IP 的，入站 IP 地址也可能会更改。 在续订已进行基于 IP 的绑定的证书时，了解这一点尤为重要。 若要避免应用的 IP 地址更改，请按顺序执行以下步骤：

1. 上传新证书。
2. 将新证书绑定到所需的自定义域，不要删除旧证书。 此操作替换而不是删除旧的绑定。
3. 删除旧证书。 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>实施 HTTPS

默认情况下，任何人都仍可使用 HTTP 访问应用。 可以将所有 HTTP 请求都重定向到 HTTPS 端口。

在应用页的左侧导航窗格中，选择“SSL 设置”  。 然后，在“仅 HTTPS”  中，选择“启用”  。

![实施 HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

该操作完成后，将导航到指向应用的任一 HTTP URL。 例如：

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>强制实施 TLS 版本

应用默认情况下允许 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2，这是行业标准（例如 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)）建议的 TLS 级别。 若要强制实施不同的 TLS 版本，请按照下列步骤操作：

在应用页的左侧导航窗格中，选择“SSL 设置”  。 然后，在“TLS 版本”  中，选择所需的最低 TLS 版本。 此设置仅控制入站调用。 

![强制实施 TLS 1.1 或 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

该操作完成后，你的应用将拒绝使用更低 TLS 版本的所有连接。

## <a name="automate-with-scripts"></a>使用脚本自动化

可以在 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview) 中使用脚本自动完成应用的 SSL 绑定。

### <a name="azure-cli"></a>Azure CLI

以下命令上传已导出的 PFX 文件并获取指纹。

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

以下命令使用前一命令获取的指纹添加基于 SNI 的 SSL 绑定。

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

以下命令强制应用使用 https。

```azurecli-interactive
az webapp update \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --https-only true
```

以下命令强制实施最低的 TLS 版本 (1.2)。

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下命令上传已导出的 PFX 文件并添加基于 SNI 的 SSL 绑定。

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>在代码中使用证书

如果应用需要连接到远程资源，而远程资源需要证书身份验证，则可将公用或私有证书上传到应用。 不需将这些证书绑定到应用中的任何自定义域。 有关详细信息，请参阅[在 Azure 应用服务的应用程序代码中使用 SSL 证书](app-service-web-ssl-cert-load.md)。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 升级应用的定价层
> * 将自定义证书绑定到应用服务
> * 续订证书
> * 实施 HTTPS
> * 强制实施 TLS 1.1/1.2
> * 使用脚本自动完成 TLS 管理

继续学习下一教程，了解如何使用 Azure 内容分发网络。

> [!div class="nextstepaction"]
> [向 Azure 应用服务添加内容分发网络 (CDN)](../cdn/cdn-add-to-web-app.md)

有关详细信息，请参阅[在 Azure 应用服务的应用程序代码中使用 SSL 证书](app-service-web-ssl-cert-load.md)。

---
title: 在 Azure 应用服务的应用程序代码中使用已上传的 SSL 证书 | Microsoft Docs
description: ''
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.openlocfilehash: 6800bf766deb2044d400f92dbe370fa15bdd5f00
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2017
ms.locfileid: "26047598"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>在 Azure 应用服务的应用程序代码中使用 SSL 证书

本操作方法指南介绍如何使用已上传或导入到应用服务的应用程序代码中的 SSL 证书之一。 一个用例是应用访问需要证书身份验证的外部服务。 

这种在代码中使用 SSL 证书的方法利用应用服务中的 SSL 功能，要求应用位于“基本”或更高层。 替代方法是在应用程序目录中包含证书文件并直接加载它（请参阅[替代方案：以文件形式加载证书](#file)）。 但是，使用此替代方案无法对应用程序代码或开发人员隐藏证书中的私钥。 此外，如果应用程序代码在开源存储库中，则并不适合将包含私钥的证书保存在存储库中。

让应用服务管理 SSL 证书时，可以分开维护证书和应用程序代码，并保护敏感数据。

## <a name="prerequisites"></a>先决条件

若要完成本操作说明指南：

- [创建应用服务应用](/azure/app-service/)
- [将自定义 DNS 名称映射到 Web 应用](app-service-web-tutorial-custom-domain.md)
- [将 SSL 证书上传](app-service-web-tutorial-custom-ssl.md)或者[将应用服务证书导入](web-sites-purchase-ssl-web-site.md)到 Web 应用


## <a name="load-your-certificates"></a>加载证书

若要使用已上传到或者导入到应用服务的证书，请先使该证书可由应用程序代码访问。 为此可以使用 `WEBSITE_LOAD_CERTIFICATES` 应用设置。

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中，打开 Web 应用页。

在左侧导航栏中，单击“SSL 证书”。

![上传的证书](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

为此 Web 应用上传和导入的所有 SSL 证书及其指纹都显示在此处。 复制想要使用的证书的指纹。

在左侧导航窗格中，单击“应用程序设置”。

添加名为 `WEBSITE_LOAD_CERTIFICATES` 的应用设置，并将其值设置为证书的指纹。 若要使多个证书可访问，请使用逗号分隔的指纹值。 若要使所有证书可访问，请将值设置为 `*`。 

![配置应用设置](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

完成后，单击“保存”。

现在，配置的证书可供代码使用。

## <a name="use-certificate-in-c-code"></a>在 C# 代码中使用证书

证书可供访问后，可在 C# 代码中通过证书指纹访问它。 以下代码加载具有指纹 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 的证书。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>替代方案：以文件形式加载证书

本部分介绍如何加载应用程序目录中的证书文件。 

以下 C# 示例从应用存储库的 `certs` 目录中加载名为 `mycert.pfx` 的证书。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```


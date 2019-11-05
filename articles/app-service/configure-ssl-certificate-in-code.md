---
title: 在应用程序代码中使用 SSL 证书-Azure App Service |Microsoft Docs
description: 了解如何使用客户端证书连接到需要它们的远程资源。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513687"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>在 Azure 应用服务的应用程序代码中使用 SSL 证书

应用服务应用代码可充当客户端，并可访问需要证书身份验证的外部服务。 本操作方法指南介绍如何在应用程序代码中使用公共或专用证书。

这种在代码中使用证书的方法利用应用服务中的 SSL 功能，要求应用位于“基本”层或更高层。 也可[将证书文件包括到应用存储库中](#load-certificate-from-file)，但建议不要对专用证书这样做。

让应用服务管理 SSL 证书时，可以分开维护证书和应用程序代码，并保护敏感数据。

## <a name="prerequisites"></a>必备组件

按照本操作方法指南操作：

- [创建应用服务应用](/azure/app-service/)
- [将证书添加到应用](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>查找指纹

在<a href="https://portal.azure.com" target="_blank">Azure 门户</a>中，从左侧菜单中选择 "**应用服务**" >  **\<"应用名称**" ">"。

在应用程序的左侧导航栏中，选择 " **TLS/SSL 设置**"，然后选择 "**私钥证书（.pfx）** " 或 "**公钥证书（.cer）** "。

查找要使用的证书，并复制指纹。

![复制证书指纹](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>加载证书

若要在应用代码中使用证书，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中运行以下命令，将其指纹添加到 `WEBSITE_LOAD_CERTIFICATES` 应用设置：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要使所有证书可供访问，请将值设置为 `*`。

> [!NOTE]
> 此设置将指定的证书放置在[当前 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)存储中的大多数定价层，但在**隔离**层中（即应用在[应用服务环境](environment/intro.md)中运行），将证书放入[本地 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)店.
>

现在，配置的证书可供代码使用了。

## <a name="load-the-certificate-in-code"></a>在代码中加载证书

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
## <a name="load-certificate-from-file"></a>从文件加载证书

如需从应用程序目录加载证书文件，则可首选某些方式，例如，使用 [FTPS](deploy-ftp.md) 会比使用 [Git](deploy-local-git.md) 更好。 应将专用证书之类的敏感信息置于源代码管理之外。

即使是在 .NET 代码中直接加载文件，库仍会验证是否加载当前的用户配置文件。 若要加载当前用户配置文件，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中用以下命令设置 "`WEBSITE_LOAD_USER_PROFILE` 应用" 设置。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

设置此设置以后，以下 C# 示例会从应用存储库的 `mycert.pfx` 目录中加载名为 `certs` 的证书。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>更多资源

* [使用 SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
* [实施 HTTPS](configure-ssl-bindings.md#enforce-https)
* [实施 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [常见问题解答：应用服务证书](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)

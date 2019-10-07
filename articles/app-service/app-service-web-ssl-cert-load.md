---
title: 在应用程序代码中使用客户端 SSL 证书 - Azure 应用服务 | Microsoft Docs
description: 了解如何使用客户端证书连接到需要它们的远程资源。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c8c270681794621b2a12671d4bcf350cd6cc4d8
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981117"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>在 Azure 应用服务的应用程序代码中使用 SSL 证书

本操作方法指南介绍如何在应用程序代码中使用公共或专用证书。 一个用例是应用访问需要证书身份验证的外部服务。

这种在代码中使用证书的方法利用应用服务中的 SSL 功能，要求应用位于“基本”层或更高层。 也可[将证书文件包括到应用存储库中](#load-certificate-from-file)，但建议不要对专用证书这样做。

让应用服务管理 SSL 证书时，可以分开维护证书和应用程序代码，并保护敏感数据。

## <a name="upload-a-private-certificate"></a>上传私有证书

在上传专用证书之前，请确保[它满足所有要求](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate)，唯一例外是不需要将它配置为进行服务器身份验证。

准备好上传时，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中运行以下命令。

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

复制证书指纹并参阅[使证书可供访问](#make-the-certificate-accessible)。

## <a name="upload-a-public-certificate"></a>上传公用证书

公共证书可以使用 *.cer* 格式。 若要上传公共证书，请在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中导航到应用。

在应用的左侧导航栏中单击“SSL 设置” > “公共证书(.cer)” > “上传公共证书”。

在“名称”中，键入证书的名称。 在“CER 证书文件”中，选择你的 CER 文件。

单击“上传” 。

![上传公用证书](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

上传证书以后，请复制证书指纹并参阅[使证书可供访问](#make-the-certificate-accessible)。

## <a name="import-an-app-service-certificate"></a>导入应用服务证书

请参阅[为 Azure 应用服务购买和配置 SSL 证书](web-sites-purchase-ssl-web-site.md)。

导入证书以后，请复制证书指纹并参阅[使证书可供访问](#make-the-certificate-accessible)。

## <a name="make-the-certificate-accessible"></a>使证书可供访问

若要在应用代码中使用已上传或已导入的证书，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中运行以下命令，使其指纹可通过 `WEBSITE_LOAD_CERTIFICATES` 应用设置访问：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要使所有证书可供访问，请将值设置为 `*`。

> [!NOTE]
> 此设置将[当前 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)存储中的指定证书放入大多数定价层，但如果应用在**隔离**层上运行（即应用在[应用服务环境](environment/intro.md)中运行），则可能需要签入[本地Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)存储库。
>

![配置应用设置](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

完成后，单击“保存”。

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

即使是在 .NET 代码中直接加载文件，库仍会验证是否加载当前的用户配置文件。 若要加载当前用户配置文件，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中用以下命令设置 `WEBSITE_LOAD_USER_PROFILE` 应用设置。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

设置此设置以后，以下 C# 示例会从应用存储库的 `certs` 目录中加载名为 `mycert.pfx` 的证书。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

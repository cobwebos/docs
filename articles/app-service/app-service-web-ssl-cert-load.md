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
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475091"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>在 Azure 应用服务的应用程序代码中使用 SSL 证书

本操作方法指南演示如何在应用程序代码中使用公共或私有证书。 一个用例是应用访问需要证书身份验证的外部服务。

在代码中使用证书向这种方法利用了 SSL 在应用服务中，这要求您的应用程序中的功能**基本**层或更高版本。 此外，也可以[证书文件包含应用存储库](#load-certificate-from-file)，但并不是建议的做法，对于私有证书。

让应用服务管理 SSL 证书时，可以分开维护证书和应用程序代码，并保护敏感数据。

## <a name="upload-a-private-certificate"></a>上传私有证书

在之前上传私有证书，请确保[满足所有要求](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate)，只不过它不需要进行服务器身份验证配置。

当准备好要上传后时，在中运行以下命令<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>。

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

复制证书指纹，请参阅[使证书可访问](#make-the-certificate-accessible)。

## <a name="upload-a-public-certificate"></a>上传公用证书

中支持公用证书 *.cer*格式。 若要上传公用证书<a href="https://portal.azure.com" target="_blank">Azure 门户</a>，并导航到您的应用程序。

单击**SSL 设置** > **公共证书 (.cer)**  > **上载公共证书**从您的应用程序的左侧导航窗格。

在中**名称**，为证书键入一个名称。 在中**CER 证书文件**，选择你的 CER 文件。

单击“上传” 。 

![上传公用证书](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

后上传证书，复制证书指纹，请参阅[使证书可访问](#make-the-certificate-accessible)。

## <a name="import-an-app-service-certificate"></a>导入应用服务证书

请参阅[购买和配置 Azure 应用服务 SSL 证书](web-sites-purchase-ssl-web-site.md)。

一旦导入证书，复制证书指纹，请参阅[使证书可访问](#make-the-certificate-accessible)。

## <a name="make-the-certificate-accessible"></a>使证书可访问

若要在应用代码中使用已上传或导入证书，使其指纹可通过访问`WEBSITE_LOAD_CERTIFICATES`应用设置，通过运行以下命令<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要使所有证书可访问，请将值设置为`*`。

> [!NOTE]
> 此设置会放置在指定的证书[当前 User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)适用于大多数的定价层，但在应用商店**独立**层 (即在应用中运行[应用服务环境](environment/intro.md))，它将中的证书[本地 Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)存储。
>

![配置应用设置](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

完成后，单击“保存”  。

配置的证书现已准备好供你的代码。

## <a name="load-the-certificate-in-code"></a>加载在代码中的证书

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

如果需要从你的应用程序目录中加载证书文件，则最好将其使用上传[FTPS](deploy-ftp.md)而不是[Git](deploy-local-git.md)，例如。 你应该保留出源代码管理的私有证书等敏感数据。

即使你要在.NET 代码中直接加载文件，在库仍验证是否加载当前用户配置文件。 若要加载当前用户配置文件，请设置`WEBSITE_LOAD_USER_PROFILE`使用以下命令中的应用设置<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

此设置设置后，以下C#的示例加载名为的证书`mycert.pfx`从`certs`应用程序的存储库的目录。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

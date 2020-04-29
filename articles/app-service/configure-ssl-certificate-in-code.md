---
title: 在代码中使用 TLS/SSL 证书
description: 了解如何在代码中使用客户端证书。 使用客户端证书向远程资源进行身份验证，或使用这些证书运行加密任务。
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811699"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>在 Azure 应用服务中通过代码使用 TLS/SSL 证书

在应用程序代码中，可以访问[已添加到应用服务的公用证书或私用证书](configure-ssl-certificate.md)。 应用代码可以充当客户端并可访问需要证书身份验证的外部服务，否则可能需要执行加密任务。 本操作方法指南介绍如何在应用程序代码中使用公共或专用证书。

在代码中使用证书的这种方法将使用应用服务中的 TLS 功能，这要求应用位于**基本**层或更高级别。 如果应用位于“免费”或“共享”层，则你可以[在应用存储库中包含证书文件](#load-certificate-from-file)。  

当你让应用服务管理你的 TLS/SSL 证书时，你可以单独维护证书和应用程序代码，并保护敏感数据。

## <a name="prerequisites"></a>先决条件

按照本操作方法指南操作：

- [创建应用服务应用](/azure/app-service/)
- [将证书添加到应用](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>查找指纹

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>的左侧菜单中，选择“应用程序服务” > “\<app-name>”   。

在应用的左侧导航栏中选择“TLS/SSL 设置”，然后选择“私钥证书(.pfx)”或“公钥证书(.cer)”。   

找到要使用的证书并复制指纹。

![复制证书指纹](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>使证书可供访问

若要在应用代码中访问证书，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中运行`WEBSITE_LOAD_CERTIFICATES`以下命令，将其指纹添加到应用设置：

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

若要使所有证书可供访问，请将值设置为 `*`。

## <a name="load-certificate-in-windows-apps"></a>在 Windows 应用中加载证书

Windows 证书存储中的 Windows 托管应用可以通过 `WEBSITE_LOAD_CERTIFICATES` 应用设置访问指定的证书，该存储的位置取决于[定价层](overview-hosting-plans.md)：

- “隔离”层 - 位于 [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)。  
- 所有其他层 - 位于 [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)。

在 C# 代码中，可按证书指纹访问证书。 以下代码加载具有指纹 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 的证书。

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

在 Java 代码中，可以使用“使用者公用名”字段从“Windows-MY”存储访问证书（请参阅[公钥证书](https://en.wikipedia.org/wiki/Public_key_certificate)）。 以下代码演示如何加载私钥证书：

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

对于不支持或不充分支持 Windows 证书存储的语言，请参阅[从文件加载证书](#load-certificate-from-file)。

## <a name="load-certificate-in-linux-apps"></a>在 Linux 应用中加载证书

`WEBSITE_LOAD_CERTIFICATES`应用设置使指定证书可供 Linux 托管应用（包括自定义容器应用）作为文件访问。 这些文件位于以下目录中：

- 专用证书- `/var/ssl/private` （ `.p12`文件）
- 公共证书- `/var/ssl/certs` （ `.der`文件）

证书文件名是证书指纹。 以下 c # 代码演示了如何在 Linux 应用程序中加载公共证书。

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

若要了解如何从 node.js、PHP、Python、Java 或 Ruby 中的文件加载 TLS/SSL 证书，请参阅相应语言或 web 平台的文档。

## <a name="load-certificate-from-file"></a>从文件加载证书

例如，如需加载手动上传的证书文件，则最好是使用 [FTPS](deploy-ftp.md) 而不是 [Git](deploy-local-git.md) 上传证书。 应将专用证书之类的敏感信息置于源代码管理之外。

> [!NOTE]
> 即使从文件加载证书，Windows 上的 ASP.NET 和 ASP.NET Core 也必须访问证书存储。 若要在 Windows .NET 应用中加载证书文件，请在<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>中使用以下命令加载当前用户配置文件：
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> 在代码中使用证书的这种方法将使用应用服务中的 TLS 功能，这要求应用位于**基本**层或更高级别。

以下 C# 示例从应用中的相对路径加载公用证书：

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

若要了解如何从 node.js、PHP、Python、Java 或 Ruby 中的文件加载 TLS/SSL 证书，请参阅相应语言或 web 平台的文档。

## <a name="more-resources"></a>更多资源

* [在 Azure 应用服务中使用 TLS/SSL 绑定保护自定义 DNS 名称](configure-ssl-bindings.md)
* [实施 HTTPS](configure-ssl-bindings.md#enforce-https)
* [实施 TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [常见问题解答：应用服务证书](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)

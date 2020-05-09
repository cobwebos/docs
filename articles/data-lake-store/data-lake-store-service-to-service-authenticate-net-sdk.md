---
title: .NET-服务到服务身份验证-Data Lake Storage Gen1
description: 了解如何通过 .NET SDK 使用 Azure Active Directory 实现 Azure Data Lake Storage Gen1 服务到服务身份验证
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bac5fa95ca5ce4e289d4dd18cba9238da7b8a2e2
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691622"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>使用 .NET SDK 进行 Azure Data Lake Storage Gen1 服务到服务身份验证
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
> * [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-service-to-service-authenticate-python.md)
> * [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

本文介绍如何使用 .NET SDK 进行 Azure Data Lake Storage Gen1 服务到服务身份验证。 有关使用 .NET SDK 进行 Data Lake Storage Gen1 最终用户身份验证的信息，请参阅[使用 .NET SDK 进行 Data Lake Storage Gen1 最终用户身份验证](data-lake-store-end-user-authenticate-net-sdk.md)。

## <a name="prerequisites"></a>先决条件
* **Visual Studio 2013 或更高**版本。 以下说明使用 Visual Studio 2019。

* **一个 Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

* **创建 Azure Active Directory“Web”应用程序**。 必须已完成[使用 Azure Active Directory 进行 Data Lake Storage Gen1 服务到服务身份验证](data-lake-store-service-to-service-authenticate-using-active-directory.md)中的步骤。

## <a name="create-a-net-application"></a>创建 .NET 应用程序
1. 在 Visual Studio 中，选择 "**文件**" 菜单，选择 "**新建**"，然后选择 "**项目**"。
2. 选择 "**控制台应用（.NET Framework）**"，然后选择 "**下一步**"。
3. 在“项目名称”**** 中，输入 `CreateADLApplication`，然后选择“创建”****。

4. 将 NuGet 包添加到项目。

   1. 在解决方案资源管理器中右键单击项目名称，单击“管理 NuGet 包” ****。
   2. 在 " **NuGet 包管理器**" 选项卡中，确保 "**包源**" 设置为**Nuget.org** ，并选中 "**包括预发行**版" 复选框。
   3. 搜索并安装以下 NuGet 包：

      * `Microsoft.Azure.Management.DataLake.Store` - 本教程使用 v2.1.3-预览版。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教程使用 v2.2.12。

        ![添加 NuGet 源](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "创建新的 Azure Data Lake 帐户")
   4. 关闭**NuGet 包管理器**。

5. 打开“Program.cs” ****，删除现有代码，并包含以下语句，添加对命名空间的引用。

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>使用客户端密码的服务到服务身份验证
在 .NET 客户端应用程序中添加此代码片段。 将占位符值替换为从 Azure AD Web 应用程序（作为必备组件列出）检索到的值。 此代码片段可让你使用 Azure AD Web 应用程序的客户端密码/密钥通过 Data Lake Storage Gen1 以非交互方式对应用程序进行身份验证****。

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

上述代码片段将使用 helper 函数 `GetCreds_SPI_SecretKey`。 可在[此处（GitHub 上）](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey)获取此 helper 函数的代码。

## <a name="service-to-service-authentication-with-certificate"></a>使用证书的服务到服务身份验证

在 .NET 客户端应用程序中添加此代码片段。 将占位符值替换为从 Azure AD Web 应用程序（作为必备组件列出）检索到的值。 此代码片段可让你使用 Azure AD Web 应用程序的证书通过 Data Lake Storage Gen1 以非交互方式对应用程序进行身份验证****。 有关如何创建 Azure AD 应用程序的说明，请参阅[使用证书创建服务主体](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)。

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

上述代码片段将使用 helper 函数 `GetCreds_SPI_Cert`。 可在[此处（GitHub 上）](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert)获取此 helper 函数的代码。

## <a name="next-steps"></a>后续步骤
本文介绍了如何通过 .NET SDK 使用服务到服务身份验证进行 Data Lake Storage Gen1 身份验证。 接下来，可以查看以下介绍如何使用 .NET SDK 在 Data Lake Storage Gen1 中执行操作的文章。

* [使用 .NET SDK 对 Data Lake Storage Gen1 上的帐户管理操作](data-lake-store-get-started-net-sdk.md)
* [使用 NET SDK 在 Data Lake Storage Gen1 中进行的数据操作](data-lake-store-data-operations-net-sdk.md)

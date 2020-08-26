---
title: 编写应用身份验证码
titleSuffix: Azure Digital Twins
description: 请参阅如何在客户端应用程序中编写身份验证代码
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 00219dbebb8e84c21b9e5b84cf71309c63fc518e
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855967"
---
# <a name="write-client-app-authentication-code"></a>编写客户端应用身份验证代码

[设置 Azure 数字孪生实例和身份验证](how-to-set-up-instance-scripted.md)后，可以创建将用于与实例进行交互的客户端应用程序。 设置入门客户端项目后，本文将介绍 **如何在该客户端应用程序中编写代码，以便** 对 Azure 数字孪生实例进行身份验证。

本文提供了两种方法来示例代码。 您可以根据自己的选择，使用最适合自己的语言：
* 示例代码的第一部分使用 Azure 数字孪生 .NET (c # ) SDK。 SDK 是适用于 .NET 的 Azure SDK 的一部分，位于： [*适用于 .net 的 Azure IoT 数字克隆客户端库*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。
* 示例代码的第二部分适用于不使用 .NET SDK 的用户，而是使用其他语言的 AutoRest 生成的 Sdk。 有关此策略的详细信息，请参阅 [*如何：创建适用于 Azure 数字孪生的自定义 Sdk AutoRest*](how-to-create-custom-sdks.md)。

你还可以在 [*操作方法：使用 Azure 数字孪生 api 和 sdk*](how-to-use-apis-sdks.md)中了解有关 Azure 数字孪生的 Api 和 sdk 的详细信息。

## <a name="prerequisites"></a>先决条件

首先，请完成 [*操作方法：设置实例和身份验证*](how-to-set-up-instance-scripted.md)中的设置步骤。 这将确保你有 Azure 数字孪生实例，你的用户具有访问权限，并且已为客户端应用程序设置了权限。 完成此设置后，就可以编写客户端应用代码了。

若要继续，你需要一个客户端应用程序项目，可在其中编写代码。 如果尚未设置客户端应用程序项目，请使用与本教程一起使用的所选语言创建基本项目。

## <a name="authentication-and-client-creation-net-c-sdk"></a>身份验证和客户端创建： .NET (c # ) SDK

首先，在项目中包括以下包，以便将 .NET SDK 和身份验证工具用于本操作方法：
* `Azure.DigitalTwins.Core` (版本 `1.0.0-preview.2`) 
* `Azure.Identity` (版本 `1.1.1`) 

根据您选择的工具，您可以使用 Visual Studio 包管理器或 `dotnet` 命令行工具包含包。 

还需要以下 using 语句：

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
若要使用 .NET SDK 进行身份验证，请使用在 [Azure](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) 中定义的凭据获取方法之一。 下面是在同一应用程序) 中经常使用的两个 (：

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) 适用于交互式应用程序，可用于创建经过身份验证的 SDK 客户端
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) 非常适合于需要 (MSI) 的托管标识，这是使用 Azure Functions

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 方法
[InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)方法适用于交互式应用程序，并将打开一个 web 浏览器用于身份验证。

若要使用交互式浏览器凭据创建经过身份验证的 SDK 客户端，请添加以下代码：

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> 虽然你可以将客户端 ID、租户 ID 和实例 URL 直接放置在代码中（如上所示），但最好是让你的代码从配置文件或环境变量获取这些值。

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 方法
 如果需要[ (MSI) 的托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)（例如，使用 Azure Functions 时）， [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet)方法非常有用。
在 Azure 函数中，可以使用如下所示的托管标识凭据：

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

请参阅 [*如何：设置用于处理数据的 Azure 函数*](how-to-create-azure-function.md) 以获取更完整的示例，该示例介绍函数上下文中的一些重要配置选项。

此外，若要在函数中使用身份验证，请记住：
* [启用托管标识](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* 使用适当的[环境变量](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 向函数应用分配权限，使其能够访问数字孪生 Api。 有关 Azure Functions 过程的详细信息，请参阅 [*如何：设置用于处理数据的 Azure 函数*](how-to-create-azure-function.md)。

## <a name="authentication-with-an-autorest-generated-sdk"></a>使用 AutoRest 生成的 SDK 进行身份验证

如果使用的不是 .NET，则可以选择使用所选的语言构建 SDK 库，如 [*操作方法：使用 AutoRest 创建 Azure 数字孪生的自定义 sdk*](how-to-create-custom-sdks.md)中所述。

本部分介绍如何在这种情况下进行身份验证。

### <a name="prerequisites"></a>先决条件

首先，应完成使用 AutoRest [*：使用 AutoRest 创建 Azure 数字孪生的自定义*](how-to-create-custom-sdks.md)sdk 中的步骤，创建包含的自定义 SDK 的步骤。

此示例使用通过 AutoRest 生成的 Typescript SDK。 因此，它还需要：
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-chap](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>最小身份验证代码示例

若要使用 Azure 服务对应用进行身份验证，你可以在客户端应用中使用以下最少代码。

你将需要 *应用程序 (客户端) id* 和 *目录 (之前的租户) Id* 以及 Azure 数字孪生实例的 URL。

> [!TIP]
> Azure 数字孪生实例的 URL 是通过将 *https://* 添加到 Azure 数字孪生实例的 *主机名*的开头来完成的。 若要查看 *主机名*以及实例的所有属性，可以运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。 可以使用 `az account show --query tenantId` 命令查看 *目录 (租户) ID*。 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

请注意，上面的代码会将客户端 ID、租户 ID 和实例 URL 直接放置在代码中，这是一个很好的做法，就是让代码改为从配置文件或环境变量获取这些值。

MSAL 有更多选项可用于实现缓存和其他身份验证流等功能。 有关此内容的详细信息，请参阅 [*Microsoft 身份验证库概述 (MSAL) *](../active-directory/develop/msal-overview.md)。

## <a name="next-steps"></a>后续步骤

详细了解如何在 Azure 数字孪生中运行安全性：
* [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)

或者，现在已经设置了身份验证，请转到在实例中创建模型：
* [*操作说明：管理自定义模型*](how-to-manage-model.md)
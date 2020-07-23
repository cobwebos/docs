---
title: 对客户端应用程序进行身份验证
titleSuffix: Azure Digital Twins
description: 请参阅如何针对 Azure 数字孪生服务对客户端应用程序进行身份验证。
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390816"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>使用 Azure 数字孪生对客户端应用程序进行身份验证

[创建 Azure 数字孪生实例](how-to-set-up-instance.md)后，可以创建将用于与实例进行交互的客户端应用程序。 设置入门客户端项目后，本文介绍如何使用 Azure 数字孪生实例正确验证该客户端应用程序。

这需要执行两个步骤来完成：
1. 创建应用注册
2. 编写客户端应用程序中的身份验证代码

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>创建应用注册

若要在客户端应用程序中对 Azure 数字孪生进行身份验证，需要在[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)中设置**应用注册**。

通过此应用注册，你可以配置对[Azure 数字孪生 api](how-to-use-apis-sdks.md)的访问权限。 你的客户端应用会根据应用注册进行身份验证，因此，会将配置的访问权限授予 Api。

若要创建应用注册，需要提供 Azure 数字孪生 Api 的资源 Id，以及 API 的基准权限。 在工作目录中，打开一个新文件，然后输入以下 JSON 代码片段来配置这些详细信息： 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

将此文件保存为*manifest.js*。

> [!NOTE] 
> 在某些位置，"友好" 的可读字符串 `https://digitaltwins.azure.net` 可用于 Azure 数字孪生资源应用 ID 而不是 GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` 。 例如，在此文档集中，许多示例将使用 MSAL 库进行身份验证，并且可以使用友好字符串。 但是，在创建应用注册的这一步中，需要 ID 的 GUID 格式，如上面所示。 

在 Cloud Shell 窗口中，单击 "上传/下载文件" 图标，然后选择 "上传"。

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="显示上传选项选择的 Cloud Shell 窗口":::
导航到刚刚创建的*manifest.js* ，并单击 "打开"。

接下来，运行以下命令以创建应用注册（根据需要替换占位符）：

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

此命令的输出如下所示。

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="新的 AAD 应用注册":::

创建应用注册后，请单击下面的[链接](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)导航到 Azure 门户中的 AAD 应用注册概述页。

在此概述中，从列表中选择刚创建的应用注册。 这会在如下所示的页面中打开其详细信息：

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure 门户：身份验证 Id":::

记下显示**在页面上**的*应用程序（客户端） id*和*目录（租户） id* 。 稍后将使用这些值对 Azure 数字孪生 Api 的客户端应用进行身份验证。

> [!NOTE]
> 根据你的方案，你可能需要对应用注册进行其他更改。 下面是可能需要满足的一些常见要求：
> * 激活公共客户端访问
> * 为 web 和桌面访问设置特定回复 Url
> * 允许隐式 OAuth2 身份验证流
> * 如果 Azure 订阅是使用 Microsoft 帐户（如 Live、Xbox 或 Hotmail）创建的，则需要在应用注册上设置*signInAudience*以支持个人帐户。
> 设置这些设置的最简单方法是使用[Azure 门户](https://portal.azure.com/)。 有关此过程的详细信息，请参阅[将应用程序注册到 Microsoft 标识平台](https://docs.microsoft.com/graph/auth-register-app-v2)。

## <a name="write-client-app-authentication-code-net-c-sdk"></a>编写客户端应用身份验证代码： .NET （c #） SDK

本节介绍你需要在客户端应用程序中包括的代码，以便使用 .NET （c #） SDK 完成身份验证过程。
Azure 数字孪生 c # SDK 是用于 .NET 的 Azure SDK 的一部分。 它位于此处：[适用于 .net 的 Azure IoT 数字克隆客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。

### <a name="prerequisites"></a>先决条件

如果尚未设置 starter client 应用项目，请创建一个与本教程一起使用的基本 .NET 项目。

若要使用 .NET SDK，需要在项目中包含以下包：
* `Azure.DigitalTwins.Core`（版本 `1.0.0-preview.2` ）
* `Azure.Identity`

根据您选择的工具，您可以通过 Visual Studio 包管理器或 `dotnet` 命令行工具执行此操作。 

### <a name="authentication-and-client-creation-net"></a>身份验证和客户端创建： .NET

若要使用 .NET SDK 进行身份验证，请使用在[Azure](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)中定义的凭据获取方法之一。

下面是常用的两种： 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)。 此方法适用于交互式应用程序，并将显示用于身份验证的 web 浏览器。
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet)。 在需要[托管标识（MSI）](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)的情况下（例如，使用 Azure Functions 时，此方法非常有用。 

还需要以下 using 语句：

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

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

在 Azure 函数中，可以使用如下所示的托管标识凭据：

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

请参阅[如何：设置用于处理数据的 Azure 函数](how-to-create-azure-function.md)以获取更完整的示例，该示例介绍函数上下文中的一些重要配置选项。

此外，若要在函数中使用身份验证，请记住：
* [启用托管标识](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [环境变量](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 向函数应用分配权限，使其能够访问数字孪生 Api。 有关详细信息，请参阅[操作方法：设置用于处理数据的 Azure 函数](how-to-create-azure-function.md)。

## <a name="authentication-in-an-autorest-generated-sdk"></a>在 AutoRest 生成的 SDK 中进行身份验证

如果使用的不是 .NET，则可以选择使用所选的语言构建 SDK 库，如[操作方法：使用 AutoRest 创建 Azure 数字孪生的自定义 sdk](how-to-create-custom-sdks.md)中所述。

本部分介绍如何在这种情况下进行身份验证。

### <a name="prerequisites"></a>先决条件

此示例使用通过 AutoRest 生成的 Typescript SDK。 因此，它还需要：
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-chap](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>最小身份验证代码示例

若要使用 Azure 服务对 .NET 应用程序进行身份验证，你可以在客户端应用程序中使用以下最少代码。

你将需要之前的*应用程序（客户端） id*和*目录（租户） Id*以及 Azure 数字孪生实例的 URL。

> [!TIP]
> Azure 数字孪生实例的 URL 是通过将*https://* 添加到 Azure 数字孪生实例的*主机名*的开头来完成的。 若要查看*主机名*以及实例的所有属性，可以运行 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 。 可以使用 `az account show --query tenantId` 命令查看*目录（租户） ID*。 

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

MSAL 有更多选项可用于实现缓存和其他身份验证流等功能。 有关此内容的详细信息，请参阅[Microsoft 身份验证库概述（MSAL）](../active-directory/develop/msal-overview.md)。

## <a name="next-steps"></a>后续步骤

详细了解如何在 Azure 数字孪生中运行安全性：
* [概念： Azure 数字孪生解决方案的安全性](concepts-security.md)

或者，现在已经设置了身份验证，请转到在实例中创建模型：
* [操作说明：管理自定义模型](how-to-manage-model.md)
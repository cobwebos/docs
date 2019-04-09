---
title: 连接到 Azure 媒体服务 v3 API-.NET
description: 了解如何连接到使用.NET 的媒体服务 v3 API。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: cb6826be8f6a9a8caf54a23b0dbc5f3dda7830d6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59273968"
---
# <a name="connect-to-media-services-v3-api---net"></a>连接到媒体服务 v3 API-.NET

本文介绍您如何连接到 Azure 媒体服务 v3.NET SDK 使用服务主体登录名方法。

## <a name="prerequisites"></a>必备组件

- [创建媒体服务帐户](create-account-cli-how-to.md)。 请务必记住的资源组名称和媒体服务帐户名称
- 安装想要使用用于.NET 开发的工具。 在本文中的步骤说明如何使用[Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)。 可以使用 Visual Studio Code，请参阅[使用C# ](https://code.visualstudio.com/docs/languages/csharp)。 或者，可以使用不同的代码编辑器。

## <a name="create-a-console-application"></a>创建控制台应用程序

1. 启动 Visual Studio。 
1. 从**文件**菜单上，单击**新建** > **项目**。 
1. 创建 **.NET Core**控制台应用程序。

本主题中的示例应用程序面向`netcoreapp2.0`。 中使用异步 'main'，这是从开始提供的代码C#7.1。 请参阅此[博客](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/)的更多详细信息。

## <a name="add-required-nuget-packages"></a>添加所需的 NuGet 包

1. 在 Visual Studio 中，选择**工具** > **NuGet 包管理器** > **NuGet 管理器控制台**。
2. 在中**程序包管理器控制台**窗口中，使用`Install-Package`命令，将添加以下 NuGet 包。 例如，`Install-Package Microsoft.Azure.Management.Media`。

|程序包|描述|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure 媒体服务 SDK。 <br/>若要确保使用最新的 Azure 媒体服务包，请检查[Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)。|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|适用于 NET 的 Azure SDK 的 ADAL 身份验证库|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|从环境变量和本地 JSON 文件中读取配置值|
|`Microsoft.Extensions.Configuration.Json`|从环境变量和本地 JSON 文件中读取配置值
|`WindowsAzure.Storage`|存储 SDK|

## <a name="create-and-configure-the-app-settings-file"></a>创建和配置应用程序设置文件

### <a name="create-appsettingsjson"></a>创建 appsettings.json

1. 转转**常规** > **文本文件**。
1. 将其命名为"appsettings.json"。
1. 为"如果较新则复制"的.json 文件的"复制到输出目录"属性设置 （以使应用程序能够访问该发布时）。

### <a name="set-values-in-appsettingsjson"></a>在 appsettings.json 中的设置值

运行`az ams account sp create`命令中所述[访问 Api](access-api-cli-how-to.md)。 该命令将返回 json，你应将复制到您"appsettings.json"。
 
## <a name="add-configuration-file"></a>添加配置文件

为方便起见，添加一个配置文件，负责读取从"appsettings.json"的值。

1. 将新的.cs 类添加到你的项目。 将它命名为 `ConfigWrapper`。 
1. 以下代码粘贴到此文件中 (此示例假定你具有的命名空间是`ConsoleApp1`)。

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>连接到.NET 客户端

若要开始将媒体服务 API 与 .NET 结合使用，需要创建 AzureMediaServicesClient 对象。 若要创建对象，需要提供客户端所需凭据以使用 Azure AD 连接到 Azure。 在下面的代码中，GetCredentialsAsync 函数创建基于本地配置文件中提供的凭据的 ServiceClientCredentials 对象。

1. 打开 `Program.cs`。
1. 粘贴以下代码：

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="see-also"></a>另请参阅

- [教程：上传、 编码和流式传输视频-.NET](stream-files-tutorial-with-api.md) 
- [教程：使用媒体服务 v3-.NET live Stream](stream-live-tutorial-with-api.md)
- [教程：分析视频使用媒体服务 v3-.NET](analyze-videos-tutorial-with-api.md)
- [从本地文件的.NET 中创建作业输入](job-input-from-local-file-how-to.md)
- [从 HTTPS URL-.NET 创建作业输入](job-input-from-http-how-to.md)
- [使用自定义转换的.NET 进行编码](customize-encoder-presets-how-to.md)
- [使用 AES-128 动态加密和密钥传送服务-.NET](protect-with-aes128.md)
- [使用 DRM 动态加密和许可证传送服务的.NET](protect-with-drm.md)
- [从现有策略-.NET 获取签名密钥](get-content-key-policy-dotnet-howto.md)
- [使用媒体服务-.NET 创建筛选器](filters-dynamic-manifest-dotnet-howto.md)
- [高级视频按需示例的 Azure Functions v2 使用媒体服务 v3](https://aka.ms/ams3functions)

## <a name="next-steps"></a>后续步骤

[.NET 参考](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)

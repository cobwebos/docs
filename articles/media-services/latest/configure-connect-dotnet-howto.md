---
title: 连接到 Azure 媒体服务 v3 API - .NET
description: 本文演示如何通过 .NET 连接到媒体服务 v3 API。
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
ms.date: 09/18/2019
ms.author: juliako
ms.custom: has-adal-ref
ms.openlocfilehash: 6dc05f225e3585b83bd6a57ca47bd5adf97934ea
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201116"
---
# <a name="connect-to-media-services-v3-api---net"></a>连接到媒体服务 v3 API - .NET

本文介绍如何使用服务主体登录方法连接到 Azure 媒体服务 v3 .NET SDK。

## <a name="prerequisites"></a>先决条件

- [创建媒体服务帐户](create-account-cli-how-to.md)。 请务必记住资源组名称和媒体服务帐户名称
- 安装用于 .NET 开发的工具。 本文中的步骤演示如何使用 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)。 你也可以使用 Visual Studio Code，具体请参阅[使用 C#](https://code.visualstudio.com/docs/languages/csharp)。 或者，可以使用其他代码编辑器。

> [!IMPORTANT]
> 查看[命名约定](media-services-apis-overview.md#naming-conventions)。

## <a name="create-a-console-application"></a>创建控制台应用程序

1. 启动 Visual Studio。 
1. 在“文件”菜单中，单击“新建” > “项目”。    
1. 创建 **.NET Core** 控制台应用程序。

本主题中的示例应用面向 `netcoreapp2.0`。 代码使用从 C# 7.1 开始提供的“async main”。 有关更多详细信息，请参阅[此博客](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/)。

## <a name="add-required-nuget-packages"></a>添加所需的 NuGet 包

1. 在 Visual Studio 中，选择“工具” > “NuGet 包管理器” > “NuGet 管理器控制台”。   
2. 在“包管理器控制台”窗口中，使用 `Install-Package` 命令添加以下 NuGet 包。  例如，`Install-Package Microsoft.Azure.Management.Media`。

|程序包|说明|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure 媒体服务 SDK。 <br/>为确保使用最新的 Azure 媒体服务包，请选中 [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)。|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|适用于 Azure SDK for NET 的 ADAL 身份验证库|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|读取环境变量和本地 JSON 文件中的配置值|
|`Microsoft.Extensions.Configuration.Json`|读取环境变量和本地 JSON 文件中的配置值
|`WindowsAzure.Storage`|存储 SDK|

## <a name="create-and-configure-the-app-settings-file"></a>创建并配置应用设置文件

### <a name="create-appsettingsjson"></a>创建 appsettings.json

1. 转到“常规” > “文本文件”。  
1. 将其命名为“appsettings.json”。
1. 将此 .json 文件的“复制到输出目录”属性设置为“如果较新则复制”（使应用程序能够在发布时访问此文件）。

### <a name="set-values-in-appsettingsjson"></a>设置 appsettings.json 中的值

根据[访问 API](access-api-cli-how-to.md) 中所述运行 `az ams account sp create` 命令。 该命令返回要复制到“appsettings.json”中的 JSON。
 
## <a name="add-configuration-file"></a>添加配置文件

为方便起见，请添加负责从“appsettings.json”读取值的配置文件。

1. 将新的 .cs 类添加到项目。 将它命名为 `ConfigWrapper`。 
1. 将以下代码粘贴到此文件中（本示例假设命名空间为 `ConsoleApp1`）。

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

## <a name="connect-to-the-net-client"></a>连接到 .NET 客户端

若要开始将媒体服务 API 与 .NET 结合使用，需要创建 AzureMediaServicesClient 对象  。 若要创建对象，需要提供客户端所需凭据以使用 Azure AD 连接到 Azure。 在以下代码中，GetCredentialsAsync 函数根据本地配置文件中提供的凭据创建 ServiceClientCredentials 对象。

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

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
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

## <a name="next-steps"></a>后续步骤

- [教程：对视频进行上传、编码和流式处理 - .NET](stream-files-tutorial-with-api.md) 
- [教程：通过媒体服务 v3 进行实时流式传输 - .NET](stream-live-tutorial-with-api.md)
- [教程：使用媒体服务 v3 来分析视频 - .NET](analyze-videos-tutorial-with-api.md)
- [从本地文件创建作业输入 - .NET](job-input-from-local-file-how-to.md)
- [从 HTTPS URL 创建作业输入 - .NET](job-input-from-http-how-to.md)
- [对自定义转换进行编码 - .NET](customize-encoder-presets-how-to.md)
- [使用 AES-128 动态加密和密钥传递服务 - .NET](protect-with-aes128.md)
- [使用 DRM 动态加密和许可证传送服务 - .NET](protect-with-drm.md)
- [从现有策略获取签名密钥 - .NET](get-content-key-policy-dotnet-howto.md)
- [使用媒体服务创建筛选器 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [使用媒体服务 v3 的 Azure Functions v2 的高级视频点播示例](https://aka.ms/ams3functions)

## <a name="see-also"></a>另请参阅

* [.NET 参考](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* 有关更多代码示例，请参阅 [.NET SDK 示例](https://github.com/Azure-Samples/media-services-v3-dotnet)存储库。

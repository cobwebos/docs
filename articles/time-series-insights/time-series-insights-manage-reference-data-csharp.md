---
title: 使用 C# 管理 GA 环境中的参考数据 - Azure 时序见解 | Microsoft Docs
description: 了解如何通过创建以 C# 编写的自定义应用程序来管理 GA 环境的参考数据。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/15/2020
ms.custom: seodec18
ms.openlocfilehash: f0ce0f7d90540274d24a7e0248e6f197b74033a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416982"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-using-c"></a>使用 C# 管理 Azure 时序见解环境的 GA 参考数据

本文演示了如何组合 C#、[MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 和 Azure Active Directory 来向 Azure 时序见解 GA [参考数据管理 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) 发出编程性 API 请求。

> [!TIP]
> 查看 [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample) 上的 GA C# 代码示例。

## <a name="summary"></a>总结

下面的示例代码演示了以下功能：

* 使用 [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) PublicClientApplication 获取访问令牌。
* 针对 GA [参考数据管理 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) 的顺序创建、读取、更新和删除操作。
* 常见的响应代码，包括[常见错误代码](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api#validation-and-error-handling)。
    
    参考数据管理 API 单独处理每个项，一个项出现错误不会阻止其他项成功完成。 例如，如果你的请求包含 100 个项，其中一个项发生错误，则会写入 99 个项，并拒绝一个项。

## <a name="prerequisites-and-setup"></a>先决条件和设置

在编译和运行示例代码之前，请完成以下步骤：

1. [预配 GA Azure 时序见解](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
)环境。

1. 在环境中[创建参考数据集](time-series-insights-add-reference-data-set.md)。 使用以下参考数据方案：

   | 项名 | 类型 |
   | --- | --- |
   | uuid | 字符串 | 

1. 为 Azure Active Directory 配置 Azure 时序见解环境，如[身份验证和授权](time-series-insights-authentication-and-authorization.md)中所述。 使用 `http://localhost:8080/` 作为“重定向 URI”。

1. 安装必需的项目依赖项。

1. 编辑下面的示例代码，将每个 #PLACEHOLDER# 替换为相应的环境标识符。

1. 在项目的根目录中运行 `dotnet run`。 出现提示时，使用你的用户配置文件登录到 Azure。 

## <a name="project-dependencies"></a>项目依赖项

建议使用最新版本的 Visual Studio 和 NETCore.app：

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - 版本 16.4.2+
* [NETCore.app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) - 版本 2.2.8

示例代码包含两个必需的依赖项：

* MSAL.NET [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -  4.7.1 包。
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 12.0.3 包。

使用 [NuGet 2.12+](https://www.nuget.org/) 添加包：

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

或：

1. 声明一个 `csharp-tsi-msal-ga-sample.csproj` 文件：

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```
1. 然后运行 `dotnet restore`。

## <a name="c-sample-code"></a>C# 示例代码

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         * 
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             * 
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;
                
             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {   
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>后续步骤

- 阅读 GA [参考数据管理 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) 参考文档。

---
title: include 文件
description: include 文件
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 4a3f38e1423db0755d8c76f8850e41173d250f43
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
### <a name="create-a-console-application"></a>创建控制台应用程序

如果创建中继时已禁用“需要客户端授权”选项，可使用任何浏览器向混合连接 URL 发送请求。 若要访问受保护的终结点，需在 `ServiceBusAuthorization` 标头中创建并传递令牌，如下所示。

在 Visual Studio 中创建新的**控制台应用 (.NET Framework)** 项目。

### <a name="add-the-relay-nuget-package"></a>添加中继 NuGet 包

1. 右键单击新创建的项目，然后选择“管理 NuGet 包”。
2. 选择“浏览”，然后搜索 **Microsoft.Azure.Relay**。 在搜索结果中，选择“Microsoft Azure 中继”。 
3. 选择“安装”即可完成安装。 关闭对话框。

### <a name="write-code-to-send-requests"></a>编写代码以发送请求

1. 将 Program.cs 文件顶部的现有 `using` 语句替换为以下 `using` 语句：
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. 将常量添加到 `Program` 类，用于保存混合连接的连接详细信息。 将括号中的占位符替换为在创建混合连接时获得的值。 请务必使用完全限定的命名空间名称。
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. 将以下方法添加到 `Program` 类：
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());
    }
    ```
4. 在 `Program` 类的 `Main` 方法中添加以下代码行。
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Program.cs 应如下所示：
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```


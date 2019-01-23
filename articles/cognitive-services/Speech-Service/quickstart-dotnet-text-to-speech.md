---
title: 快速入门：将文本转换为语音，.NET Core - 语音服务
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用文本转语音 REST API 将文本转换为语音。 本指南中包含的示例文本以语音合成标记语言 (SSML) 的形式构造。 这样你就可以选择语音响应的语音和语言。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 207836baaaa30dca24edeab93e9bd1c4a1ac67e6
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359079"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>快速入门：使用 .NET Core 将文本转换为语音

本快速入门介绍如何使用 .NET Core 和文本转语音 REST API 将文本转换为语音。 本指南中包含的示例文本以[语音合成标记语言 (SSML)](speech-synthesis-markup.md) 的形式构造，这样你就可以选择响应的语音和语言。

此快速入门需要包含语音服务资源的 [Azure 认知服务帐户](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 如果没有帐户，可以使用[免费试用版](https://azure.microsoft.com/try/cognitive-services/)获取订阅密钥。

## <a name="prerequisites"></a>先决条件

本快速入门需要：

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download) 或你喜欢用的文本编辑器
* 语音服务的 Azure 订阅密钥

## <a name="create-a-net-core-project"></a>创建 .NET Core 项目

打开新的命令提示符（或终端会话）并运行以下命令：

```console
dotnet new console -o tts-sample
cd tts-sample
```

第一个命令执行两项操作。 它创建新的 .NET 控制台应用程序，并创建名为 `tts-sample` 的目录。 第二个命令转到项目的目录。

## <a name="select-the-c-language-version"></a>选择 C# 语言版本

本快速入门需要 C# 7.1 或更高版本。 可以通过多种方式来更改项目的 C# 版本。 本指南将介绍如何调整 `tts-sample.csproj` 文件。 如需所有可用选项，例如在 Visual Studio 中更改语言的选项，请参阅[选择 C# 语言版本](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)。

打开项目，然后打开 `tts-sample.csproj`。 确保将 `LangVersion` 设置为 7.1 或更高版本。 如果没有用于语言版本的属性组，请添加以下行：

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

确保保存所做的更改。

## <a name="add-required-namespaces-to-your-project"></a>将所需命名空间添加到项目

此前运行的 `dotnet new console` 命令创建了一个项目（包括 `Program.cs`）。 此文件是需放置应用程序代码的位置。 打开 `Program.cs`，替换现有的 using 语句。 这些语句可确保你有权访问生成并运行示例应用所需的所有类型。

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>创建用于令牌交换的类

文本转语音 REST API 需要访问令牌来进行身份验证。 若要获取访问令牌，需要进行交换。 此示例通过 `issueToken` 终结点使用语音服务订阅密钥来交换访问令牌。

此示例假定语音服务订阅位于“美国西部”区域。 如果使用其他区域，请更新 `FetchTokenUri` 的值。 如需完整的列表，请参阅[区域](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)。

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> 有关身份验证的详细信息，请参阅[使用访问令牌进行身份验证](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)。

## <a name="get-an-access-token-and-set-the-host-url"></a>获取访问令牌并设置主机 URL

找到 `static void Main(string[] args)` 并将其替换为 `static async Task Main(string[] args)`。

接下来，将以下代码复制到 main 方法中。 它执行许多操作，但最重要的是，它使用文本作为输入，并调用 `Authentication` 函数，用订阅密钥来交换访问令牌。 如果出现问题，则会将错误输出到控制台。

确保在运行应用之前添加订阅密钥。

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

然后，设置文本转语音的主机和路由：

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>生成 SSML 请求

以 `POST` 请求正文的形式发送文本。 使用 SSML 可以指定语音和语言。 在本快速入门中，我们将使用 SSML，同时将语言设置为 `en-US`，将语音设置为 `ZiraRUS`。 让我们针对请求来构造 SSML：

```csharp
string body = @"<speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> 此示例使用 `ZiraRUS` 语音字体。 如需 Microsoft 提供的语音/语言的完整列表，请参阅[语言支持](language-support.md)。 若要为品牌创建唯一且可识别的语音，请参阅[创建自定义语音字体](how-to-customize-voice-font.md)。

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>实例化客户端，发出请求，然后将合成的音频保存到文件

此代码示例中涉及到许多事项。 让我们快速查看所发生的事项：

* 客户端和请求已实例化。
* HTTP 方法已设置为 `POST`。
* 所需标头已添加到请求中。
* 请求已发送，状态代码已检查。
* 响应以异步方式读取，并已写入到名为 sample.wav 的文件中。

将以下代码复制到项目中。 确保将 `User-Agent` 标头的值替换为 Azure 门户中资源的名称。

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>运行示例应用

上述操作完成后，就可以运行文本转语音应用了。 从命令行（或终端会话）导航到项目目录，然后运行以下命令：

```console
dotnet run
```

如果成功，则会将语音文件保存到项目文件夹中。 使用偏好的媒体播放器播放该文件。

## <a name="clean-up-resources"></a>清理资源

如果已将订阅密钥硬编码到程序中，请确保在完成本快速入门后删除该订阅密钥。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [浏览 GitHub 上的 .NET 示例](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>另请参阅

* [文本到语音 API 参考](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)
* [创建自定义语音字体](how-to-customize-voice-font.md)
* [录制语音样本用于创建自定义语音](record-custom-voice-samples.md)

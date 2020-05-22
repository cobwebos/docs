---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 4dbf9dddd46e38ae9b8cec3288bdacccb1ef1c78
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586832"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>创建 .NET Core 项目

打开新的命令提示符（或终端会话）并运行以下命令：

```console
dotnet new console -o languages-sample
cd languages-sample
```

第一个命令执行两项操作。 它创建新的 .NET 控制台应用程序，并创建名为 `languages-sample` 的目录。 第二个命令是切换至项目目录。

接下来需安装 Json.Net。 在项目的目录中，运行以下命令：

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>将所需命名空间添加到项目

此前运行的 `dotnet new console` 命令创建了一个项目（包括 `Program.cs`）。 此文件是需放置应用程序代码的位置。 打开 `Program.cs`，替换现有的 using 语句。 这些语句可确保你有权访问生成并运行示例应用所需的所有类型。

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-endpoint-information-from-an-environment-variable"></a>从环境变量获取终结点信息

将以下行添加到 `Program` 类。 这些行将从环境变量中读取订阅密钥和终结点，并在遇到任何问题时引发错误。

```csharp
private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
```

## <a name="create-a-function-to-get-a-list-of-languages"></a>创建获取语言列表的函数

在 `Program` 类中，创建一个名为 `GetLanguages` 的函数。 该类封装用于调用 Languages 资源的代码，并将结果输出到控制台。

```csharp
static void GetLanguages()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-route"></a>设置路由

将以下行添加到 `GetLanguages` 函数。

```csharp
string route = "/languages?api-version=3.0";
```

## <a name="instantiate-the-client-and-make-a-request"></a>实例化客户端并发出请求

以下行实例化 `HttpClient` 和 `HttpRequestMessage`：

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>构造请求并输出响应

在 `HttpRequestMessage` 中，需执行以下操作：

* 声明 HTTP 方法
* 构造请求 URI
* 添加必需的标头
* 发出异步请求
* 输出响应

向 `HttpRequestMessage` 添加以下代码：

```csharp
// Set the method to GET
request.Method = HttpMethod.Get;
// Construct the full URI
request.RequestUri = new Uri(endpoint + route);
// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;
// Pretty print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

如果使用的是认知服务多服务订阅，则还必须在请求参数中包括 `Ocp-Apim-Subscription-Region`。 [详细了解如何使用多服务订阅进行身份验证](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

要使用“Pretty Print”（响应格式）打印响应，请将此函数添加到 Program 类：

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="put-it-all-together"></a>将其放在一起

最后一步是在 `Main` 函数中调用 `GetLanguages()`。 找到 `static void Main(string[] args)` 并添加以下行：

```csharp
GetLanguages();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>运行示例应用

上述操作完成后，就可以运行示例应用了。 从命令行（或终端会话）导航到项目目录，然后运行以下命令：

```console
dotnet run
```

## <a name="sample-response"></a>示例响应

请在此[语言列表](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)中查找国家/地区缩写。

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>清理资源

请务必删除示例应用的源代码中的机密信息，例如订阅密钥。

## <a name="next-steps"></a>后续步骤

查看 API 参考，了解使用“翻译”可以执行的所有操作。

> [!div class="nextstepaction"]
> [API 参考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)

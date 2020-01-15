---
title: 快速入门：适用于 Go 的必应自动建议客户端库 | Microsoft Docs
description: 开始使用适用于 Go 的必应自动建议客户端库，并基于部分查询字符串获取搜索建议。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e826d69bbf0f94d02799e9b93b26db8d4cbbba54
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451159"
---
# <a name="quickstart-bing-autosuggest-client-library-for-go"></a>快速入门：适用于 Go 的必应自动建议客户端库

开始使用适用于 Go 的必应自动建议客户端库。 请按照以下步骤安装库并试用基本任务的示例。 

使用适用于 Go 的必应自动建议客户端库，以便基于部分查询字符串获取搜索建议。

[参考文档](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [库源代码](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [示例代码](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>必备条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/)
* 最新版本的 [Go](https://golang.org/dl/)

## <a name="setting-up"></a>设置

### <a name="create-an-azure-resource"></a>创建 Azure 资源 

通过创建 Azure 资源，开始使用必应自动建议客户端库。 选择下面适合你的资源类型：

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

### <a name="create-an-environment-variable"></a>创建环境变量

>[!NOTE]
> 在 2019 年 7 月 1 日之后创建的非试用资源的终结点使用如下所示的自定义子域格式。 有关详细信息和区域终结点的完整列表，请参阅[认知服务的自定义子域名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)。 

从创建的资源使用密钥和终结点，创建两个用于身份验证的环境变量：
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY` - 用于验证请求的资源密钥。
* `AUTOSUGGEST_ENDPOINT` - 用于发送 API 请求的资源终结点。 它将如下所示： 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

使用操作系统的说明。
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

添加环境变量后，请重启控制台窗口。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source ~/.bashrc`，使更改生效。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

编辑 `.bash_profile`，然后添加环境变量：

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

添加环境变量后，请从控制台窗口运行 `source .bash_profile`，使更改生效。
***

### <a name="create-a-new-go-project"></a>创建新的 Go 项目

在控制台窗口（cmd、PowerShell、终端、Bash）中，为 Go 项目创建一个新的工作区并导航到该工作区。 工作区包含三个文件夹： 

* **src** - 此目录包含源代码和包。 使用 `go get` 命令安装的任何包将位于此处。
* **pkg** - 此目录包含编译的 Go 包对象。 这些文件使用 `.a` 扩展名。
* **bin** - 此目录包含运行 `go install` 时创建的二进制可执行文件。

> [!TIP]
> 详细了解 [Go 工作区](https://golang.org/doc/code.html#Workspaces)的结构。 本指南包含有关设置 `$GOPATH` 和 `$GOROOT` 的信息。

让我们创建名为 `my-app` 的工作区，并为 `src`、`pkg` 和 `bin` 创建所需的子目录：

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-client-library-for-go"></a>安装适用于 Go 的客户端库

现在，让我们安装适用于 Go 的客户端库： 

```bash
$ go get -u <library-location-or-url>
```

或者，如果使用 dep，则在存储库中运行：

```bash
$ dep ensure -add <library-location-or-url>
```

### <a name="create-your-go-application"></a>创建 Go 应用程序

接下来，让我们创建名为 `src/sample-app.go` 的文件：

```bash
$ cd src
$ touch sample-app.go
```

打开 `sample-app.go`，添加包名称并导入以下库：

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

创建一个名为 `main` 的函数。 然后，为必应自动建议密钥和终结点创建环境变量。

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>代码示例

这些代码示例演示了如何使用适用于 Go 的必应自动建议客户端库来完成基本任务：

* [对客户端进行身份验证](#authenticate-the-client)
* [发送 API 请求](#send-an-api-request)

## <a name="authenticate-the-client"></a>验证客户端

> [!NOTE] 
> 本快速入门假设你已为必应自动建议密钥创建了名为 `BING_AUTOSUGGEST_SUBSCRIPTION_KEY` 的[环境变量](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，以及一个用于终结点的名为 `BING_AUTOSUGGEST_ENDPOINT` 的环境变量。

在 `main()` 函数中，使用终结点和密钥实例化客户端。 

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

## <a name="send-an-api-request"></a>发送 API 请求

在同一方法中，使用客户端的 [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) 方法将查询发送到必应。 然后，循环访问[建议](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet)响应，并输出第一个建议。

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>运行应用程序

从应用程序目录使用 `go run [arguments]` 命令运行 Go 应用程序。

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [必应自动建议教程](../tutorials/autosuggest.md)

## <a name="see-also"></a>另请参阅

- [什么是必应自动建议？](../get-suggested-search-terms.md)
- [必应自动推荐 API v7 参考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)

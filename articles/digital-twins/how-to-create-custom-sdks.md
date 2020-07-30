---
title: 通过 AutoRest 创建 Azure 数字孪生的自定义 Sdk
titleSuffix: Azure Digital Twins
description: '请参阅如何生成自定义 Sdk，以将 Azure 数字孪生与 c # 以外的语言配合使用。'
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: a93e0b6d29bb10e5e71f48134916cac9cd563fb2
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420033"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>使用 AutoRest 创建 Azure 数字孪生的自定义 Sdk

目前，用于与 Azure 数字孪生 Api 交互的已发布数据平面 SDK 仅适用于 .NET （c #）。 你可以阅读有关 .NET SDK 和 Api 的一般信息，请参阅[*操作方法：使用 Azure 数字孪生 api 和 sdk*](how-to-use-apis-sdks.md)。 如果你使用的是另一种语言，本文将演示如何使用 AutoRest 按你选择的语言生成自己的数据平面 SDK。

>[!NOTE]
> 如果需要，还可以使用 AutoRest 生成控制平面 SDK。 为此，请使用[控制平面 Swagger （OpenAPI）文件](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/preview/2020-03-01-preview)而不是数据平面来完成本文中的步骤。

## <a name="set-up-your-machine"></a>设置计算机

若要生成 SDK，你将需要：
* [AutoRest](https://github.com/Azure/autorest)，版本2.0.4413 （目前不支持版本3）
* 作为 AutoRest 的先决条件[Node.js](https://nodejs.org)
* *digitaltwins.js*的 Azure 数字孪生[数据平面 Swagger （OpenAPI）文件](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins/preview/2020-05-31-preview)及其随附的示例文件夹。 将 Swagger 文件及其示例的文件夹下载到本地计算机。

一旦你的计算机配备了上述列表中的所有内容，你就可以使用 AutoRest 创建该 SDK。

## <a name="create-the-sdk-with-autorest"></a>创建具有 AutoRest 的 SDK 

如果已安装 Node.js，则可以运行以下命令，确保已安装正确版本的 AutoRest：
```cmd/sh
npm install -g autorest@2.0.4413
```

若要在 Azure 数字孪生 Swagger 文件中运行 AutoRest，请执行以下步骤：
1. 将 Azure 数字孪生 Swagger 文件及其随附的示例文件夹复制到工作目录。
2. 使用命令提示符窗口切换到该工作目录。
3. 在以下命令中运行 AutoRest。 将 `<language>` 占位符替换为你选择的语言： `--python` 、 `--java` 、 `--go` 等。 （可在[AUTOREST 自述文件](https://github.com/Azure/autorest)中找到完整的选项列表。）

```cmd/sh
autorest --input-file=adtApiSwagger.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

因此，你将在工作目录中看到一个名为*ADTApi*的新文件夹。 生成的 SDK 文件将具有命名空间*ADTApi*。 您将继续使用该命名空间，这篇文章中的其他用法示例。

AutoRest 支持多种语言代码生成器。

## <a name="add-the-sdk-to-a-visual-studio-project"></a>将 SDK 添加到 Visual Studio 项目

可以将 AutoRest 生成的文件直接包括到 .NET 解决方案中。 不过，您可能需要在多个不同的项目（客户端应用程序、Azure Functions 的应用程序等）中包括 Azure 数字孪生 SDK。 出于此原因，从生成的文件生成一个单独的项目（.NET 类库）可能会很有用。 然后，可以将此类库项目作为项目引用包含到多个解决方案中。

本部分提供有关如何将 SDK 构建为类库的说明，它是其自己的项目，可以包含在其他项目中。 这些步骤依赖于**Visual Studio** （可从[此处](https://visualstudio.microsoft.com/downloads/)安装最新版本）。

下面是相关步骤：

1. 为类库创建新的 Visual Studio 解决方案
2. 使用*ADTApi*作为项目名称
3. 在 "解决方案资源管理器" 中，右键选择生成的解决方案的*ADTApi*项目，然后选择 "*添加 > 现有项 ...* "
4. 找到生成 SDK 的文件夹，然后选择根级别的文件
5. 按 "确定"
6. 将文件夹添加到项目（在解决方案资源管理器中右键选择项目，然后选择 "*添加 >" 新建文件夹*"）
7. 命名文件夹*模型*
8. 右键选择解决方案资源管理器中的 "*模型*" 文件夹，然后选择 "*添加 > 现有项 ...* "
9. 选择生成的 SDK 的 "*模型*" 文件夹中的文件，并按 "确定"

若要成功生成 SDK，你的项目将需要以下参考：
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

若要添加这些工具，请打开 " *> Nuget 包管理器 > 管理解决方案的 NuGet 包*..."。

1. 在面板中，确保选中 "*浏览*" 选项卡
2. 搜索*Microsoft. Rest*
3. 选择 `ClientRuntime` 和 `ClientRuntime.Azure` 包，并将其添加到解决方案

你现在可以生成项目，并将其作为项目引用包含在你编写的任何 Azure 数字孪生应用程序中。

## <a name="general-guidelines-for-generated-sdks"></a>生成的 Sdk 一般准则

本部分包含有关使用生成的 SDK 的常规信息和指南。

### <a name="synchronous-and-asynchronous-calls"></a>同步和异步调用

所有 SDK 函数都提供同步和异步版本。

### <a name="typed-and-untyped-data"></a>类型化和非类型化数据

REST API 调用通常返回强类型对象。 不过，由于 Azure 数字孪生允许用户定义孪生的自定义类型，因此无法为许多 Azure 数字孪生调用预定义静态返回数据。 相反，Api 将返回强类型化包装器类型（如果适用），并且自定义类型的 "输入数据类型" 是在 Json.NET 对象中（在 API 签名中出现数据类型 "对象" 的情况下使用）。 您可以在代码中适当地强制转换这些对象。

### <a name="error-handling"></a>错误处理。

当 SDK 中发生错误（包括 HTTP 错误，如404）时，SDK 将引发异常。 出于此原因，请务必将 try/catch 块内的所有 API 调用封装。

下面是一个代码片段，它尝试添加一个克隆，并在此过程中捕获任何错误：

```csharp
try
{
    await client.DigitalTwins.AddAsync(id, initData);
    Console.WriteLine($"Created a twin successfully: {id}");
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
}
```

### <a name="paging"></a>分页

AutoRest 为 SDK 生成两种类型的分页模式：
* 一个用于除查询 API 之外的所有 Api
* 一个用于查询 API

在非查询分页模式下，每次调用都有两个版本：
* 用于初始调用的版本（例如 `DigitalTwins.ListEdges()` ）
* 用于获取以下页面的版本。 这些调用的后缀为 "Next" （如 `DigitalTwins.ListEdgesNext()` ）

以下代码片段演示了如何从 Azure 数字孪生检索传出关系的分页列表：
```csharp
try
{
    // List to hold the results in
    List<object> relList = new List<object>();
    // Enumerate the IPage object returned to get the results
    // ListAsync will throw if an error occurs
    IPage<object> relPage = await client.DigitalTwins.ListEdgesAsync(id);
    relList.AddRange(relPage);
    // If there are more pages, the NextPageLink in the page is set
    while (relPage.NextPageLink != null)
    {
        // Get more pages...
        relPage = await client.DigitalTwins.ListEdgesNextAsync(relPage.NextPageLink);
        relList.AddRange(relPage);
    }
    Console.WriteLine($"Found {relList.Count} relationships on {id}");
    // Do something with each object found
    // As relationships are custom types, they are JSON.Net types
    foreach (JObject r in relList)
    {
        string relId = r.Value<string>("$edgeId");
        string relName = r.Value<string>("$relationship");
        Console.WriteLine($"Found relationship {relId} from {id}");
    }
}
catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error retrieving relationships on {id}: {e.Response.StatusCode}");
}
```

第二种模式仅为查询 API 生成。 它显式使用 `continuationToken` 。

下面是此模式的示例：

```csharp
string query = "SELECT * FROM digitaltwins";
string conToken = null; // continuation token from the query
int page = 0;
try
{
    // Repeat the query while there are pages
    do
    {
        QuerySpecification spec = new QuerySpecification(query, conToken);
        QueryResult qr = await client.Query.QueryTwinsAsync(spec);
        page++;
        Console.WriteLine($"== Query results page {page}:");
        if (qr.Items != null)
        {
            // Query returns are JObjects
            foreach(JObject o in qr.Items)
            {
                string twinId = o.Value<string>("$dtId");
                Console.WriteLine($"  Found {twinId}");
            }
        }
        Console.WriteLine($"== End query results page {page}");
        conToken = qr.ContinuationToken;
    } while (conToken != null);
} catch (ErrorResponseException e)
{
    Console.WriteLine($"*** Error in twin query: ${e.Response.StatusCode}");
}
```

## <a name="next-steps"></a>后续步骤

逐步完成创建可使用 SDK 的客户端应用的步骤：
* [*教程：为客户端应用编写代码*](tutorial-code.md)
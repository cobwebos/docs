---
title: "Azure Functions Microsoft Graph 绑定 | Microsoft Docs"
description: "了解如何在 Azure Functions 中使用 Microsoft Graph 触发器和绑定。"
services: functions
author: mattchenderson
manager: cfowler
editor: 
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/19/2017
ms.author: mahender
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b0eb0ac63c7dbb9d6cbba093937231e93670529e
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="azure-functions-microsoft-graph-bindings"></a>Azure Functions Microsoft Graph 绑定
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

本文介绍如何在 Azure Functions 中配置和使用 Microsoft Graph 触发器和绑定。
借助它们，你可以使用 Azure Functions 处理 [Microsoft Graph](https://graph.microsoft.io) 中的数据、见解和事件。

Microsoft Graph 扩展提供了以下绑定：
- [身份验证令牌输入绑定](#token-input)，通过它你可以与任意 Microsoft Graph API 进行交互。
- [Excel 表输入绑定](#excel-input)，通过它你可以读取 Excel 中的数据。
- [Excel 表输出绑定](#excel-output)，通过它你可以修改 Excel 数据。
- [OneDrive 文件输入绑定](#onedrive-input)，通过它你可以读取 OneDrive 中的文件。
- [OneDrive 文件输出绑定](#onedrive-output)，通过它你可以写入到 OneDrive 中的文件。
- [Outlook 邮件输出绑定](#outlook-output)，通过它你可以使用 Outlook 发送电子邮件。
- 一系列 [Microsoft Graph webhook 触发器和绑定](#webhooks)，通过它你可以响应 Microsoft Graph 中的事件。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note] 
> Microsoft Graph 绑定目前处于预览状态。

## <a name="setting-up-the-extensions"></a>设置扩展

可通过绑定扩展获取 Microsoft Graph 绑定。 绑定扩展是 Azure Functions 运行时的可选组件。 本部分将演示如何设置 Microsoft Graph 扩展和身份验证令牌扩展。

### <a name="enabling-functions-20-preview"></a>启用 Functions 2.0 预览版

绑定扩展仅对于 Azure Functions 2.0 预览版可用。 若要启用 Functions 2.0，请将 `FUNCTIONS_EXTENSION_VERSION` 应用程序设置设置为“beta”。  若要了解如何配置应用程序设置，请参阅 [Azure Functions 中的应用程序设置](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings)。

### <a name="installing-the-extension"></a>安装扩展

若要从 Azure 门户安装扩展，需要导航到引用此扩展的模板或绑定。 新建一个函数，在模板选择屏幕中选择“Microsoft Graph”方案。 从此方案中选择一个模板。 或者，可以导航到现有函数的的“集成”选项卡，然后选择本主题介绍的绑定之一。

在这两种情况下，均会显示一个警告，它将指定要安装的扩展。 单击“安装”，以获取该扩展。

> [!Note] 
> 每个函数应用只需安装每个扩展一次。 在消耗计划中，门户内安装进程最多需要 10 分钟。

如果使用的是 Visual Studio，可以通过安装以下 NuGet 包获取扩展：
- [Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/)
- [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/)

### <a name="configuring-app-service-authentication--authorization"></a>配置应用服务身份验证/授权

本主题介绍的绑定需要使用一个标识。 这样 Microsoft Graph 就可以强制执行权限并审核交互。 此标识可以是访问你的应用程序的用户或应用程序本身。 若要配置此标识，将需要使用 Azure Active Directory 设置[应用服务身份验证/授权](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview)。 还需要请求你的函数所需的任意资源权限。

> [!Note] 
> Microsoft Graph 扩展仅支持 AAD 身份验证。 用户需要使用工作或学校帐户登录。

如果使用的是 Azure 门户，则在安装扩展提示下方，你将看到一个警告，它会提示你配置应用服务身份验证/授权并请求模板或绑定所需的任意权限。 根据需要单击“立即配置 AAD”或“立即添加权限”。






<a name="token-input"></a>
## <a name="auth-token-input-binding"></a>身份验证令牌输入绑定

该绑定将获取给定资源的 AAD 令牌，并将其作为字符串提供给你的代码。 该资源可以是此应用程序拥有访问权限的任何资源。 

### <a name="configuring-an-auth-token-input-binding"></a>配置身份验证令牌输入绑定

该绑定本身不需要任何 AAD 权限，但是你可能需要请求其他权限，具体取决于使用该令牌的方式。 请查看想要使用此令牌访问的资源的要求。

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在身份验证令牌的函数代码中使用的变量名称。 请参阅[在代码中使用身份验证令牌输入绑定](#token-input-code)。|
|**类型**|必需 - 必须设置为 `token`。|
|**direction**|必需 - 必须设置为 `in`。|
|**identity**|必需 - 用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**resource**|必需 - 正在为其请求令牌的 AAD 资源 URL。|

<a name="token-input-code"></a>
### <a name="using-an-auth-token-input-binding-from-code"></a>在代码中使用身份验证令牌输入绑定

通常会将此令牌作为字符串提供给代码。

#### <a name="sample-getting-user-profile-information"></a>示例：获取用户配置文件信息

假设具有以下 function.json，它定义了一个包含令牌输入绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 C# 示例使用此令牌向 Microsoft Graph 发出 HTTP 调用，并返回结果：

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, TraceWriter log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

以下 JS 示例使用此令牌向 Microsoft Graph 发出 HTTP 调用，并返回结果。 在上述 `function.json` 中，首先将 `$return` 更改为 `res`。

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```





<a name="excel-input"></a>
## <a name="excel-table-input-binding"></a>Excel 表输入绑定

此绑定将读取 OneDrive 中存储的 Excel 表的内容。

### <a name="configuring-an-excel-table-input-binding"></a>配置 Excel 表输入绑定

此绑定需要以下 AAD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|读取用户文件|

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在 Excel 表的函数代码中使用的变量名称。 请参阅[在代码中使用 Excel 表输入绑定](#excel-input-code)。|
|**类型**|必需 - 必须设置为 `excel`。|
|**direction**|必需 - 必须设置为 `in`。|
|**identity**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|必须 - OneDrive 中到 Excel 工作簿的路径。|
|**worksheetName**|表所在的工作表。|
|**tableName**|表的名称。 如果未指定，将使用工作表的内容。|

<a name="excel-input-code"></a>
### <a name="using-an-excel-table-input-binding-from-code"></a>在代码中使用 Excel 表输入绑定

此绑定将向 .NET 函数公开以下类型：
- string[][]
- Microsoft.Graph.WorkbookTable
- 自定义对象类型（使用结构化模型绑定）

#### <a name="sample-reading-an-excel-table"></a>示例：读取 Excel 表

假设具有以下 function.json，它定义了一个包含 Excel 输入绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将读取指定表的内容并将它们返回给用户：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

以下 JS 示例将读取指定表的内容并将它们返回给用户。 在上述 `function.json` 中，首先将 `$return` 更改为 `res`。

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

<a name="excel-output"></a>
## <a name="excel-table-output-binding"></a>Excel 表输出绑定

此绑定将修改 OneDrive 中存储的 Excel 表的内容。

### <a name="configuring-an-excel-table-output-binding"></a>配置 Excel 表输出绑定

此绑定需要以下 AAD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|拥有对用户文件的完全访问权限|

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在身份验证令牌的函数代码中使用的变量名称。 请参阅[在代码中使用 Excel 表输出绑定](#excel-output-code)。|
|**类型**|必需 - 必须设置为 `excel`。|
|**direction**|必需 - 必须设置为 `out`。|
|**identity**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|必须 - OneDrive 中到 Excel 工作簿的路径。|
|**worksheetName**|表所在的工作表。|
|**tableName**|表的名称。 如果未指定，将使用工作表的内容。|
|**updateType**|必需 - 对表进行的更改的类型。 可以是以下值之一：<ul><li><code>update</code> - 替换 OneDrive 中的表的内容。</li><li><code>append</code> - 通过新建行将负载添加到 OneDrive 中的表的末尾。</li></ul>|

<a name="excel-output-code"></a>
### <a name="using-an-excel-table-output-binding-from-code"></a>在代码中使用 Excel 表输出绑定

此绑定将向 .NET 函数公开以下类型：
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- 自定义对象类型（使用结构化模型绑定）

#### <a name="sample-adding-rows-to-an-excel-table"></a>示例：向 Excel 表添加行

假设具有以下 function.json，它定义了一个包含 Excel 输出绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```


以下 C# 示例将根据查询字符串中的输入向表（假设是单列）添加新的行：

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, TraceWriter log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

以下 JS 示例将根据查询字符串中的输入向表（假设是单列）添加新的行。 在上述 `function.json` 中，首先将 `$return` 更改为 `res`。

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```




<a name="onedrive-input"></a>
## <a name="onedrive-file-input-binding"></a>OneDrive 文件输入绑定

此绑定将读取 OneDrive 中存储的文件的内容。

### <a name="configuring-a-onedrive-file-input-binding"></a>配置 OneDrive 文件输入绑定

此绑定需要以下 AAD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|读取用户文件|

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在文件的函数代码中使用的变量名称。 请参阅[在代码中使用 OneDrive 文件输入绑定](#onedrive-input-code)。|
|**类型**|必需 - 必须设置为 `onedrive`。|
|**direction**|必需 - 必须设置为 `in`。|
|**identity**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|必须 - OneDrive 中到文件的路径。|

<a name="onedrive-input-code"></a>
### <a name="using-a-onedrive-file-input-binding-from-code"></a>在代码中使用 OneDrive 文件输入绑定

此绑定将向 .NET 函数公开以下类型：
- byte[]
- Stream
- 字符串
- Microsoft.Graph.DriveItem

#### <a name="sample-reading-a-file-from-onedrive"></a>示例：读取 OneDrive 中的文件

假设具有以下 function.json，它定义了一个包含 OneDrive 输入绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将读取查询字符串中指定的文件并记录它的长度：

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

以下 JS 示例将读取查询字符串中指定的文件并返回它的长度。 在上述 `function.json` 中，首先将 `$return` 更改为 `res`。

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```


<a name="onedrive-output"></a>
## <a name="onedrive-file-output-binding"></a>OneDrive 文件输出绑定

此绑定将修改 OneDrive 中存储的文件的内容。

### <a name="configuring-a-onedrive-file-output-binding"></a>配置 OneDrive 文件输出绑定

此绑定需要以下 AAD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|拥有对用户文件的完全访问权限|

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在文件的函数代码中使用的变量名称。 请参阅[在代码中使用 OneDrive 文件输出绑定](#onedrive-output-code)。|
|**类型**|必需 - 必须设置为 `onedrive`。|
|**direction**|必需 - 必须设置为 `out`。|
|**identity**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|必须 - OneDrive 中到文件的路径。|

<a name="onedrive-output-code"></a>
### <a name="using-a-onedrive-file-output-binding-from-code"></a>在代码中使用 OneDrive 文件输出绑定

此绑定将向 .NET 函数公开以下类型：
- byte[]
- Stream
- 字符串
- Microsoft.Graph.DriveItem

#### <a name="sample-writing-to-a-file-in-onedrive"></a>示例：写入到 OneDrive 中的文件

假设具有以下 function.json，它定义了一个包含 OneDrive 输出绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将从查询字符串获取文本并将其写入文本文件（上述配置中定义的 FunctionsTest.txt，位于调用者的 OneDrive 的根目录）：

```csharp
using System.Net;
using System.Text;

public static async Task Run(HttpRequest req, TraceWriter log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    return;
}
```
以下 JS 示例将从查询字符串获取文本并将其写入文本文件（上述配置中定义的 FunctionsTest.txt，位于调用者的 OneDrive 的根目录）。 在上述 `function.json` 中，首先将 `$return` 更改为 `res`。

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```



<a name="outlook-output"></a>
## <a name="outlook-message-output-binding"></a>Outlook 邮件输出绑定

通过 Outlook 发送邮件。

### <a name="configuring-an-outlook-message-output-binding"></a>配置 Outlook 邮件输出绑定

此绑定需要以下 AAD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|以用户身份发送邮件|

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**|必需 - 必须设置为 `outlook`。|
|**direction**|必需 - 必须设置为 `out`。|
|**identity**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |

<a name="outlook-output-code"></a>
### <a name="using-an-outlook-message-output-binding-from-code"></a>在代码中使用 Outlook 邮件输出绑定

此绑定将向 .NET 函数公开以下类型：
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- 字符串
- 自定义对象类型（使用结构化模型绑定）

#### <a name="sample-sending-an-email-through-outlook"></a>示例：通过 Outlook 发送电子邮件

假设具有以下 function.json，它定义了一个包含 Outlook 邮件输出绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

以下 C# 示例会将邮件从调用者发送到查询字符串中指定的收件人：

```csharp
using System.Net;

public static void Run(HttpRequest req, out Message message, TraceWriter log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

以下 JS 示例会将邮件从调用者发送到查询字符串中指定的收件人：

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```





<a name="webhooks"></a>
## <a name="microsoft-graph-webhook-bindings"></a>Microsoft Graph webhook 绑定

通过 Webhook，你可以响应 Microsoft Graph 中的事件。 若要支持 webhook，需要使用函数创建、刷新和响应 webhook 订阅。 完整的 webhook 解决方案需要组合以下绑定：
- [Microsoft Graph webhook 触发器](#webhook-trigger)，使你可以响应传入的 webhook。
- [Microsoft Graph webhook 订阅输入绑定](#webhook-input)，使你可以列出现有的订阅并选择性地更新这些订阅。
- [Microsoft Graph webhook 订阅输出绑定](#webhook-output)，使你可以创建或删除 webhook 订阅。

这些绑定本身不需要任何 AAD 权限，但是你需要请求你想要响应的资源类型的相关权限。 有关每种资源类型所需的权限列表，请参阅[订阅权限](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions)。

有关 webhook 的详细信息，请参阅[使用 Microsoft Graph 中的 webhook]。





<a name="webhook-trigger"></a>
### <a name="microsoft-graph-webhook-trigger"></a>Microsoft Graph webhook 触发器

此触发器使函数可以响应从 Microsoft Graph 传入的 webhook。 此触发器的每个实例均可响应一个 Microsoft Graph 资源类型。

#### <a name="configuring-a-microsoft-graph-webhook-trigger"></a>配置 Microsoft Graph webhook 触发器

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**|必需 - 必须设置为 `graphWebhook`。|
|**direction**|必需 - 必须设置为 `trigger`。|
|**resourceType**|必需 - 此函数应为其响应 webhook 的图表资源。 可以是以下值之一：<ul><li><code>#Microsoft.Graph.Message</code> - 对 Outlook 邮件进行的更改。</li><li><code>#Microsoft.Graph.DriveItem</code> - 对 OneDrive 根项目进行的更改。</li><li><code>#Microsoft.Graph.Contact - changes made to personal contacts in Outlook.</code></li><li><code>#Microsoft.Graph.Event - changes made to Outlook calendar items.</code></li></ul>|

> [!Note]
> 一个函数应用只能拥有一个针对给定 `resourceType` 值注册的函数。

#### <a name="using-a-microsoft-graph-webhook-trigger-from-code"></a>在代码中使用 Microsoft Graph webhook 触发器

此绑定将向 .NET 函数公开以下类型：
- 与资源类型相关的 Microsoft Graph SDK 类型，例如 Microsoft.Graph.Message、Microsoft.Graph.DriveItem
- 自定义对象类型（使用结构化模型绑定）

有关在代码中使用此绑定的示例，请参阅 [Microsoft Graph webhook 示例](#webhook-samples)。



<a name="webhook-input"></a>
### <a name="microsoft-graph-webhook-subscription-input-binding"></a>Microsoft Graph webhook 订阅输入绑定

此绑定使你可以检索此函数应用管理的订阅列表。 此绑定将读取函数应用存储中的信息，并且不会反映在此应用外部创建的其他订阅。

#### <a name="configuring-a-microsoft-graph-webhook-subscription-input-binding"></a>配置 Microsoft Graph webhook 订阅输入绑定

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**|必需 - 必须设置为 `graphWebhookSubscription`。|
|**direction**|必需 - 必须设置为 `in`。|
|**filter**| 如果设置为 `userFromRequest`，则此绑定将只检索调用者所拥有的订阅（仅对 [HTTP 触发器有效]）。| 

#### <a name="using-a-microsoft-graph-webhook-subscription-input-binding-from-code"></a>在代码中使用 Microsoft Graph webhook 订阅输入绑定

此绑定将向 .NET 函数公开以下类型：
- string[]
- 自定义对象类型数组
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]

有关在代码中使用此绑定的示例，请参阅 [Microsoft Graph webhook 示例](#webhook-samples)。


<a name="webhook-output"></a>
### <a name="microsoft-graph-webhook-subscription-output-binding"></a>Microsoft Graph webhook 订阅输出绑定

此绑定使你可以在 Microsoft Graph 中创建、删除和刷新 webhook 订阅。

#### <a name="configuring-a-microsoft-graph-webhook-subscription-output-binding"></a>配置 Microsoft Graph webhook 订阅输出绑定

该绑定支持以下属性：

|属性|说明|
|--------|--------|
|**name**|必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**|必需 - 必须设置为 `graphWebhookSubscription`。|
|**direction**|必需 - 必须设置为 `out`。|
|**identity**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**action**|必需 - 指定绑定应执行的操作。 可以是以下值之一：<ul><li><code>create</code> - 注册新订阅。</li><li><code>delete</code> - 删除指定订阅。</li><li><code>refresh</code> - 刷新指定订阅，避免它过期。</li></ul>|
|**subscriptionResource**|仅在将 _action_ 设置为 `create` 时为必需。 指定 Microsoft Graph 资源，以监视其更改。 请参阅[使用 Microsoft Graph 中的 webhook]。 |
|**changeType**|仅在将 _action_ 设置为 `create` 时为必需。 指示订阅资源中将触发通知的更改类型。 支持的值为：`created`、`updated`、`deleted`。 可以使用逗号分隔的列表组合多个值。|

#### <a name="using-a-microsoft-graph-webhook-subscription-output-binding-from-code"></a>在代码中使用 Microsoft Graph webhook 输出绑定

此绑定将向 .NET 函数公开以下类型：
- 字符串
- Microsoft.Graph.Subscription

有关在代码中使用此绑定的示例，请参阅 [Microsoft Graph webhook 示例](#webhook-samples)。
 
<a name="webhook-samples"></a>
### <a name="microsoft-graph-webhook-samples"></a>Microsoft Graph webhook 示例

#### <a name="sample-creating-a-subscription"></a>示例：创建订阅

假设具有以下 function.json，它使用创建操作定义了一个包含订阅输出绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphwebhook",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "listen": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将注册一个 webhook，当调用者收到 Outlook 邮件时它将通知此函数应用：

```csharp
using System;
using System.Net;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, TraceWriter log)
{
  log.Info("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

以下 JS 示例将注册一个 webhook，当调用者收到 Outlook 邮件时它将通知此函数应用：

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

#### <a name="sample-handling-notifications"></a>示例：处理通知

假设具有以下 function.json，它将 Graph webhook 触发器定义为处理 Outlook 邮件：

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将响应传入邮件并记录收件人发送的主题中包含“Azure Functions”的邮件的正文：

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;

public static async Task Run(Message msg, TraceWriter log)  
{
    log.Info("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.Info($"Processed email: {msg.BodyPreview}");
    }
}
```

以下 JS 示例将响应传入邮件并记录收件人发送的主题中包含“Azure Functions”的邮件的正文：

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

#### <a name="sample-deleting-subscriptions"></a>示例：删除订阅

假设具有以下 function.json，它使用删除操作定义了一个包含订阅输入绑定和订阅输出绑定的 HTTP 触发器：

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将获取调用者的所有订阅并将其删除：

```csharp
using System.Net;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.Info($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

以下 JS 示例将获取调用者的所有订阅并将其删除：

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

#### <a name="sample-refreshing-subscriptions"></a>示例：刷新订阅

有两种刷新订阅的方法：
- 使用应用程序标识处理所有订阅。 此方法需要 Azure Active Directory 管理员的许可。Azure Functions 支持的所有语言均可使用此方法。
- 通过手动绑定各个用户 ID 来使用与各个订阅关联的标识。 此方法需要一些自定义代码来执行绑定。 仅 .NET functions 可以使用此方法。

这两个选项在下面说明。

**使用应用程序标识**

假设具有以下 function.json，它使用应用程序标识定义了一个包含订阅输入绑定和订阅输出绑定的计时器触发器：

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将使用应用程序标识定时刷新订阅：

```csharp
using System;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, TraceWriter log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.Info($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

以下 JS 示例将使用应用程序标识定时刷新订阅：

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

**使用动态用户标识**

对于备用选项，假设具有以下 function.json，它定义了一个计时器触发器，将订阅输入绑定延迟绑定到函数代码：

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

以下 C# 示例将通过在代码中创建输出绑定来使用各个用户的标识，进而定时刷新订阅：
```csharp
using System;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, TraceWriter log)
{
  log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.Info($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```



[HTTP 触发器有效]: functions-bindings-http-webhook.md
[使用 Microsoft Graph 中的 webhook]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks


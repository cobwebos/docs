---
title: Azure Functions 的 Microsoft Graph 绑定
description: 了解如何在 Azure Functions 中使用 Microsoft Graph 触发器和绑定。
services: functions
author: mattchenderson
manager: cfowler
editor: ''
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 12/20/2017
ms.author: mahender
ms.openlocfilehash: d774f0ca644793235a8c423b052b559d26e289c4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Azure Functions 的 Microsoft Graph 绑定

本文介绍如何在 Azure Functions 中配置和使用 Microsoft Graph 触发器和绑定。 借助它们，你可以使用 Azure Functions 处理 [Microsoft Graph](https://graph.microsoft.io) 中的数据、见解和事件。

Microsoft Graph 扩展提供了以下绑定：
- [身份验证令牌输入绑定](#token-input)，通过它你可以与任意 Microsoft Graph API 进行交互。
- [Excel 表输入绑定](#excel-input)，通过它你可以读取 Excel 中的数据。
- [Excel 表输出绑定](#excel-output)，通过它你可以修改 Excel 数据。
- [OneDrive 文件输入绑定](#onedrive-input)，用于读取 OneDrive 中的文件。
- [OneDrive 文件输出绑定](#onedrive-output)，用于写入到 OneDrive 中的文件。
- [Outlook 邮件输出绑定](#outlook-output)，通过它你可以使用 Outlook 发送电子邮件。
- 一系列 [Microsoft Graph webhook 触发器和绑定](#webhooks)，通过它你可以响应 Microsoft Graph 中的事件。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Azure Functions 2.x 版的 Microsoft Graph 绑定目前处于预览状态。 Functions 1.x 版不支持这些绑定。

## <a name="packages"></a>包

[Microsoft.Azure.WebJobs.Extensions.AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) NuGet 包中提供了身份验证令牌输入绑定。 [Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) 包中提供了其他 Microsoft Graph 绑定。 [azure-functions-microsoftgraph-extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub 存储库中提供了这些包的源代码。

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="setting-up-the-extensions"></a>设置扩展

可通过绑定扩展获取 Microsoft Graph 绑定。 绑定扩展是 Azure Functions 运行时的可选组件。 本部分介绍如何设置 Microsoft Graph 扩展和身份验证令牌扩展。

### <a name="enabling-functions-20-preview"></a>启用 Functions 2.0 预览版

绑定扩展仅适用于 Azure Functions 2.0 预览版。 

有关如何将函数应用设置为使用 Functions 运行时预览版 2.0 的信息，请参阅[如何面向 Azure Functions 运行时版本](set-runtime-version.md)。

### <a name="installing-the-extension"></a>安装扩展

若要从 Azure 门户安装扩展，请导航到引用此扩展的模板或绑定。 新建一个函数，在模板选择屏幕中选择“Microsoft Graph”方案。 从此方案中选择一个模板。 或者，可以导航到现有函数的的“集成”选项卡，然后选择本文介绍的绑定之一。

在这两种情况下，均会显示一个警告，它将指定要安装的扩展。 单击“安装”，以获取该扩展。 每个函数应用只需安装每个扩展一次。 

> [!Note] 
> 在消耗计划中，门户内安装进程最多需要 10 分钟。

如果使用的是 Visual Studio，可以通过安装[本文中前面列出的 NuGet 包](#packages)来获取扩展。

### <a name="configuring-authentication--authorization"></a>配置身份验证/授权

本文介绍的绑定需要使用一个标识。 这样 Microsoft Graph 就可以强制执行权限并审核交互。 此标识可以是访问你的应用程序的用户或应用程序本身。 若要配置此标识，请使用 Azure Active Directory 设置[应用服务身份验证/授权](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview)。 还需要请求你的函数所需的任意资源权限。

> [!Note] 
> Microsoft Graph 扩展仅支持 Azure AD 身份验证。 用户需要使用工作或学校帐户登录。

如果使用的是 Azure 门户，则安装扩展的提示下面会显示一条警告。 该警告提示配置应用服务身份验证/授权并请求模板或绑定所需的任意权限。 根据需要单击“立即配置 Azure AD”或“立即添加权限”。



<a name="token-input"></a>
## <a name="auth-token"></a>身份验证令牌

身份验证令牌输入绑定将获取给定资源的 Azure AD 令牌，并将其作为字符串提供给你的代码。 该资源可以是此应用程序拥有访问权限的任何资源。 

本部分包含以下小节：

* [示例](#auth-token---example)
* [属性](#auth-token---attributes)
* [配置](#auth-token---configuration)
* [使用情况](#auth-token---usage)

### <a name="auth-token---example"></a>身份验证令牌 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>身份验证令牌 - C# 脚本示例

以下示例获取用户配置文件信息。

*function.json* 文件定义一个包含令牌输入绑定的 HTTP 触发器：

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

该 C# 脚本代码使用此令牌向 Microsoft Graph 发出 HTTP 调用，并返回结果：

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

#### <a name="auth-token---javascript-example"></a>身份验证令牌 - JavaScript 示例

以下示例获取用户配置文件信息。

*function.json* 文件定义一个包含令牌输入绑定的 HTTP 触发器：

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

该 JavaScript 代码使用此令牌向 Microsoft Graph 发出 HTTP 调用，并返回结果：

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

### <a name="auth-token---attributes"></a>身份验证令牌 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [Token](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) 特性。

### <a name="auth-token---configuration"></a>身份验证令牌 - 配置

下表解释了在 *function.json* 文件和 `Token` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在身份验证令牌的函数代码中使用的变量名称。 请参阅[在代码中使用身份验证令牌输入绑定](#token-input-code)。|
|**类型**||必需 - 必须设置为 `token`。|
|**direction**||必需 - 必须设置为 `in`。|
|**identity**|**标识**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId**|**UserId**  |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|**UserToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**资源**|**resource**|必需 - 正在为其请求令牌的 Azure AD 资源 URL。|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>身份验证令牌 - 用法

该绑定本身不需要任何 Azure AD 权限，但是你可能需要请求其他权限，具体取决于使用该令牌的方式。 请查看想要使用此令牌访问的资源的要求。

通常会将此令牌作为字符串提供给代码。




<a name="excel-input"></a>
## <a name="excel-input"></a>Excel 输入

Excel 表输入绑定将读取 OneDrive 中存储的 Excel 表的内容。

本部分包含以下小节：

* [示例](#excel-input---example)
* [属性](#excel-input---attributes)
* [配置](#excel-input---configuration)
* [使用情况](#excel-input---usage)

### <a name="excel-input---example"></a>Excel 输入 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Excel 输入 - C# 脚本示例

以下 *function.json* 文件定义一个包含 Excel 输入绑定的 HTTP 触发器：

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

以下 C# 脚本代码读取指定表的内容并将它们返回给用户：

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives; 

public static IActionResult Run(HttpRequest req, string[][] excelTableData, TraceWriter log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Excel 输入 - JavaScript 示例

以下 *function.json* 文件定义一个包含 Excel 输入绑定的 HTTP 触发器：

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 JavaScript 代码读取指定表的内容并将它们返回给用户：

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Excel 输入 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) 特性。

### <a name="excel-input---configuration"></a>Excel 输入 - 配置

下表解释了在 *function.json* 文件和 `Excel` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在 Excel 表的函数代码中使用的变量名称。 请参阅[在代码中使用 Excel 表输入绑定](#excel-input-code)。|
|**类型**||必需 - 必须设置为 `excel`。|
|**direction**||必需 - 必须设置为 `in`。|
|**identity**|**标识**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId**|**UserId**  |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|**UserToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|**路径**|必须 - OneDrive 中到 Excel 工作簿的路径。|
|**worksheetName**|**WorksheetName**|表所在的工作表。|
|**tableName**|**TableName**|表的名称。 如果未指定，将使用工作表的内容。|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Excel 输入 - 用法

此绑定需要以下 Azure AD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|读取用户文件|

此绑定将向 .NET 函数公开以下类型：
- string[][]
- Microsoft.Graph.WorkbookTable
- 自定义对象类型（使用结构化模型绑定）










<a name="excel-output"></a>
## <a name="excel-output"></a>Excel 输出

Excel 输出绑定修改 OneDrive 中存储的 Excel 表的内容。

本部分包含以下小节：

* [示例](#excel-output---example)
* [属性](#excel-output---attributes)
* [配置](#excel-output---configuration)
* [使用情况](#excel-output---usage)

### <a name="excel-output---example"></a>Excel 输出 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Excel 输出 - C# 脚本示例

以下示例将行添加到 Excel 表。

*function.json* 文件定义一个包含 Excel 输出绑定的 HTTP 触发器：

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

该 C# 脚本代码根据查询字符串中的输入向表（假设是单列）添加新的行：

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

#### <a name="excel-output---javascript-example"></a>Excel 输出 - JavaScript 示例

以下示例将行添加到 Excel 表。

*function.json* 文件定义一个包含 Excel 输出绑定的 HTTP 触发器：

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 JavaScript 代码根据查询字符串中的输入向表（假设是单列）添加新的行：

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Excel 输出 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) 特性。

### <a name="excel-output---configuration"></a>Excel 输出 - 配置

下表解释了在 *function.json* 文件和 `Excel` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在身份验证令牌的函数代码中使用的变量名称。 请参阅[在代码中使用 Excel 表输出绑定](#excel-output-code)。|
|**类型**||必需 - 必须设置为 `excel`。|
|**direction**||必需 - 必须设置为 `out`。|
|**identity**|**标识**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**UserId** |**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|**UserToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|**路径**|必须 - OneDrive 中到 Excel 工作簿的路径。|
|**worksheetName**|**WorksheetName**|表所在的工作表。|
|**tableName**|**TableName**|表的名称。 如果未指定，将使用工作表的内容。|
|**updateType**|**UpdateType**|必需 - 对表进行的更改的类型。 可以是以下值之一：<ul><li><code>update</code> - 替换 OneDrive 中的表的内容。</li><li><code>append</code> - 通过新建行将负载添加到 OneDrive 中的表的末尾。</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Excel 输出 - 用法

此绑定需要以下 Azure AD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|拥有对用户文件的完全访问权限|

此绑定将向 .NET 函数公开以下类型：
- string[][]
- Newtonsoft.Json.Linq.JObject
- Microsoft.Graph.WorkbookTable
- 自定义对象类型（使用结构化模型绑定）





<a name="onedrive-input"></a>
## <a name="file-input"></a>文件输入

OneDrive 文件输入绑定读取 OneDrive 中存储的文件的内容。

本部分包含以下小节：

* [示例](#file-input---example)
* [属性](#file-input---attributes)
* [配置](#file-input---configuration)
* [使用情况](#file-input---usage)

### <a name="file-input---example"></a>文件输入 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>文件输入 - C# 脚本示例

以下示例读取 OneDrive 中存储的文件。

*function.json* 文件定义一个包含 OneDrive 文件输入绑定的 HTTP 触发器：

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

该 C# 脚本代码读取查询字符串中指定的文件并记录它的长度：

```csharp
using System.Net;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, TraceWriter log)
{
    log.Info(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>文件输入 - JavaScript 示例

以下示例读取 OneDrive 中存储的文件。

*function.json* 文件定义一个包含 OneDrive 文件输入绑定的 HTTP 触发器：

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

以下 JavaScript 代码读取查询字符串中指定的文件并返回它的长度。

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>文件输入 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) 特性。

### <a name="file-input---configuration"></a>文件输入 - 配置

下表解释了在 *function.json* 文件和 `OneDrive` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在文件的函数代码中使用的变量名称。 请参阅[在代码中使用 OneDrive 文件输入绑定](#onedrive-input-code)。|
|**类型**||必需 - 必须设置为 `onedrive`。|
|**direction**||必需 - 必须设置为 `in`。|
|**identity**|**标识**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId**|**UserId**  |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|**UserToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|**路径**|必须 - OneDrive 中到文件的路径。|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>文件输入 - 用法

此绑定需要以下 Azure AD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|读取用户文件|

此绑定将向 .NET 函数公开以下类型：
- byte[]
- Stream
- 字符串
- Microsoft.Graph.DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>文件输出

OneDrive 文件输出绑定修改 OneDrive 中存储的文件的内容。

本部分包含以下小节：

* [示例](#file-output---example)
* [属性](#file-output---attributes)
* [配置](#file-output---configuration)
* [使用情况](#file-output---usage)

### <a name="file-output---example"></a>文件输出 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>文件输出 - C# 脚本示例

以下示例写入 OneDrive 中存储的文件。

*function.json* 文件定义一个包含 OneDrive 输出绑定的 HTTP 触发器：

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

该 C# 脚本代码从查询字符串获取文本并将其写入文本文件（上述示例中定义的 FunctionsTest.txt，位于调用者的 OneDrive 的根目录）：

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

#### <a name="file-output---javascript-example"></a>文件输出 - JavaScript 示例

以下示例写入 OneDrive 中存储的文件。

*function.json* 文件定义一个包含 OneDrive 输出绑定的 HTTP 触发器：

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
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

该 JavaScript 代码从查询字符串获取文本并将其写入文本文件（上述配置中定义的 FunctionsTest.txt，位于调用者的 OneDrive 的根目录）。

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>文件输出 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) 特性。

### <a name="file-output---configuration"></a>文件输出 - 配置

下表解释了在 *function.json* 文件和 `OneDrive` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在文件的函数代码中使用的变量名称。 请参阅[在代码中使用 OneDrive 文件输出绑定](#onedrive-output-code)。|
|**类型**||必需 - 必须设置为 `onedrive`。|
|**direction**||必需 - 必须设置为 `out`。|
|**identity**|**标识**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**UserId** |**userId** |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|**UserToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**路径**|**路径**|必须 - OneDrive 中到文件的路径。|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>文件输出 - 用法

此绑定需要以下 Azure AD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|拥有对用户文件的完全访问权限|

此绑定将向 .NET 函数公开以下类型：
- byte[]
- Stream
- 字符串
- Microsoft.Graph.DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Outlook 输出

Outlook 消息输出绑定通过 Outlook 发送电子邮件。

本部分包含以下小节：

* [示例](#outlook-output---example)
* [属性](#outlook-output---attributes)
* [配置](#outlook-output---configuration)
* [使用情况](#outlook-outnput---usage)

### <a name="outlook-output---example"></a>Outlook 输出 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Outlook 输出 - C# 脚本示例

以下示例通过 Outlook 发送电子邮件。

*function.json* 文件定义一个包含 Outlook 消息输出绑定的 HTTP 触发器：

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

该 C# 脚本代码将邮件从调用者发送到查询字符串中指定的收件人：

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

#### <a name="outlook-output---javascript-example"></a>Outlook 输出 - JavaScript 示例

以下示例通过 Outlook 发送电子邮件。

*function.json* 文件定义一个包含 Outlook 消息输出绑定的 HTTP 触发器：

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

该 JavaScript 代码将邮件从调用者发送到查询字符串中指定的收件人：

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

### <a name="outlook-output---attributes"></a>Outlook 输出 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) 特性。

### <a name="outlook-output---configuration"></a>Outlook 输出 - 配置

下表解释了在 *function.json* 文件和 `Outlook` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**||必需 - 必须设置为 `outlook`。|
|**direction**||必需 - 必须设置为 `out`。|
|**identity**|**标识**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId**|**UserId**  |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|**UserToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Outlook 输出 - 用法

此绑定需要以下 Azure AD 权限：
|资源|权限|
|--------|--------|
|Microsoft Graph|以用户身份发送邮件|

此绑定将向 .NET 函数公开以下类型：
- Microsoft.Graph.Message
- Newtonsoft.Json.Linq.JObject
- 字符串
- 自定义对象类型（使用结构化模型绑定）






## <a name="webhooks"></a>Webhook

通过 Webhook，你可以响应 Microsoft Graph 中的事件。 若要支持 webhook，需要使用函数创建、刷新和响应 webhook 订阅。 完整的 webhook 解决方案需要组合以下绑定：
- [Microsoft Graph webhook 触发器](#webhook-trigger)，使你可以响应传入的 webhook。
- [Microsoft Graph webhook 订阅输入绑定](#webhook-input)，使你可以列出现有的订阅并选择性地更新这些订阅。
- [Microsoft Graph webhook 订阅输出绑定](#webhook-output)，使你可以创建或删除 webhook 订阅。

这些绑定本身不需要任何 Azure AD 权限，但是你需要请求你想要响应的资源类型的相关权限。 有关每种资源类型所需的权限列表，请参阅[订阅权限](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/subscription_post_subscriptions#permissions)。

有关 webhook 的详细信息，请参阅[使用 Microsoft Graph 中的 webhook]。





## <a name="webhook-trigger"></a>Webhook 触发器

Microsoft Graph webhook 触发器使函数可以响应从 Microsoft Graph 传入的 webhook。 此触发器的每个实例均可响应一个 Microsoft Graph 资源类型。

本部分包含以下小节：

* [示例](#webhook-trigger---example)
* [属性](#webhook-trigger---attributes)
* [配置](#webhook-trigger---configuration)
* [使用情况](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Webhook 触发器 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Webhook 触发器 - C# 脚本示例

以下示例处理传入 Outlook 消息的 webhook。 若要使用 webhook 触发器，可以[创建订阅](#webhook-output---example)，并[刷新订阅](#webhook-subscription-refresh)以防止过期。

*function.json* 文件定义 webhook 触发器：

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

该 C# 脚本代码将响应传入邮件并记录收件人发送的主题中包含“Azure Functions”的邮件的正文：

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

#### <a name="webhook-trigger---javascript-example"></a>Webhook 触发器 - JavaScript 示例

以下示例处理传入 Outlook 消息的 webhook。 若要使用 webhook 触发器，可以[创建订阅](#webhook-output---example)，并[刷新订阅](#webhook-subscription-refresh)以防止过期。

*function.json* 文件定义 webhook 触发器：

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

该 JavaScript 代码将响应传入邮件并记录收件人发送的主题中包含“Azure Functions”的邮件的正文：

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

### <a name="webhook-trigger---attributes"></a>Webhook 触发器 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [GraphWebHookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookTriggerAttribute.cs) 特性。

### <a name="webhook-trigger---configuration"></a>Webhook 触发器 - 配置

下表解释了在 *function.json* 文件和 `GraphWebHookTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**||必需 - 必须设置为 `graphWebhook`。|
|**direction**||必需 - 必须设置为 `trigger`。|
|**resourceType**|**ResourceType**|必需 - 此函数应为其响应 webhook 的图表资源。 可以是以下值之一：<ul><li><code>#Microsoft.Graph.Message</code> - 对 Outlook 邮件进行的更改。</li><li><code>#Microsoft.Graph.DriveItem</code> - 对 OneDrive 根项目进行的更改。</li><li><code>#Microsoft.Graph.Contact</code> - 对 Outlook 中的联系人所做的更改。</li><li><code>#Microsoft.Graph.Event</code> - 对 Outlook 日历项所做的更改。</li></ul>|

> [!Note]
> 一个函数应用只能拥有一个针对给定 `resourceType` 值注册的函数。

### <a name="webhook-trigger---usage"></a>Webhook 触发器 - 用法

此绑定将向 .NET 函数公开以下类型：
- 与资源类型相关的 Microsoft Graph SDK 类型，例如 `Microsoft.Graph.Message` 或 `Microsoft.Graph.DriveItem`。
- 自定义对象类型（使用结构化模型绑定）




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Webhook 输入

Microsoft Graph webhook 输入绑定使你可以检索此函数应用管理的订阅列表。 此绑定将读取函数应用存储中的信息，并且不会反映在此应用外部创建的其他订阅。

本部分包含以下小节：

* [示例](#webhook-input---example)
* [属性](#webhook-input---attributes)
* [配置](#webhook-input---configuration)
* [使用情况](#webhook-input---usage)

### <a name="webhook-input---example"></a>Webhook 输入 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Webhook 输入 - C# 脚本示例

以下示例获取调用者的所有订阅并将其删除。

*function.json* 文件使用删除操作定义了一个包含订阅输入绑定和订阅输出绑定的 HTTP 触发器：

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

该 C# 脚本代码获取订阅并将其删除：

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

#### <a name="webhook-input---javascript-example"></a>Webhook 输入 - JavaScript 示例

以下示例获取调用者的所有订阅并将其删除。

*function.json* 文件使用删除操作定义了一个包含订阅输入绑定和订阅输出绑定的 HTTP 触发器：

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

该 JavaScript 代码获取订阅并将其删除：

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

### <a name="webhook-input---attributes"></a>Webhook 输入 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) 特性。

### <a name="webhook-input---configuration"></a>Webhook 输入 - 配置

下表解释了在 *function.json* 文件和 `GraphWebHookSubscription` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**||必需 - 必须设置为 `graphWebhookSubscription`。|
|**direction**||必需 - 必须设置为 `in`。|
|**filter**|**筛选器**| 如果设置为 `userFromRequest`，则此绑定将只检索调用者所拥有的订阅（仅对 [HTTP 触发器有效]）。| 

### <a name="webhook-input---usage"></a>Webhook 输入 - 用法

此绑定将向 .NET 函数公开以下类型：
- string[]
- 自定义对象类型数组
- Newtonsoft.Json.Linq.JObject[]
- Microsoft.Graph.Subscription[]





## <a name="webhook-output"></a>Webhook 输出

webhook 订阅输出绑定使你可以在 Microsoft Graph 中创建、删除和刷新 webhook 订阅。

本部分包含以下小节：

* [示例](#webhook-output---example)
* [属性](#webhook-output---attributes)
* [配置](#webhook-output---configuration)
* [使用情况](#webhook-output---usage)

### <a name="webhook-output---example"></a>Webhook 输出 - 示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Webhook 输出 - C# 脚本示例

以下示例创建订阅。 可以[刷新订阅](#webhook-subscription-refresh)以防止过期。

*function.json* 文件使用创建操作定义了一个包含订阅输出绑定的 HTTP 触发器：

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

该 C# 脚本代码注册一个 webhook，当调用者收到 Outlook 邮件时它将通知此函数应用：

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

#### <a name="webhook-output---javascript-example"></a>Webhook 输出 - JavaScript 示例

以下示例创建订阅。 可以[刷新订阅](#webhook-subscription-refresh)以防止过期。

*function.json* 文件使用创建操作定义了一个包含订阅输出绑定的 HTTP 触发器：

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

该 JavaScript 代码注册一个 webhook，当调用者收到 Outlook 邮件时它将通知此函数应用：

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Webhook 输出 - 属性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [GraphWebHookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebHookSubscriptionAttribute.cs) 特性。

### <a name="webhook-output---configuration"></a>Webhook 输出 - 配置

下表解释了在 *function.json* 文件和 `GraphWebHookSubscription` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**name**||必需 - 在邮件的函数代码中使用的变量名称。 请参阅[在代码中使用 Outlook 邮件输出绑定](#outlook-output-code)。|
|**类型**||必需 - 必须设置为 `graphWebhookSubscription`。|
|**direction**||必需 - 必须设置为 `out`。|
|**identity**|**标识**|必需 - 将用于执行操作的标识。 可以是以下值之一：<ul><li><code>userFromRequest</code> - 仅对 [HTTP 触发器有效] 有效。 使用调用者的标识。</li><li><code>userFromId</code> - 使用具有指定 ID 的已登录用户的标识。 请参阅 <code>userId</code> 属性。</li><li><code>userFromToken</code> - 使用指定令牌代表的标识。 请参阅 <code>userToken</code> 属性。</li><li><code>clientCredentials</code> - 使用函数应用的标识。</li></ul>|
|**userId**|**UserId**  |仅在将 identity 设置为 `userFromId` 时为必需。 与已登录用户关联的用户主体 ID。|
|**userToken**|**UserToken**|仅在将 identity 设置为 `userFromToken` 时为必需。 函数应用的有效令牌。 |
|**action**|**Action**|必需 - 指定绑定应执行的操作。 可以是以下值之一：<ul><li><code>create</code> - 注册新订阅。</li><li><code>delete</code> - 删除指定订阅。</li><li><code>refresh</code> - 刷新指定订阅，避免它过期。</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|仅在将 _action_ 设置为 `create` 时为必需。 指定 Microsoft Graph 资源，以监视其更改。 请参阅[使用 Microsoft Graph 中的 webhook]。 |
|**changeType**|**ChangeType**|仅在将 _action_ 设置为 `create` 时为必需。 指示订阅资源中将触发通知的更改类型。 支持的值为：`created`、`updated`、`deleted`。 可以使用逗号分隔的列表组合多个值。|

### <a name="webhook-output---usage"></a>Webhook 输出 - 用法

此绑定将向 .NET 函数公开以下类型：
- 字符串
- Microsoft.Graph.Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Webhook 订阅刷新

有两种刷新订阅的方法：

- 使用应用程序标识处理所有订阅。 此方法需要 Azure Active Directory 管理员的许可。Azure Functions 支持的所有语言均可使用此方法。
- 通过手动绑定各个用户 ID 来使用与各个订阅关联的标识。 此方法需要一些自定义代码来执行绑定。 仅 .NET functions 可以使用此方法。

本部分包含其中每种方法的示例：

* [应用标识示例](#webhook-subscription-refresh---app-identity-example)
* [用户标识示例](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Webhook 订阅刷新 - 应用标识示例

参阅语言特定的示例：

* [C# 脚本 (.csx)](#app-identity-refresh---c-script-example)
* [JavaScript](#app-identity-refresh---javascript-example)

### <a name="app-identity-refresh---c-script-example"></a>应用标识刷新 - C# 脚本示例

以下示例使用应用程序标识来刷新订阅。

*function.json* 使用订阅输入绑定和订阅输出绑定定义一个计时器触发器：

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

该 C# 脚本代码刷新订阅：

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

### <a name="app-identity-refresh---c-script-example"></a>应用标识刷新 - C# 脚本示例

以下示例使用应用程序标识来刷新订阅。

*function.json* 使用订阅输入绑定和订阅输出绑定定义一个计时器触发器：

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

该 JavaScript 代码刷新订阅：

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

### <a name="webhook-subscription-refresh---user-identity-example"></a>Webhook 订阅刷新 - 用户标识示例

以下示例使用用户标识来刷新订阅。

*function.json*文件定义一个计时器触发器，并使订阅输入绑定遵从函数代码：

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

该 C# 脚本代码使用每个用户的标识刷新订阅，并在代码中创建输出绑定：

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

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)

[HTTP 触发器有效]: functions-bindings-http-webhook.md
[使用 Microsoft Graph 中的 webhook]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks

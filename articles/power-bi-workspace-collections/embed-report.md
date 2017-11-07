---
title: "嵌入 Azure Power BI 工作区集合中的报表 | Microsoft Docs"
description: "了解如何将 Azure Power BI 工作区集合中的报表嵌入到应用程序中。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ROBOTS: NOINDEX
ms.assetid: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: asaxton
ms.openlocfilehash: 56e7ca90132527c0ef9d4bd478e99b75ca055272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="embed-a-report-in-power-bi-workspace-collections"></a>嵌入 Power BI 工作区集合中的报表

了解如何将 Azure Power BI 工作区集合中的报表嵌入到应用程序中。

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月 或合同指示时可用。 建议规划迁移到 Power BI Embedded 以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

我们会探讨如何真正在应用程序中嵌入报表。 本文假设工作区集合中的工作区内已有一个报表。 如果尚未完成该步骤，请参阅 [Power BI 工作区集合入门](get-started.md)。

可以配合 Power BI 工作区集合使用 .NET (C#) 或 Node.js SDK 和 JavaScript，轻松构建应用程序。

## <a name="using-the-access-keys-to-use-rest-apis"></a>借助访问密钥使用 REST API

若要调用 REST API，可以传递访问密钥（可从给定工作区集合的 Azure 门户中获取）。 有关详细信息，请参阅 [Power BI 工作区集合入门](get-started.md)。

## <a name="get-a-report-id"></a>获取报表 ID

每个访问令牌基于某个报表。 需要获取想要嵌入的报表的给定报表 ID。 可以通过调用[获取报表](https://msdn.microsoft.com/library/azure/mt711510.aspx)REST API 来实现此目的。 此操作将返回报表 ID 和嵌入 URL。 可以使用 Power BI .NET SDK 或通过直接调用 REST API 来实现此目的。

### <a name="using-the-power-bi-net-sdk"></a>使用 Power BI .NET SDK

使用 .NET SDK 时，需要创建基于从 Azure 门户获取的访问密钥的令牌凭据。 这就需要安装 [Power BI API NuGet 包](https://www.nuget.org/profiles/powerbi)。

**安装 NuGet 包**

```
Install-Package Microsoft.PowerBI.Api
```

**C# 代码**

```
using Microsoft.PowerBI.Api.V1;
using Microsoft.Rest;

var credentials = new TokenCredentials("{access key}", "AppKey");
var client = new PowerBIClient(credentials);
client.BaseUri = new Uri(https://api.powerbi.com);

var reports = (IList<Report>)client.Reports.GetReports(workspaceCollectionName, workspaceId).Value;

// Select the report that you want to work with from the collection of reports.
```

### <a name="calling-the-rest-api-directly"></a>直接调用 REST API

```
System.Net.WebRequest request = System.Net.WebRequest.Create("https://api.powerbi.com/v1.0/collections/{collectionName}/workspaces/{workspaceId}/Reports") as System.Net.HttpWebRequest;

request.Method = "GET";
request.ContentLength = 0;
request.Headers.Add("Authorization", String.Format("AppKey {0}", accessToken.Value));

using (var response = request.GetResponse() as System.Net.HttpWebResponse)
{
    using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
    {
        // Work with JSON response to get the report you want to work with.
    }

}
```

## <a name="create-an-access-token"></a>创建访问令牌

Power BI 工作区集合使用嵌入标记，即 HMAC 签名的 JSON Web 令牌。 令牌已使用 Power BI 工作区集合中的访问密钥签名。 默认情况下，嵌入令牌用于提供对要嵌入应用程序的报表的只读访问权限。 嵌入令牌是针对特定报表颁发的，应该与嵌入 URL 相关联。

应在服务器上创建访问令牌，因为要使用访问密钥对令牌进行签名/加密。 有关如何创建访问令牌的信息，请参阅[通过 Power BI 工作区集合进行身份验证和授权](app-token-flow.md)。 此外，还可以查看 [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) 方法。 以下示例演示了如何使用用于 Power BI 的 .NET SDK。

将使用前面检索到的报表 ID。 创建嵌入令牌后，将使用访问密钥来生成可从 JavaScript 的角度使用的令牌。 *PowerBIToken 类*要求安装 [Power BI Core NuGut 包](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)。

**安装 NuGet 包**

```
Install-Package Microsoft.PowerBI.Core
```

**C# 代码**

```
using Microsoft.PowerBI.Security;

// rlsUsername, roles and scopes are optional.
embedToken = PowerBIToken.CreateReportEmbedToken(workspaceCollectionName, workspaceId, reportId, rlsUsername, roles, scopes);

var token = embedToken.Generate("{access key}");
```

### <a name="adding-permission-scopes-to-embed-tokens"></a>将权限范围添加到嵌入令牌

使用 Embed 令牌时，对于允许对其进行访问的资源，你可能希望限制其使用。 为此，可以生成一个权限范围受到约束的令牌。 有关详细信息，请参阅[范围](app-token-flow.md#scopes)

## <a name="embed-using-javascript"></a>使用 JavaScript 嵌入

获取访问令牌和报表 ID 后，可以使用 JavaScript 来嵌入报表。 这就需要安装 NuGet [Power BI JavaScript 包](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)。 https://embedded.powerbi.com/appTokenReportEmbed 即为 embedUrl。

> [!NOTE]
> 可以使用 [JavaScript 报表嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)测试功能。 我们还提供了适用于不同操作的代码示例。

**安装 NuGet 包**

```
Install-Package Microsoft.PowerBI.JavaScript
```

**JavaScript 代码**

```
<script src="/scripts/powerbi.js"></script>
<div id="reportContainer"></div>

var embedConfiguration = {
    type: 'report',
    accessToken: 'eyJ0eXAiO...Qron7qYpY9MI',
    id: '5dac7a4a-4452-46b3-99f6-a25915e0fe55',
    embedUrl: 'https://embedded.powerbi.com/appTokenReportEmbed'
};

var $reportContainer = $('#reportContainer');
var report = powerbi.embed($reportContainer.get(0), embedConfiguration);
```

### <a name="set-the-size-of-embedded-elements"></a>设置嵌入元素的大小

报表会根据其容器大小自动嵌入。 若要重写默认的嵌入项大小，只需添加 CSS 类属性，或者宽度和高度的内联样式。

## <a name="see-also"></a>另请参阅

[示例入门](get-started-sample.md)  
[Power BI 工作区集合中的身份验证和授权](app-token-flow.md)  
[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[JavaScript 嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)  
[Power BI JavaScript 包](https://www.nuget.org/packages/Microsoft.PowerBI.JavaScript/)  
[Power BI API NuGet 包](https://www.nuget.org/profiles/powerbi)
[Power BI Core NuGut 包](https://www.nuget.org/packages/Microsoft.PowerBI.Core/)  
[PowerBI-CSharp Git 存储库](https://github.com/Microsoft/PowerBI-CSharp)  
[PowerBI-Node Git 存储库](https://github.com/Microsoft/PowerBI-Node)  

有更多问题？ [尝试 Power BI 社区](http://community.powerbi.com/)

---
title: 会话管理 REST API
description: 描述如何管理会话
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681241"
---
# <a name="use-the-session-management-rest-api"></a>使用会话管理 REST API

要使用 Azure 远程呈现功能，需要创建*会话*。 每个会话对应于在 Azure 中分配的虚拟机 （VM） 并等待客户端设备连接。 当设备连接时，VM 呈现请求的数据，并将结果作为视频流提供。 在创建会话期间，您选择要运行的服务器类型，这决定了定价。 一旦不再需要会话，就应该停止会话。 如果不手动停止，则在会话的*租约时间*到期时，它将自动关闭。

我们在*脚本*文件夹中的[ARR 示例存储库](https://github.com/Azure/azure-remote-rendering)（称为*呈现会话.ps1）* 中提供了 PowerShell 脚本，该脚本演示了我们服务的使用情况。 此处介绍了脚本及其配置：[示例 PowerShell 脚本](../samples/powershell-example-scripts.md)

> [!TIP]
> 此页上列出的 PowerShell 命令旨在相互补充。 如果在同一 PowerShell 命令提示符中按顺序运行所有脚本，它们将相互构建。

## <a name="regions"></a>区域

请参阅要将请求发送到的基本 URL[的可用区域列表](../reference/regions.md)。

对于下面的示例脚本，我们选择了*西乌斯2*区域。

### <a name="example-script-choose-an-endpoint"></a>示例脚本：选择终结点

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>帐户

如果没有远程呈现帐户，[请创建一个](create-an-account.md)。 每个资源都由*一个帐户 Id*标识，该帐户 Id 在整个会话 API 中使用。

### <a name="example-script-set-accountid-and-accountkey"></a>示例脚本：设置帐户 Id 和帐户密钥

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>常见请求标头

* *授权*标头的值必须为`Bearer TOKEN`"，其中"`TOKEN`是[安全令牌服务返回的](tokens.md)身份验证令牌。

### <a name="example-script-request-a-token"></a>示例脚本：请求令牌

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>常见响应标头

* 产品团队可以使用*MS-CV*标头跟踪服务中的调用。

## <a name="create-a-session"></a>创建会话

此命令创建会话。 它返回新会话的 ID。 所有其他命令都需要会话 ID。

| URI | 方法 |
|-----------|:-----------|
| /v1/帐户/*帐户 Id*/会话/创建 | POST |

**请求正文：**

* 最大租赁时间（时间跨度）：自动停用 VM 时的超时值
* 模型（数组）：要预加载的资产容器 URL
* 大小（字符串）：VM 大小 **（"标准"** 或 **"高级"）。** 请参阅特定的[VM 大小限制](../reference/limits.md#overall-number-of-polygons)。

**反应：**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 202 | - 会话 Id： GUID | Success |

### <a name="example-script-create-a-session"></a>示例脚本：创建会话

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

示例输出：

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>示例脚本：存储会话 Id

来自上述请求的响应包括会话**Id**，您需要用于所有后续请求。

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>更新会话

此命令更新会话的参数。 目前，您只能延长会话的租约时间。

> [!IMPORTANT]
> 自会话开始以来，租赁时间始终作为总时间给出。 这意味着，如果您创建了一个租约时间为一小时的会话，并且希望将其租约时间再延长一小时，则必须将其最大租约时间更新为两小时。

| URI | 方法 |
|-----------|:-----------|
| /v1/帐户/*帐户 ID*/会话/*会话 Id* | PATCH |

**请求正文：**

* 最大租赁时间（时间跨度）：自动停用 VM 时的超时值

**反应：**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | | Success |

### <a name="example-script-update-a-session"></a>示例脚本：更新会话

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

示例输出：

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>获取活动会话

此命令返回活动会话的列表。

| URI | 方法 |
|-----------|:-----------|
| /v1/帐户/*帐户 Id*/会话 | GET |

**反应：**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | - 会话：会话属性数组 | 有关会话属性的说明，请参阅"获取会话属性"部分 |

### <a name="example-script-query-active-sessions"></a>示例脚本：查询活动会话

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

示例输出：

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>获取会话属性

此命令返回有关会话的信息，例如其 VM 主机名。

| URI | 方法 |
|-----------|:-----------|
| /v1/帐户/*帐户 Id*/会话/*会话 Id*/属性 | GET |

**反应：**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | - 消息：字符串<br/>- 会话经过时间：时间跨度<br/>- 会话主机名：字符串<br/>- 会话 Id：字符串<br/>- 会话最大租赁时间：时间跨度<br/>- 会话大小：枚举<br/>- 会话状态：枚举 | 枚举会话状态 [ 启动、 就绪、 停止、 停止、 过期、 错误]<br/>如果状态为"错误"或"过期"，则邮件将包含更多信息 |

### <a name="example-script-get-session-properties"></a>示例脚本：获取会话属性

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

示例输出：

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>停止会话

此命令停止会话。 分配的 VM 将在不久后回收。

| URI | 方法 |
|-----------|:-----------|
| /v1/帐户/*帐户 Id*/会话/*会话 Id* | DELETE |

**反应：**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>示例脚本：停止会话

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

示例输出：

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>后续步骤

* [电源外壳脚本示例](../samples/powershell-example-scripts.md)

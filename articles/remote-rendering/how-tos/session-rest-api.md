---
title: 会话管理 REST API
description: 描述如何管理会话
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 0af9d6906e038a4b9285a2c302fc0c98345fdbd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90023748"
---
# <a name="use-the-session-management-rest-api"></a>使用会话管理 REST API

若要使用 Azure 远程呈现功能，需要创建一个 *会话*。 每个会话都对应于要在 Azure 中分配的虚拟机 (VM) 正在等待客户端设备连接。 当设备连接时，VM 将呈现请求的数据，并将结果作为视频流提供。 在会话创建过程中，您选择了要在哪种服务器上运行，这会确定定价。 不再需要会话时，应停止此会话。 如果未手动停止，它将在会话的 *租约时间* 到期时自动关闭。

我们在 "*脚本*" 文件夹（称为*RenderingSession.ps1*）中的 " [ARR 示例" 存储库](https://github.com/Azure/azure-remote-rendering)中提供了一个 PowerShell 脚本，它演示了如何使用我们的服务。 此脚本及其配置如下所述： [PowerShell 脚本示例](../samples/powershell-example-scripts.md)

> [!TIP]
> 此页上列出的 PowerShell 命令旨在互相补充。 如果在同一个 PowerShell 命令提示符中按顺序运行所有脚本，则这些脚本将在彼此之上进行构建。

## <a name="regions"></a>区域

请参阅要将请求发送到的基本 Url 的 [可用区域列表](../reference/regions.md) 。

对于下面的示例脚本，我们选择区域 *westus2*。

### <a name="example-script-choose-an-endpoint"></a>示例脚本：选择终结点

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>帐户

如果没有远程呈现帐户，请 [创建一个](create-an-account.md)。 每个资源都由 AccountId 标识，该 *accountId*在整个会话 api 中使用。

### <a name="example-script-set-accountid-and-accountkey"></a>示例脚本： Set accountId 和 accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>常见请求标头

* *授权*标头的值必须为 " `Bearer TOKEN` "，其中 " `TOKEN` " 是[安全令牌服务返回](tokens.md)的身份验证令牌。

### <a name="example-script-request-a-token"></a>示例脚本：请求令牌

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>常见响应标头

* 产品团队可以使用 *MS CV* 标头来跟踪服务中的调用。

## <a name="create-a-session"></a>创建会话

此命令创建一个会话。 它将返回新会话的 ID。 你需要所有其他命令的会话 ID。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**请求正文：**

* maxLeaseTime (timespan) ：会话将自动解除授权时的超时值
*  (数组) 模型：要预加载的资产容器 Url
* size (字符串) ：要配置的服务器大小 ([**"标准"**](../reference/vm-sizes.md) 或 [**"高级"**](../reference/vm-sizes.md)) 。 请参阅特定 [大小限制](../reference/limits.md#overall-number-of-polygons)。

**响应**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 202 | -sessionId： GUID | 成功 |

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

### <a name="example-script-store-sessionid"></a>示例脚本：存储 sessionId

上述请求中的响应包含 SessionId，你需要为所有后续请求提供一个 **sessionId**。

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>修改和查询会话属性

有几个命令可用于查询或修改现有会话的参数。

> [!CAUTION]
> 对于所有 REST 调用，通常情况下，发送这些命令会导致服务器中止并返回故障。 在本例中，状态代码为 429 ( "请求太多" ) 。 根据经验法则，后续调用之间应有 5-10 秒的延迟。

### <a name="update-session-parameters"></a>更新会话参数

此命令将更新会话的参数。 目前只能延长会话的租约时间。

> [!IMPORTANT]
> 租约时间始终被指定为自会话开始后的总时间。 这意味着，如果你创建了一个租约时间为一小时的会话，并且想要将其租约时间延长一小时，则必须将其 maxLeaseTime 更新为两小时。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/*sessionId* | PATCH |

**请求正文：**

* maxLeaseTime (timespan) ：会话将自动解除授权时的超时值

**响应**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | | Success |

#### <a name="example-script-update-a-session"></a>示例脚本：更新会话

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

### <a name="get-active-sessions"></a>获取活动会话

此命令返回活动会话的列表。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**响应**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | -会话：会话属性的数组 | 有关会话属性的说明，请参阅 "获取会话属性" 部分 |

#### <a name="example-script-query-active-sessions"></a>示例脚本：查询活动会话

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

### <a name="get-sessions-properties"></a>获取会话属性

此命令返回有关会话的信息，如会话的 VM 主机名。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/properties | GET |

**响应**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 200 | -message： string<br/>-sessionElapsedTime： timespan<br/>-sessionHostname： string<br/>-sessionId： string<br/>-sessionMaxLeaseTime： timespan<br/>-sessionSize： enum<br/>-sessionStatus： enum | 枚举 sessionStatus {启动、准备、停止、停止、过期、出错}<br/>如果状态为 "错误" 或 "已过期"，则该消息将包含详细信息 |

#### <a name="example-script-get-session-properties"></a>示例脚本：获取会话属性

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

此命令停止会话。 分配的 VM 不久后将被回收。

| URI | 方法 |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId* | DELETE |

**响应**

| 状态代码 | JSON 有效负载 | 注释 |
|-----------|:-----------|:-----------|
| 204 | | 成功 |

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

* [PowerShell 脚本示例](../samples/powershell-example-scripts.md)

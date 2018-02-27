---
title: "Log Analytics HTTP 数据收集器 API | Microsoft Docs"
description: "可以使用 Log Analytics HTTP 数据收集器 API，从能够调用 REST API 的任何客户端将 POST JSON 数据添加到 Log Analytics 存储库。 本文介绍如何使用 API，并提供关于如何使用不同的编程语言发布数据的示例。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: 5c6f2b35b48988af533612cb48da8fe79a838cf6
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2018
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>使用 HTTP 数据收集器 API（公共预览版）将数据发送到 Log Analytics
本文说明如何使用 HTTP 数据收集器 API 从 REST API 客户端将数据发送到 Log Analytics。  它说明对于脚本或应用程序收集的数据，如何设置其格式、将其包含在请求中，并由 Log Analytics 授权该请求。  将针对 PowerShell、C# 和 Python 提供示例。

> [!NOTE]
> Log Analytics HTTP 数据收集器 API 以公共预览版形式提供。

## <a name="concepts"></a>概念
可以使用 HTTP 数据收集器 API 从任何可以调用 REST API 的客户端将数据发送到 Log Analytics。  这可能是 Azure 自动化中从 Azure 或另一个云收集管理数据的 runbook，也可能是使用 Log Analytics 合并并分析数据的备用管理系统。

Log Analytics 存储库中的所有数据都存储为具有某种特定记录类型的记录。  可以将要发送到 HTTP 数据收集器 API 的数据格式化为采用 JSON 格式的多条记录。  提交数据时，将针对请求负载中的每条记录在存储库中创建单独的记录。


![HTTP 数据收集器概述](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>创建请求
若要使用 HTTP 数据收集器 API，可以创建一个 POST 请求，其中包含要以 JavaScript 对象表示法 (JSON) 格式发送的数据。  接下来的三个表列出了每个请求所需的属性。 在本文的后面部分将更详细地介绍每个属性。

### <a name="request-uri"></a>请求 URI
| 属性 | 属性 |
|:--- |:--- |
| 方法 |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| 内容类型 |application/json |

### <a name="request-uri-parameters"></a>请求 URI 参数
| 参数 | 说明 |
|:--- |:--- |
| CustomerID |Log Analytics 工作区的唯一标识符。 |
| 资源 |API 资源名称: /api/logs。 |
| API 版本 |用于此请求的 API 版本。 目前，API 版本为 2016-04-01。 |

### <a name="request-headers"></a>请求标头
| 标头 | 说明 |
|:--- |:--- |
| 授权 |授权签名。 在本文的后面部分，可以了解有关如何创建 HMAC-SHA256 标头的信息。 |
| Log-Type |指定正在提交的数据的记录类型。 目前，日志类型仅支持字母字符。 它不支持数字或特殊字符。 |
| x-ms-date |处理请求的日期，采用 RFC 1123 格式。 |
| time-generated-field |数据中包含数据项时间戳的字段名称。 如果指定某一字段，其内容用于 **TimeGenerated**。 如果未指定此字段，**TimeGenerated** 的默认值是引入消息的时间。 消息字段的内容应遵循 ISO 8601 格式 YYYY-MM-DDThh:mm:ssZ。 |

## <a name="authorization"></a>授权
Log Analytics HTTP 数据收集器 API 的任何请求都必须包含授权标头。 若要验证请求，必须针对发出请求的工作区使用主要或辅助密钥对请求进行签名。 然后，传递该签名作为请求的一部分。   

下面是授权标头的格式：

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* 是 Log Analytics 工作区的唯一标识符。 *签名*是[基于哈希的消息验证代码 (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx)，它构造自请求并使用 [SHA256 算法](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx)进行计算。 然后，使用 Base64 编码进行编码。

使用此格式对 **SharedKey** 签名字符串进行编码：

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

下面是签名字符串的示例：

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

如果具有签名字符串，可在 UTF-8 编码的字符串上使用 HMAC-SHA256 算法对其进行编码，并以 Base64 形式对结果进行编码。 使用以下格式：

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

后续部分中的示例提供了示例代码，可帮助你创建授权标头。

## <a name="request-body"></a>请求正文
消息正文必须采用 JSON 格式。 它必须包括一个或多个记录，其中包含采用以下格式的属性名称和值对：

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

可以使用以下格式在单个请求中一同批处理多个记录。 所有记录必须都为相同的记录类型。

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>记录类型和属性
在通过 Log Analytics HTTP 数据收集器 API 提交数据时，定义自定义的记录类型。 目前，无法将数据写入已由其他数据类型和解决方案创建的现有记录类型。 Log Analytics 会读取传入数据，并创建与输入值的数据类型匹配的属性。

对 Log Analytics API 的每个请求必须包括 **Log-Type** 标头以及记录类型的名称。 后缀 **_CL** 会自动附加到所输入的名称作为自定义日志，以将其与其他日志类型区分开来。 例如，如果输入的名称为 **MyNewRecordType**，Log Analytics 将创建类型为 **MyNewRecordType_CL** 的记录。 这会有助于确保用户创建的类型名称与当前或未来的 Microsoft 解决方案中提供的名称之间不存在任何冲突。

要标识属性的数据类型，Log Analytics 将为属性名称添加后缀。 如果某个属性包含 null 值，该属性将不包括在该记录中。 下表列出了属性数据类型及相应的后缀：

| 属性数据类型 | 后缀 |
|:--- |:--- |
| String |_s |
| 布尔 |_b |
| Double |_d |
| 日期/时间 |_t |
| GUID |_g |

Log Analytics 对每个属性所使用的数据类型取决于新记录的记录类型是否已存在。

* 如果记录类型不存在，Log Analytics 将创建新的记录类型。 Log Analytics 使用 JSON 类型推理，为新记录的每个属性确定数据类型。
* 如果记录类型确实存在，Log Analytics 将尝试基于现有属性创建新记录。 如果新记录中的属性数据类型不匹配并且无法转换为现有类型，或者如果记录中包含的属性不存在，Log Analytics 将创建一个具有相关后缀的新属性。

例如，此提交条目将创建具有以下三个属性的记录：**number_d**、**boolean_b** 和 **string_s**：

![示例记录 1](media/log-analytics-data-collector-api/record-01.png)

如果提交了下面的条目，并且所有值都采用字符串格式，则这些属性将不会更改。 这些值都可以转换为现有数据类型：

![示例记录 2](media/log-analytics-data-collector-api/record-02.png)

但是，如果提交了下面的内容，Log Analytics 会创建新属性 **boolean_d** 和 **string_d**。 这些值不能转换：

![示例记录 3](media/log-analytics-data-collector-api/record-03.png)

如果提交了后续条目，在记录类型创建前，Log Analytics 将创建具有以下三个属性的记录：**number_s**、**boolean_s** 和 **string_s**。 在此条目中，每个初始值都采用字符串的格式：

![示例记录 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>数据限制
发布到 Log Analytics 数据收集 API 的数据有一些限制。

* 每次发布到 Log Analytics 数据收集器 API 的数据最大为 30 MB。 这是对单次发布的大小限制。 如果单次发布的数据超过 30 MB，应将数据拆分为较小的区块，并同时发送它们。
* 字段值最大为 32 KB。 如果字段值大于 32 KB，数据将截断。
* 给定类型的推荐最大字段数是 50 个。 这是从可用性和搜索体验角度考虑的现实限制。  

## <a name="return-codes"></a>返回代码
HTTP 状态代码 200 表示已接收请求以便进行处理。 这表示操作已成功完成。

此表列出了服务可能返回的完整状态代码集：

| 代码 | 状态 | 错误代码 | 说明 |
|:--- |:--- |:--- |:--- |
| 200 |OK | |已成功接受请求。 |
| 400 |错误的请求 |InactiveCustomer |工作区已关闭。 |
| 400 |错误的请求 |InvalidApiVersion |服务无法识别所指定的 API 版本。 |
| 400 |错误的请求 |InvalidCustomerId |指定的工作区 ID 无效。 |
| 400 |错误的请求 |InvalidDataFormat |已提交无效的 JSON。 响应正文可能包含有关如何解决该错误的详细信息。 |
| 400 |错误的请求 |InvalidLogType |所指定日志类型包含特殊字符或数字。 |
| 400 |错误的请求 |MissingApiVersion |未指定 API 版本。 |
| 400 |错误的请求 |MissingContentType |未指定内容类型。 |
| 400 |错误的请求 |MissingLogType |未指定所需的值日志类型。 |
| 400 |错误的请求 |UnsupportedContentType |内容类型未设为“application/json”。 |
| 403 |禁止 |InvalidAuthorization |服务未能对请求进行身份验证。 验证工作区 ID 和连接密钥是否有效。 |
| 404 |未找到 | | 提供的 URL 不正确，或请求太大。 |
| 429 |请求过多 | | 服务遇到来自帐户的大量数据。 请稍后重试请求。 |
| 500 |内部服务器错误 |UnspecifiedError |服务遇到内部错误。 请重试请求。 |
| 503 |服务不可用 |ServiceUnavailable |服务当前不可用，无法接收请求。 请重试请求。 |

## <a name="query-data"></a>查询数据
若要查询由 Log Analytics HTTP 数据收集器 API 提交的数据，请搜索以下记录：**Type** 等于指定的 **LogType** 值并且附加 **_CL**。 例如，如果使用了 **MyCustomLog**，则返回具有 **Type=MyCustomLog_CL** 的所有记录。

>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则上述查询会更改为如下所示。

> `MyCustomLog_CL`

## <a name="sample-requests"></a>示例请求
在接下来的部分中，将找到如何使用不同的编程语言将数据提交到 Log Analytics HTTP 数据收集器 API 的示例。

对于每个示例，执行以下步骤来设置授权标头的变量：

1. 在 Azure 门户中，找到 Log Analytics 工作区。
2. 依次选择“高级设置”和“已连接的源”。
2. 在 **Workspace ID** 的右侧，选择复制图标，并粘贴该 ID 作为 **Customer ID** 变量的值。
3. 在 **Primary Key** 的右侧，选择复制图标，并粘贴该 ID 作为 **Shared Key** 变量的值。

或者，可以针对日志类型和 JSON 数据更改变量。

### <a name="powershell-sample"></a>PowerShell 示例
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>C# 示例
```
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(message);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-sample"></a>Python 示例
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>后续步骤
- 使用[日志搜索 API](log-analytics-log-search-api.md) 从 Log Analytics 存储库中检索数据。

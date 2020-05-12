---
title: Azure 存储监视数据引用 |Microsoft Docs
description: 用于监视来自 Azure 存储的数据的日志和指标参考。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.openlocfilehash: 7ba66441a87e3e02483ae27400f9900d2d052af4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118152"
---
# <a name="azure-storage-monitoring-data-reference"></a>Azure 存储监视数据引用

有关收集和分析 Azure 存储的监视数据的详细信息，请参阅[监视 Azure 存储](monitor-storage.md)。

## <a name="metrics"></a>指标

下表列出了为 Azure 存储收集的平台指标。 

### <a name="capacity-metrics"></a>容量度量值

容量指标每隔一小时发送到 Azure Monitor。 值每日刷新。 时间粒度定义呈现指标值的时间间隔。 所有容量指标的受支持时间粒度为一小时 (PT1H)。

Azure 存储在 Azure Monitor 中提供以下容量指标。

#### <a name="account-level"></a>帐户级别

| 指标 | 说明 |
| ------------------- | ----------------- |
| UsedCapacity | 存储帐户使用的存储量。 对于标准存储帐户，该指标是 Blob、表、文件和队列使用的容量总和。 对于高级存储帐户和 Blob 存储帐户，它与 BlobCapacity 相同。 <br/><br/> 单元：字节 <br/> 聚合类型：平均 <br/> 值示例：1024 |

#### <a name="blob-storage"></a>Blob 存储

| 指标 | 说明 |
| ------------------- | ----------------- |
| BlobCapacity | 存储帐户中使用的 Blob 存储总计。 <br/><br/> 单元：字节 <br/> 聚合类型：平均 <br/> 值示例：1024 <br/> 维度： **BlobType**和**BlobTier** （[定义](#metrics-dimensions)） |
| BlobCount    | 在存储帐户中存储的 Blob 对象数。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 <br/> 维度： **BlobType**和**BlobTier** （[定义](#metrics-dimensions)） |
| ContainerCount    | 存储帐户中的容器数。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 |
| IndexCapacity     | ADLS Gen2 分层索引所使用的存储量 <br/><br/> 单元：字节 <br/> 聚合类型：平均 <br/> 值示例：1024 |

#### <a name="table-storage"></a>表存储

| 指标 | 说明 |
| ------------------- | ----------------- |
| TableCapacity | 存储帐户使用的表存储量。 <br/><br/> 单元：字节 <br/> 聚合类型：平均 <br/> 值示例：1024 |
| TableCount   | 存储帐户中的表数目。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 |
| TableEntityCount | 存储帐户中的表实体数目。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 |

#### <a name="queue-storage"></a>队列存储

| 指标 | 说明 |
| ------------------- | ----------------- |
| QueueCapacity | 存储帐户使用的队列存储量。 <br/><br/> 单元：字节 <br/> 聚合类型：平均 <br/> 值示例：1024 |
| QueueCount   | 存储帐户中的队列数目。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 |
| QueueMessageCount | 存储帐户中未失效的队列消息数目。 <br/><br/>单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 |

#### <a name="file-storage"></a>文件存储

| 指标 | 说明 |
| ------------------- | ----------------- |
| FileCapacity | 存储帐户使用的文件存储量。 <br/><br/> 单元：字节 <br/> 聚合类型：平均 <br/> 值示例：1024 |
| FileCount   | 存储帐户中的文件数目。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 |
| FileShareCount | 存储帐户中的文件共享数目。 <br/><br/> 单位：计数 <br/> 聚合类型：平均 <br/> 值示例：1024 |

### <a name="transaction-metrics"></a>事务度量值

从 Azure 存储到 Azure Monitor 的每个存储帐户请求都会发出事务指标。 如果存储帐户中没有任何活动，则在此期间不会有关于事务指标的数据。 所有事务指标在帐户和服务级别（Blob 存储、表存储、Azure 文件和队列存储）提供。 时间粒度定义呈现指标值的时间间隔。 所有事务指标的受支持时间粒度为 PT1H 和 PT1M。

Azure 存储在 Azure Monitor 中提供以下事务指标。

| 指标 | 说明 |
| ------------------- | ----------------- |
| 事务 | 向存储服务或指定的 API 操作发出的请求数。 此数字包括成功和失败的请求数，以及引发错误的请求数。 <br/><br/> 单位：计数 <br/> 聚合类型：总计 <br/> 适用维度：ResponseType、GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)）<br/> 值示例：1024 |
| 流入量 | 流入数据量。 此数字包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。 <br/><br/> 单元：字节 <br/> 聚合类型：总计 <br/> 适用维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| 流出量 | 流出数据量。 此数字包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数字不反映计费的流出量。 <br/><br/> 单元：字节 <br/> 聚合类型：总计 <br/> 适用维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| SuccessServerLatency | Azure 存储处理成功请求所用的平均时间。 此值不包括 SuccessE2ELatency 中指定的网络延迟。 <br/><br/> 单位：毫秒 <br/> 聚合类型：平均 <br/> 适用维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| SuccessE2ELatency | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 <br/><br/> 单位：毫秒 <br/> 聚合类型：平均 <br/> 适用维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：1024 |
| 可用性 | 存储服务或指定的 API 操作的可用性百分比。 可用性通过由“计费请求总数”值除以适用的请求数（包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 <br/><br/> 单位：百分比 <br/> 聚合类型：平均 <br/> 适用维度：GeoType、ApiName 和 Authentication（[定义](#metrics-dimensions)） <br/> 值示例：99.99 |

<a id="metrics-dimensions" />

## <a name="metrics-dimensions"></a>指标维度

Azure 存储支持对 Azure Monitor 中的指标使用以下维度。

| 维度名称 | 说明 |
| ------------------- | ----------------- |
| **BlobType** | 仅限 Blob 指标的 Blob 类型。 支持的值为 **BlockBlob**、**PageBlob** 和 **Azure Data Lake Storage**。 BlockBlob 中包含追加 Blob。 |
| **BlobTier** | Azure 存储提供了不同的访问层，允许以最具成本效益的方式存储 Blob 对象数据。 请在 [Azure 存储 Blob 层](../blobs/storage-blob-storage-tiers.md)中查看详细信息。 支持的值包括： <br/> <li>**热**：热层</li> <li>**酷**：冷层</li> <li>**存档**：存档层</li> <li>**高级**：块 blob 的高级层</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**：高级页 blob 的层类型</li> <li>**标准**：标准页 Blob 的层类型</li> <li>**Untiered**：常规用途 v1 存储帐户的层类型</li> |
| **GeoType** | 来自主要或辅助群集的事务。 可用值包括 "**主要**" 和 "**辅助**"。 从辅助租户读取对象时，该维度会应用到读取访问异地冗余存储 (RA-GRS)。 |
| **ResponseType** | 事务响应类型。 可用的值包括： <br/><br/> <li>**ServerOtherError**：除所述的所有其他服务器端错误 </li> <li>**ServerBusyError**：返回了 HTTP 503 状态代码的经过身份验证的请求。 </li> <li>**ServerTimeoutError**：返回了 HTTP 500 状态代码的已超时身份验证请求。 由于服务器错误而发生超时。 </li> <li>**Authorizationerror)**：由于未经授权的数据访问或授权失败而失败的经过身份验证的请求。 </li> <li>**NetworkError**：因网络错误而失败的经过身份验证的请求。 往往发生于客户端在超时失效之前提前关闭了连接时。 </li><li>**ClientAccountBandwidthThrottlingError**：对超出[存储帐户可伸缩性限制](scalability-targets-standard-account.md)的带宽限制请求。</li><li>**ClientAccountRequestThrottlingError**：按请求速率限制请求，超过[存储帐户的可伸缩性限制](scalability-targets-standard-account.md)。<li>**ClientThrottlingError**：其他客户端限制错误。 排除 ClientAccountBandwidthThrottlingError 和 ClientAccountRequestThrottlingError。</li> <li>**ClientTimeoutError**：返回了 HTTP 500 状态代码的已超时身份验证请求。 如果将客户端的网络超时或请求超时设置为比存储服务预期值更小的值，则预期会发生此超时。 否则，会报告为 ServerTimeoutError。 </li> <li>**ClientOtherError**：除描述的所有其他客户端错误。 </li> <li>**成功**：请求成功</li> <li> **SuccessWithThrottling**：在首次尝试时，SMB 客户端被阻止但在重试后成功的请求成功。</li> |
| **ApiName** | 操作的名称。 例如： <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> 有关所有操作名称，请参阅[文档](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 |
| **身份验证** | 事务中所用的身份验证类型。 可用的值包括： <br/> <li>**AccountKey**：通过存储帐户密钥对事务进行身份验证。</li> <li>**SAS**：使用共享访问签名对事务进行身份验证。</li> <li>**Oauth**：使用 oauth 访问令牌对事务进行身份验证。</li> <li>**匿名**：以匿名方式请求事务。 不包括预检请求。</li> <li>**AnonymousPreflight**：事务为预检请求。</li> |

对于支持维度的指标，需要指定维度值才能查看相应的指标值。 例如，如果查看成功响应的 **Transactions** 值，需要使用 **Success** 筛选 **ResponseType** 维度。 或者，如果查看块 Blob 的 **BlobCount** 值，需要使用 **BlockBlob** 筛选 **BlobType** 维度。

## <a name="resource-logs-preview"></a>资源日志（预览）

> [!NOTE]
> Azure Monitor 中的 Azure 存储日志提供公共预览版，可用于所有公有云区域中的预览版测试。 若要注册预览版，请参阅[此页](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)。  此预览版启用常规用途 v1 和常规用途 v2 存储帐户中的 blob （包括 Azure Data Lake Storage Gen2）、文件、队列、表、高级存储帐户的日志。 不支持经典存储帐户。

下表列出了 Azure 存储资源日志在 Azure Monitor 日志或 Azure 存储中收集时的属性。 属性描述用于执行操作的操作、服务和授权类型。

### <a name="fields-that-describe-the-operation"></a>描述操作的字段

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| 属性 | 说明 |
|:--- |:---|
|**time** | 存储收到请求时的协调世界时（UTC）时间。 例如：`2018/11/08 21:09:36.6900118`。|
|**resourceId** | 存储帐户的资源 ID。 例如： `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | 请求的操作的类别。 例如：`StorageRead`、`StorageWrite` 或 `StorageDelete`。|
|**operationName** | 执行的 REST 操作的类型。 <br> 有关操作的完整列表，请参阅[存储分析记录的操作和状态消息主题](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 |
|**operationVersion** | 发出请求时指定的存储服务版本。 这等效于**x-ms 版本**标头的值。 例如：`2017-04-17`。|
|**schemaVersion** | 日志的架构版本。 例如：`1.0`。|
|**statusCode** | 请求的 HTTP 状态代码。 如果请求中断，此值可能会设置为 `Unknown` 。 <br> 例如： `206` |
|**statusText** | 所请求操作的状态。  有关状态消息的完整列表，请参阅[存储分析记录的操作和状态消息主题](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)。 在版本2017-04-17 及更高版本中， `ClientOtherError` 不使用状态消息。 相反，此字段包含错误代码。 例如： `SASSuccess`  |
|**durationMs** | 执行所请求操作的总时间（以毫秒为单位）。 这包括读取传入请求和向请求者发送响应的时间。 例如：`12`。|
|**callerIpAddress** | 请求者的 IP 地址，包括端口号。 例如：`192.100.0.102:4362`。 |
|**correlationId** | 用于跨资源关联日志的 ID。 例如：`b99ba45e-a01e-0042-4ea6-772bbb000000`。 |
|**location** | 存储帐户的位置。 例如：`North Europe`。 |
|**protocol**|操作中使用的协议。 例如： `HTTP` 、 `HTTPS` 、 `SMB` 或`NFS`|
| **uri** | 请求的统一资源标识符。 例如：`http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`。 |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>描述如何对操作进行身份验证的字段

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| 属性 | 说明 |
|:--- |:---|
|**标识/类型** | 用于发出请求的身份验证类型。 例如： `OAuth` 、 `SAS Key` 、 `Account Key` 或`Anonymous` |
|**identity/tokenHash**|此字段保留为仅供内部使用。 |
|**授权/操作** | 分配给请求的操作。 例如： `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**authorization/roleAssignmentId** | 角色分配 ID。 例如：`4e2521b7-13be-4363-aeda-111111111111`。|
|**authorization/roleDefinitionId** | 角色定义 ID。 例如：`ba92f5b4-2d11-453d-a403-111111111111"`。|
|**主体/id** | 安全主体的 ID。 例如：`a4711f3a-254f-4cfb-8a2d-111111111111`。|
|**主体/类型** | 安全主体的类型。 例如：`ServicePrincipal`。 |
|**请求者/appID** | 用作请求方的 Open Authorization （OAuth）应用程序 ID。 <br> 例如：`d3f7d5fe-e64a-4e4e-871d-333333333333`。|
|**申请人/受众** | 请求的 OAuth 受众。 例如：`https://storage.azure.com`。 |
|**请求者/objectId** | 请求者的 OAuth 对象 ID。 对于 Kerberos 身份验证，表示 Kerberos 身份验证用户的对象标识符。 例如：`0e0bf547-55e5-465c-91b7-2873712b249c`。 |
|**请求者/tenantId** | 标识的 OAuth 租户 ID。 例如：`72f988bf-86f1-41af-91ab-222222222222`。|
|**申请人/tokenIssuer** | OAuth 标记颁发者。 例如：`https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`。|
|**请求者/upn** | 请求者的用户主体名称（UPN）。 例如：`someone@contoso.com`。 |
|**请求者/用户名** | 此字段保留为仅供内部使用。|

### <a name="fields-that-describe-the-service"></a>描述服务的字段

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| 属性 | 说明 |
|:--- |:---|
|**accountName** | 存储帐户的名称。 例如：`mystorageaccount`。  |
|**requestUrl** | 请求的 URL。 例如：`http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`。|
|**userAgentHeader** | **用户代理标头**值，用引号引起来。 例如：`WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`。|
|**referrerHeader** | **引用**的标头值。 例如：`http://contoso.com/about.html`。|
|**ClientRequestId** | 请求的**x-客户端请求 id**标头值。 例如：`360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`。 |
|**etag** | 所返回对象的 ETag 标识符（用引号引起）。 例如：`0x8D101F7E4B662C4`。  |
|**Azurestoragelog.serverlatencyms** | 执行请求的操作所用的总时间（以毫秒为单位）。 此值不包括网络延迟（读取传入请求和向请求者发送响应的时间）。 例如：`22`。 |
|**serviceType** | 与此请求关联的服务。 例如：`blob`、`table`、`files` 或 `queue`。 |
|**operationCount** | 请求中涉及的每个记录的操作的数目。 此计数从的索引开始 `0` 。 某些请求需要多个操作，如复制 blob 的请求。 大多数请求仅执行一个操作。 例如：`1`。 |
|**requestHeaderSize** | 以字节表示的请求标头的大小。 例如：`578`。 <br>如果请求失败，此值可能为空。 |
|**requestBodySize** | 存储服务读取的请求数据包的大小（以字节为单位）。 <br> 例如：`0`。 <br>如果请求失败，此值可能为空。  |
|**responseHeaderSize** | 以字节表示的响应标头的大小。 例如：`216`。 <br>如果请求失败，此值可能为空。  |
|**responseBodySize** | 由存储服务写入的响应数据包的大小（字节）。 如果请求未成功，此值可为空。 例如：`216`。  |
|**requestMd5** | 请求中的**content-md5**标头或**x-ms-content-md5**标头的值。 此字段中指定的 MD5 哈希值表示请求中的内容。 例如：`788815fd0198be0d275ad329cafd1830`。 <br>此字段可为空。  |
|**serverMd5** | 存储服务计算的 MD5 哈希值。 例如：`3228b3cf1069a5489b298446321f8521`。 <br>此字段可为空。  |
|**lastModifiedTime** | 返回的对象的上次修改时间（LMT）。  例如：`Tuesday, 09-Aug-11 21:13:26 GMT`。 <br>对于可返回多个对象的操作，此字段为空。 |
|**conditionsUsed** | 表示条件的键/值对的分号分隔列表。 条件可以是以下任一情况： <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> 例如：`If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`。 |
|**contentLengthHeader** | 发送到存储服务的请求的内容长度标头值。 如果请求成功，则此值等于 requestBodySize。 如果请求不成功，则此值不能等于 requestBodySize，也可能为空。 |
|**tlsVersion** | 请求连接时使用的 TLS 版本。 例如：`TLS 1.2`。 |
|**smbTreeConnectID** | 服务器消息块（SMB） **treeConnectId**在树连接时建立。 例如： `0x3` |
|**smbPersistentHandleID** | 置网络重新连接的 SMB2 CREATE 请求中的持久句柄 ID。  在[SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中引用为**SMB2_FILEID。持久**的。 例如： `0x6003f` |
|**smbVolatileHandleID** | SMB2 创建请求中的可变句柄 ID 会在重新连接网络时重新连接。  在[SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 中引用为**SMB2_FILEID。Volatile**。 例如： `0xFFFFFFFF00000065` |
|**smbMessageID** | 连接相对**MessageId**。 例如： `0x3b165` |
|**smbCreditsConsumed** | 请求使用的入口或出口，单位为64k。 例如： `0x3` |
|**smbCommandDetail** | 有关此特定请求而不是常规请求类型的详细信息。 例如： `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | 与文件或目录关联的**FileId** 。  大致类似于 NTFS FileId。 例如： `0x9223442405598953` |
|**smbSessionID** | 在会话设置时建立的 SMB2 **SessionId** 。 例如： `0x8530280128000049` |
|**smbCommandMajor uint32** | **SMB2_HEADER**中的值。 目前，这是一个介于0到18之间的数字。 例如： `0x6` |
|**smbCommandMinor** | **SmbCommandMajor**的子类（如果适用）。 例如： `DirectoryCloseAndDelete` |

## <a name="see-also"></a>另请参阅

- 有关监视 Azure 存储的说明，请参阅[监视 Azure 存储](monitor-storage.md)。
- 有关监视 Azure 资源的详细信息，请参阅[通过 Azure Monitor 监视 Azure 资源](../../azure-monitor/insights/monitor-azure-resource.md)。
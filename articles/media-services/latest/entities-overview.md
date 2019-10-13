---
title: 媒体服务实体的筛选、排序和分页 - Azure | Microsoft Docs
description: 本文讨论 Azure 媒体服务实体的筛选、排序和分页。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ed509ac8fea43a9c011bbbf76c1dc433cd78d43c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298948"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>媒体服务实体的筛选、排序和分页

本主题讨论在列出 Azure 媒体服务 v3 实体时可用的 OData 查询选项和分页支持。

## <a name="considerations"></a>注意事项

* 属于日期/时间类型的实体的属性始终采用 UTC 格式。
* 发送请求之前，应对查询字符串中的空格进行 URL 编码。

## <a name="comparison-operators"></a>比较运算符

可以使用以下运算符将字段与常量值进行比较：

相等性运算符：

- `eq`：测试某个字段是否**等于**某个常量值
- `ne`：测试某个字段是否**不等于**某个常量值

范围运算符：

- `gt`：测试某个字段是否**大于**某个常量值
- `lt`：测试某个字段是否**小于**某个常量值
- `ge`：测试某个字段是否**大于或等于**某个常量值
- `le`：测试某个字段是否**小于或等于**某个常量值

## <a name="filter"></a>筛选器

**$filter**使用筛选器提供 OData 筛选器参数，以便仅查找你感兴趣的对象。

以下 REST 示例筛选资产的 alternateId：

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

以下C#示例筛选资产的创建日期：

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>排序依据

**$orderby** -用于按指定的参数对返回的对象进行排序。 例如：    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

若要按升序或降序对结果进行排序，请在字段名称后追加 `asc` 或 `desc`，并用空格分隔。 例如， `$orderby properties/created desc` 。

## <a name="skip-token"></a>跳过令牌

**$skiptoken** -如果查询响应包含多个项，则服务将返回一个用于获取下一页结果的跳过标记（`@odata.nextLink`）。 这可用于逐页浏览整个结果集。

在媒体服务 v3 中，无法配置页面大小。 页面大小因实体类型而异，请参阅以下各个部分，了解详细信息。

如果在逐页浏览集合时创建或删除实体，则会在返回的结果中反映此更改（如果这些更改位于集合中尚未下载的部分）。 

> [!TIP]
> 应始终使用 `nextLink` 来枚举集合，而不是依赖于特定的页大小。
>
> 如果有多个实体页，则将仅显示 `nextLink`。

考虑以下使用 $skiptoken 的示例。 请务必将 *amstestaccount* 替换为你的帐户名，并将 *api-version* 值设置为最新版本。

如果按如下所示请求列出资产：

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

将获得如下所示的响应：

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

然后通过发送 Get 请求来请求显示下一页：

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

下面C#的示例演示如何枚举帐户中的所有流式处理定位符。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>使用逻辑运算符合并查询选项

媒体服务 v3 支持 "or" 和 "and" 逻辑运算符。 

以下 REST 示例检查作业的状态：

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

构造相同的查询， C#如下所示： 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>实体的筛选和排序选项

下表显示了如何将筛选和排序选项应用于不同的实体：

|实体名称|属性名|筛选器|顺序|
|---|---|---|---|
|[资产](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`, `gt`, `lt`, `ge`, `le`|`asc` 和 `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`, `gt`, `lt`| `asc` 和 `desc`|
|[内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.description    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`||
||properties.lastModified|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.policyId|`eq`， `ne`||
|[作业](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` 和 `desc`|
||properties.state        | `eq`， `ne`        |                         |
||properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
||properties.lastModified | `gt`, `ge`, `lt`, `le` | `asc` 和 `desc`| 
|[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`，`ne`，`ge`，`le`，`gt`，`lt`|`asc` 和 `desc`|
||properties.endTime    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
|[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
|[可转换](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` 和 `desc`|
|| properties.created      | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|
|| properties.lastModified | `gt`, `ge`, `lt`, `le`| `asc` 和 `desc`|

## <a name="next-steps"></a>后续步骤

* [列出资产](https://docs.microsoft.com/rest/api/media/assets/list)
* [列出内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [列出作业](https://docs.microsoft.com/rest/api/media/jobs/list)
* [列出流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [列出流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [流式传输文件](stream-files-dotnet-quickstart.md)
* [配额和限制](limits-quotas-constraints.md)

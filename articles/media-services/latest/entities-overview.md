---
title: 媒体服务实体的筛选、排序和分页-Azure |Microsoft Docs
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
ms.openlocfilehash: d13ff3944e53f103c03a92e03d217b0066bc97df
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693310"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>媒体服务实体的筛选、排序和分页

本主题讨论在列出 Azure 媒体服务 v3 实体时可用的 OData 查询选项和分页支持。

## <a name="considerations"></a>注意事项

* @No__t_0 类型的实体的属性始终采用 UTC 格式。
* 发送请求之前，应对查询字符串中的空格进行 URL 编码。

## <a name="comparison-operators"></a>比较运算符

您可以使用下列运算符来比较字段和常量值：

相等运算符：

- `eq`：测试字段是否与常数值*相等*。
- `ne`：测试字段是否*不等于*常数值。

范围运算符：

- `gt`：测试字段是否*大于*常数值。
- `lt`：测试字段是否*小于*常数值。
- `ge`：测试字段是否*大于或等于*常数。 值
- `le`：测试字段是否*小于或等于*常量值。

## <a name="filter"></a>筛选

使用 `$filter` 提供 OData 筛选器参数，以便仅查找你感兴趣的对象。

以下 REST 示例筛选资产的 `alternateId` 值：

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

以下C#示例筛选资产的创建日期：

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>排序依据

使用 `$orderby` 按指定的参数对返回的对象进行排序。 例如：    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

若要按升序或降序对结果进行排序，请将 `asc` 或 `desc` 追加到字段名称中，用空格分隔。 例如：`$orderby properties/created desc`。

## <a name="skip-token"></a>跳过令牌

如果查询响应包含许多项，则服务将返回 `$skiptoken` （`@odata.nextLink`）值，该值用于获取下一页结果。 使用它可对整个结果集进行分页。

在媒体服务 v3 中，无法配置页面大小。 页大小因实体类型而异。 有关详细信息，请阅读以下各节。

如果在对集合进行分页时创建或删除实体，则所做的更改将反映在返回的结果中（如果这些更改位于尚未下载的集合的部分中）。 

> [!TIP]
> 应始终使用 `nextLink` 来枚举集合，而不是依赖于特定的页大小。
>
> 仅当存在多个实体页时，才会显示 `nextLink` 值。

请考虑以下使用 `$skiptoken` 的示例。 请务必将 *amstestaccount* 替换为你的帐户名，并将 *api-version* 值设置为最新版本。

如果你请求如下资产列表：

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

将返回类似于下面的响应：

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

媒体服务 v3 支持**或**和**和逻辑运算符**。 

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

|实体名称|属性名称|筛选|订单|
|---|---|---|---|
|[资产](https://docs.microsoft.com/rest/api/media/assets/)|name|`eq`、`gt`、`lt`、`ge`、`le`|`asc` 和 `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`、`gt`、`lt`| `asc` 和 `desc`|
|[内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.description    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`||
||properties.lastModified|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.policyId|`eq`，`ne`||
|[作业](https://docs.microsoft.com/rest/api/media/jobs)| name  | `eq`            | `asc` 和 `desc`|
||properties.state        | `eq`，`ne`        |                         |
||properties.created      | `gt`、`ge`、`lt`、`le`| `asc` 和 `desc`|
||properties.lastModified | `gt`、`ge`、`lt`、`le` | `asc` 和 `desc`| 
|[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.endTime    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
|[流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` 和 `desc`|
|[可转换](https://docs.microsoft.com/rest/api/media/transforms)| name | `eq`            | `asc` 和 `desc`|
|| properties.created      | `gt`、`ge`、`lt`、`le`| `asc` 和 `desc`|
|| properties.lastModified | `gt`、`ge`、`lt`、`le`| `asc` 和 `desc`|

## <a name="next-steps"></a>后续步骤

* [列出资产](https://docs.microsoft.com/rest/api/media/assets/list)
* [列出内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [列出作业](https://docs.microsoft.com/rest/api/media/jobs/list)
* [列出流式处理策略](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [列出流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [流式传输文件](stream-files-dotnet-quickstart.md)
* [配额和限制](limits-quotas-constraints.md)

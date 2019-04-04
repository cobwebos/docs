---
title: 使用 v3 Api-Azure 进行开发 |Microsoft Docs
description: 本文介绍了使用媒体服务 v3 进行开发时对实体和 Api 应用的规则。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a5ab0b25a2a2db764854982b1a6801ce4f857dda
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58891950"
---
# <a name="developing-with-media-services-v3-apis"></a>使用媒体服务 v3 Api 进行开发

本文介绍了使用媒体服务 v3 进行开发时对实体和 Api 应用的规则。

## <a name="naming-conventions"></a>命名约定

Azure 媒体服务 v3 资源名称（例如，资产、作业、转换）需遵循 Azure 资源管理器命名约束。 根据 Azure 资源管理器的要求，资源名称始终必须唯一。 因此，可以为资源名称使用任何唯一的标识符字符串（例如，GUID）。 

媒体服务资源名称不能包含“<”、“>”、“%”、“&”、“:”、“&#92;”、“?”、“/”、“*”、“+”、“.”、单引号或任何控制字符。 允许其他所有字符。 资源名称的最大长度为 260 个字符。 

有关 Azure 资源管理器命名的详细信息，请参阅：[命名需求](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)和[命名约定](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

## <a name="v3-api-design-principles"></a>v3 API 设计原则

V3 API 的主要设计原则之一是使 API 更安全。 v3 API 不在 **Get** 或 **List** 操作中返回机密或凭据。 在响应中，密钥始终为 null、空值或进行了净化。 你需要调用单独的操作方法来获取机密或凭据。 当某些 API 会检索/显示机密而另一些 API 不会这样做时，可以使用单独的操作设置不同的 RBAC 安全权限。 有关如何使用 RBAC 管理访问权限的信息，请参阅[使用 RBAC 管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)。

这样的示例包括：

* 未在 StreamingLocator Get 返回 ContentKey 值。
* 未在 ContentKeyPolicy Get 返回限制密钥。
* 未返回的作业的 HTTP 输入 Url 的 URL （若要删除签名） 的查询字符串部分。

请参阅[获取内容密钥策略 - .NET](get-content-key-policy-dotnet-howto.md) 示例。

## <a name="long-running-operations"></a>长时间运行的操作

操作标记为与`x-ms-long-running-operation`在 Azure 媒体服务[swagger 文件](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)是长时间运行的操作。 

有关如何跟踪异步 Azure 操作的详细信息，请参阅[异步操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)。

媒体服务具有以下长时间运行的操作：

* 创建 LiveEvent
* 更新 LiveEvent
* 删除 LiveEvent
* Start LiveEvent
* 停止 LiveEvent
* 重置 LiveEvent
* 创建 LiveOutput
* Delete LiveOutput
* 创建 StreamingEndpoint
* 更新 StreamingEndpoint
* 删除 StreamingEndpoint
* 启动 StreamingEndpoint
* 停止 StreamingEndpoint
* 缩放 StreamingEndpoint

## <a name="filtering-ordering-paging-of-media-services-entities"></a>媒体服务实体的筛选、排序和分页

媒体服务支持适用于媒体服务 v3 实体的以下 OData 查询选项： 

* $filter 
* $orderby 
* $top 
* $skiptoken 

运算符说明：

* Eq = 等于
* Ne = 不等于
* Ge = 大于或等于
* Le = 小于或等于
* Gt = 大于
* Lt = 小于

属于日期/时间类型的实体的属性始终采用 UTC 格式。

### <a name="page-results"></a>页面结果

如果查询响应包含许多项，则服务将返回一个“\@odata.nextLink”属性来获取下一页结果。 这可用于逐页浏览整个结果集。 无法配置页面大小。 页面大小因实体类型而异，请参阅以下各个部分，了解详细信息。

如果在逐页浏览集合时创建或删除实体，则会在返回的结果中反映此更改（如果这些更改位于集合中尚未下载的部分）。 

> [!TIP]
> 应始终使用下一个链接来枚举集合，而不依赖特定的页面大小。

### <a name="assets"></a>资产

#### <a name="filteringordering"></a>筛选/排序

下表显示了如何将筛选和排序选项应用于[资产](https://docs.microsoft.com/rest/api/media/assets)属性： 

|名称|筛选器|顺序|
|---|---|---|
|id|||
|名称|eq、gt、lt| 升序和降序|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq、gt、lt| 升序和降序|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

以下 C# 示例按创建日期筛选：

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

#### <a name="pagination"></a>分页 

已启用的四个排序顺序均支持分页。 页面大小当前为 1000。

##### <a name="c-example"></a>C# 示例

以下 C# 示例显示如何枚举帐户中的所有资产。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

##### <a name="rest-example"></a>REST 示例

考虑以下使用 $skiptoken 的示例。 请务必将 *amstestaccount* 替换为你的帐户名，并将 *api-version* 值设置为最新版本。

如果按如下所示请求列出资产：

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

将获得如下所示的响应：

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

然后通过发送 Get 请求来请求显示下一页：

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

有关更多 REST 示例，请参阅[资产 - 列出](https://docs.microsoft.com/rest/api/media/assets/list)

### <a name="content-key-policies"></a>内容密钥策略

#### <a name="filteringordering"></a>筛选/排序

下表显示了如何将这些选项应用于[内容密钥策略](https://docs.microsoft.com/rest/api/media/contentkeypolicies)属性： 

|名称|筛选器|顺序|
|---|---|---|
|id|||
|名称|eq、ne、ge、le、gt、lt|升序和降序|
|properties.created |eq、ne、ge、le、gt、lt|升序和降序|
|properties.description |eq、ne、ge、le、gt、lt||
|properties.lastModified|eq、ne、ge、le、gt、lt|升序和降序|
|properties.options |||
|properties.policyId|eq、ne||
|type|||

#### <a name="pagination"></a>分页

已启用的四个排序顺序均支持分页。 当前，页面大小为 10。

以下 C# 示例演示如何通过帐户中的所有内容密钥策略进行枚举。

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

有关 REST 示例，请参阅[内容密钥策略 - 列表](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)

### <a name="jobs"></a>作业

#### <a name="filteringordering"></a>筛选/排序

下表显示了如何将这些选项应用于[作业](https://docs.microsoft.com/rest/api/media/jobs)属性： 

| 名称    | 筛选器                        | 顺序 |
|---------|-------------------------------|-------|
| 名称                    | eq            | 升序和降序|
| properties.state        | eq、ne        |                         |
| properties.created      | gt、ge、lt、le| 升序和降序|
| properties.lastModified | gt、ge、lt、le | 升序和降序| 


#### <a name="pagination"></a>分页

媒体服务 v3 支持作业分页。

以下 C# 示例演示如何枚举帐户中的作业。

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

有关 REST 示例，请参阅[作业 - 列出](https://docs.microsoft.com/rest/api/media/jobs/list)

### <a name="streaming-locators"></a>流式处理定位符

#### <a name="filteringordering"></a>筛选/排序

下表显示这些选项如何应用于 StreamingLocator 属性： 

|名称|筛选器|顺序|
|---|---|---|
|id |||
|名称|eq、ne、ge、le、gt、lt|升序和降序|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq、ne、ge、le、gt、lt|升序和降序|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq、ne、ge、le、gt、lt|升序和降序|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

#### <a name="pagination"></a>分页

已启用的四个排序顺序均支持分页。 当前，页面大小为 10。

以下 C# 示例显示如何枚举帐户中的所有 StreamingLocator。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

若要查看 REST 示例，请参阅[流式处理定位符 - 列表](https://docs.microsoft.com/rest/api/media/streaminglocators/list)

### <a name="streaming-policies"></a>流式处理策略

#### <a name="filteringordering"></a>筛选/排序

下表显示了可以如何将这些选项应用于 StreamingPolicy 属性： 

|名称|筛选器|顺序|
|---|---|---|
|id|||
|名称|eq、ne、ge、le、gt、lt|升序和降序|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq、ne、ge、le、gt、lt|升序和降序|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

#### <a name="pagination"></a>分页

已启用的四个排序顺序均支持分页。 当前，页面大小为 10。

以下 C# 示例显示如何枚举帐户中的所有 StreamingPolicies。

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

有关 REST 示例，请参阅[流式处理策略 - 列表](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)


### <a name="transform"></a>转换

#### <a name="filteringordering"></a>筛选/排序

下表显示了如何将这些选项应用于[转换](https://docs.microsoft.com/rest/api/media/transforms)属性： 

| 名称    | 筛选器                        | 顺序 |
|---------|-------------------------------|-------|
| 名称                    | eq            | 升序和降序|
| properties.created      | gt、ge、lt、le| 升序和降序|
| properties.lastModified | gt、ge、lt、le | 升序和降序|

## <a name="next-steps"></a>后续步骤

[Stream 文件](stream-files-dotnet-quickstart.md)

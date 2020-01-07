---
title: 创建 RESTful 终结点
description: 本教程介绍如何创作自定义提供程序的 RESTful 终结点， 并详细说明如何处理受支持的 RESTful HTTP 方法的请求和响应。
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7f6c51211ce0572797ade659b9316003502da1f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75648724"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>创作自定义提供程序的 RESTful 终结点

自定义提供程序是 Azure 与终结点之间的协定。 使用自定义提供程序，可以在 Azure 上自定义工作流。 本教程介绍如何创作自定义提供程序的 RESTful 终结点。 如果不熟悉 Azure 自定义提供程序，请参阅[自定义资源提供程序概述](overview.md)。

> [!NOTE]
> 本教程在[设置用于 Azure 自定义提供程序的 Azure Functions](./tutorial-custom-providers-function-setup.md) 教程的基础上编写。 本教程中的某些步骤只有在已将某个 Azure 函数应用设置为与自定义提供程序配合使用的情况下才生效。

## <a name="work-with-custom-actions-and-custom-resources"></a>使用自定义操作和自定义资源

在本教程中，我们将更新该函数应用，使之充当自定义提供程序的 RESTful 终结点。 在 Azure 中，资源和操作按下述基本的 RESTful 规范建模：

- **PUT**：创建新资源
- **GET（实例）** ：检索现有资源
- **DELETE**：删除现有资源
- **POST**：触发一项操作
- **GET（集合）** ：列出所有现有资源

 在本教程中，我们使用 Azure 表存储， 但也可使用任何其他的数据库或存储服务。

## <a name="partition-custom-resources-in-storage"></a>在存储中将自定义资源分区

由于我们要创建 RESTful 服务，因此需存储创建的资源。 对于 Azure 表存储，我们需为数据生成分区和行键。 对于自定义提供程序，应针对自定义提供程序将数据分区。 将传入请求发送到自定义提供程序时，自定义提供程序会将 `x-ms-customproviders-requestpath` 标头添加到目标为终结点的传出请求。

以下示例显示自定义资源的一个 `x-ms-customproviders-requestpath` 标头：

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

可以根据示例的 `x-ms-customproviders-requestpath` 标头创建存储的 *partitionKey* 和 *rowKey* 参数，如下表所示：

参数 | 模板 | 说明
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | *partitionKey* 参数指定如何将数据分区。 通常根据自定义提供程序实例将数据分区。
*rowKey* | `{myResourceType}:{myResourceName}` | *rowKey* 参数指定数据的单个标识符。 通常情况下，此标识符是资源的名称。

我们还需要创建一个新类，以便为自定义资源建模。 在本教程中，我们将以下 **CustomResource** 类添加到函数应用：

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** 是一个简单的泛型类，接受任何输入数据。 它基于 **TableEntity**，后者用于存储数据。 **CustomResource** 类从 **TableEntity** 继承两个属性：**partitionKey** 和 **rowKey**。

## <a name="support-custom-provider-restful-methods"></a>支持自定义提供程序 RESTful 方法

> [!NOTE]
> 如果不是从本教程直接复制代码，则响应内容必须是将 `Content-Type` 标头设置为 `application/json` 的有效 JSON。

设置数据分区以后，即可创建基本的 CRUD 并触发自定义资源和自定义操作的方法。 由于自定义提供程序充当代理，因此 RESTful 终结点必须为请求和响应建模并对其进行处理。 以下代码片段演示了如何处理基本的 RESTful 操作。

### <a name="trigger-a-custom-action"></a>触发自定义操作

对于自定义提供程序，自定义操作通过 POST 请求触发。 自定义操作可以选择接受包含一组输入参数的请求正文。 该操作随后返回响应，指出操作的结果以及操作是成功还是失败。

请将以下 **TriggerCustomAction** 方法添加到函数应用：

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

**TriggerCustomAction** 方法接受传入请求并直接回显包含状态代码的响应。

### <a name="create-a-custom-resource"></a>创建自定义资源

对于自定义提供程序，自定义资源通过 PUT 请求创建。 自定义提供程序接受 JSON 请求正文，其中包含此自定义资源的一组属性。 Azure 中的资源遵循 RESTful 模型。 可以使用同一请求 URL 创建、检索或删除资源。

请添加以下 **CreateCustomResource** 方法来创建新资源：

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

**CreateCustomResource** 方法更新传入请求，使之包含特定于 Azure 的字段：**id**、**name** 和 **type**。 这些字段是供 Azure 中的服务使用的顶级属性。 自定义提供程序可以通过它们与其他服务（例如 Azure Policy、Azure 资源管理器模板、Azure 活动日志）互操作。

properties | 示例 | 说明
---|---|---
name  | {myCustomResourceName} | 自定义资源的名称
type  | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | resource-type 命名空间
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | 资源 ID

除了添加属性，我们还将 JSON 文档保存到了 Azure 表存储。

### <a name="retrieve-a-custom-resource"></a>检索自定义资源

对于自定义提供程序，自定义资源通过 GET 请求检索。 自定义提供程序不接受 JSON 请求正文。  对于 GET 请求，终结点使用 `x-ms-customproviders-requestpath` 标头返回已创建的资源。

请添加以下 **RetrieveCustomResource** 方法来检索现有资源：

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

在 Azure 中，资源遵循 RESTful 模型。 如果执行 GET 请求，则创建某项资源的请求 URL 也返回该资源。

### <a name="remove-a-custom-resource"></a>删除自定义资源

对于自定义提供程序，自定义资源通过 DELETE 请求删除。 自定义提供程序不接受 JSON 请求正文。  对于 DELETE 请求，终结点通过 `x-ms-customproviders-requestpath` 标头删除已创建的资源。

请添加以下 **RemoveCustomResource** 方法来删除现有资源：

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

在 Azure 中，资源遵循 RESTful 模型。 如果执行 DELETE 请求，则创建某项资源的请求 URL 也删除该资源。

### <a name="list-all-custom-resources"></a>列出所有自定义资源

对于自定义提供程序，可以通过 GET 请求的集合枚举一系列现有的自定义资源。 自定义提供程序不接受 JSON 请求正文。  对于 GET 请求的集合，终结点使用 `x-ms-customproviders-requestpath` 标头枚举已创建的资源。

请添加以下 **EnumerateAllCustomResources** 方法来枚举现有资源：

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey 的 QueryComparisons.GreaterThan 和 QueryComparisons.LessThan 是 Azure 表存储语法，用于执行针对字符串的“startswith”查询。

若要列出所有现有的资源，请生成一个 Azure 表存储查询，确保资源存在于自定义提供程序分区中。 然后，该查询检查行键是否以同一 `{myResourceType}` 值开头。

## <a name="integrate-restful-operations"></a>集成 RESTful 操作

将所有 RESTful 方法添加到函数应用以后，请更新主要的 **Run** 方法，以便调用函数来处理不同的 REST 请求：

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

更新的 **Run** 方法现在包含为 Azure 表存储添加的 *tableStorage* 输入绑定。 方法的第一部分读取 `x-ms-customproviders-requestpath` 标头并使用 `Microsoft.Azure.Management.ResourceManager.Fluent` 库将值作为资源 ID 分析。 `x-ms-customproviders-requestpath` 标头由自定义提供程序发送，用于指定传入请求的路径。

可以使用分析的资源 ID 生成数据的 **partitionKey** 和 **rowKey** 值，以便查找或存储自定义资源。

添加方法和类以后，需更新函数应用的 **using** 方法。 请将以下代码添加到 C# 文件顶部：

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

如果在本教程的任何地方遇到问题，可以在[自定义提供程序 C# RESTful 终结点参考](./reference-custom-providers-csharp-endpoint.md)中查找完整的代码示例。 完成函数应用后，请保存函数应用 URL。 它可以用来在后续教程中触发函数应用。

## <a name="next-steps"></a>后续步骤

在本文中，我们创作了一个适用于 Azure 自定义提供程序终结点的 RESTful 终结点。 若要了解如何创建自定义提供程序，请参阅[教程：创建自定义提供程序](./tutorial-custom-providers-create.md)一文。

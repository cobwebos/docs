---
title: 创作自定义提供程序的 RESTful 终结点
description: 本教程将讲述如何创作自定义提供程序的 RESTful 终结点， 并将详细说明如何处理受支持的 RESTful HTTP 方法的请求和响应。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799176"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>创作自定义提供程序的 RESTful 终结点

可以通过自定义提供程序自定义 Azure 上的工作流。 自定义提供程序是 Azure 与`endpoint`之间的协定。 本教程将详述创作自定义提供程序 RESTful `endpoint`的过程。 如果不熟悉 Azure 自定义提供程序，请参阅[自定义资源提供程序概述](./custom-providers-overview.md)。

本教程分为以下步骤：

- 使用自定义操作和自定义资源
- 如何在存储中将自定义资源分区
- 支持自定义提供程序 RESTful 方法
- 集成 RESTful 操作

本教程将以下述教程为基础：

- [Setup Azure Functions for Azure Custom Providers](./tutorial-custom-providers-function-setup.md)（为 Azure 自定义提供程序设置 Azure Functions）

> [!NOTE]
> 本教程以以前的教程为基础。 教程中的某些步骤只有在已将某个 Azure 函数设置为与自定义提供程序配合使用的情况下才生效。

## <a name="working-with-custom-actions-and-custom-resources"></a>使用自定义操作和自定义资源

在本教程中，我们将更新该函数，使之充当自定义提供程序的 RESTful 终结点。 在 Azure 中，资源和操作按基本的 RESTful 规范建模：PUT - 创建新资源；GET (实例) - 检索现有的资源；DELETE - 删除现有的资源；POST - 触发某个操作；GET (集合) - 列出所有现有的资源。 在本教程中，我们将使用 Azure 表作为存储，但也可使用任何其他的数据库或存储服务。

## <a name="how-to-partition-custom-resources-in-storage"></a>如何在存储中将自定义资源分区

由于我们要创建的是 RESTful 服务，因此需将创建的资源存储在存储中。 对于 Azure 表存储，我们需为数据生成分区和行键。 对于自定义提供程序，应针对自定义提供程序将数据分区。 将传入请求发送到自定义提供程序时，自定义提供程序会将 `x-ms-customproviders-requestpath` 标头添加到目标为`endpoint`的传出请求。

自定义资源的示例 `x-ms-customproviders-requestpath` 标头：

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

我们可以根据上面的示例 `x-ms-customproviders-requestpath` 标头创建存储的 partitionKey 和 rowKey，如下所示：

参数 | 模板 | 说明
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | partitionKey 指定如何将数据分区。 大多数情况下，应根据自定义提供程序实例将数据分区。
rowKey | '{myResourceType}:{myResourceName}' | rowKey 是数据的单个标识符。 大多数情况下，这是资源的名称。

另外，我们还需要创建一个新类，以便为自定义资源建模。 在本教程中，我们会为函数添加 `CustomResource` 类，该类是一个泛型类，接受任何输入的数据：

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

这样会创建一个基于 `TableEntity` 的基本类，用于存储数据。 `CustomResource` 类从 `TableEntity` 继承两个属性：partitionKey 和 rowKey。

## <a name="support-custom-provider-restful-methods"></a>支持自定义提供程序 RESTful 方法

> [!NOTE]
> 如果你不是从教程直接复制代码，则响应内容应该是有效的 JSON 并会将 `Content-Type` 标头设置为 `application/json`。

设置数据分区以后，即可定义基本的 CRUD 并触发自定义资源和自定义操作的方法。 由于自定义提供程序充当代理，因此必须通过 RESTful `endpoint`为请求和响应建模并对其进行处理。 按照以下代码片段来处理基本的 RESTful 操作：

### <a name="trigger-custom-action"></a>触发自定义操作

对于自定义提供程序，自定义操作是通过 `POST` 请求触发的。 自定义操作可以选择接受包含一组输入参数的请求正文。 该操作随后应返回响应，指出操作的结果以及操作是成功还是失败。 在本教程中，我们将为函数添加 `TriggerCustomAction` 方法：

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
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

`TriggerCustomAction` 方法接受传入请求并直接回显包含成功状态代码的响应。 

### <a name="create-custom-resource"></a>创建自定义资源

对于自定义提供程序，自定义资源是通过 `PUT` 请求创建的。 自定义提供程序会接受 JSON 请求正文，其中包含此自定义资源的一组属性。 在 Azure 中，资源遵循 RESTful 模型。 用于创建某个资源的同一请求 URL 也应该能够检索并删除该资源。 在本教程中，我们将添加用于创建新资源的 `CreateCustomResource` 方法：

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
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

`CreateCustomResource` 方法更新传入请求，使之包含特定于 Azure 的字段：`id`、`name` 和 `type`。 这些是可供 Azure 中的服务使用的顶级属性。 自定义提供程序可以通过它们集成其他服务，例如 Azure Policy、Azure 资源管理器模板、Azure 活动日志。

属性 | 示例 | 说明
---|---|---
名称 | '{myCustomResourceName}' | 自定义资源的名称。
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | 资源类型命名空间。
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | 资源 ID。

除了添加属性，我们还将文档保存到 Azure 表存储。 

### <a name="retrieve-custom-resource"></a>检索自定义资源

对于自定义提供程序，自定义资源是通过 `GET` 请求检索的。 自定义提供程序不会接受 JSON 请求正文。  在使用 `GET` 请求时，**终结点**应使用 `x-ms-customproviders-requestpath` 标头返回已创建的资源。 在本教程中，我们将添加用于检索现有资源的 `RetrieveCustomResource` 方法：

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
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

在 Azure 中，资源应遵循 RESTful 模型。 如果执行了 `GET` 请求，则创建了资源的请求 URL 也应返回资源。

### <a name="remove-custom-resource"></a>删除自定义资源

对于自定义提供程序，自定义资源是通过 `DELETE` 请求删除的。 自定义提供程序不会接受 JSON 请求正文。  在使用 `DELETE` 请求时，**终结点**应使用 `x-ms-customproviders-requestpath` 标头删除已创建的资源。 在本教程中，我们将添加用于删除现有资源的 `RemoveCustomResource` 方法：

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
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

在 Azure 中，资源应遵循 RESTful 模型。 如果执行了 `DELETE` 请求，则创建了资源的请求 URL 也应删除资源。

### <a name="list-all-custom-resources"></a>列出所有自定义资源

对于自定义提供程序，可以通过集合 `GET` 请求枚举一系列现有的自定义资源。 自定义提供程序不会接受 JSON 请求正文。  在使用集合 `GET` 请求时，`endpoint`应使用 `x-ms-customproviders-requestpath` 标头枚举已创建的资源。 在本教程中，我们将添加用于枚举现有资源的 `EnumerateAllCustomResources` 方法。

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
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
> 行键的 GreaterThan 和 LessThan 是 Azure 表语法，用于执行针对字符串的“startswith”查询。 

为了列出所有现有的资源，我们生成一个 Azure 表查询，确保资源存在于自定义提供程序分区中。 然后，该查询检查行键是否以同一 `{myResourceType}` 开头。

## <a name="integrate-restful-operations"></a>集成 RESTful 操作

将所有 RESTful 方法添加到该函数以后，我们可以更新主要的 `Run` 方法，以便调用函数来处理不同的 REST 请求：

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
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
        // Action request for an custom action.
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

更新的 `Run` 方法现在将包含为 Azure 表存储添加的 `tableStorage` 输入绑定。 方法的第一部分现在将读取 `x-ms-customproviders-requestpath` 标头并使用 `Microsoft.Azure.Management.ResourceManager.Fluent` 库将值作为资源 ID 分析。 `x-ms-customproviders-requestpath` 标头由自定义提供程序发送，用于指定传入请求的路径。 我们现在可以使用分析的资源 ID 生成数据的 partitionKey 和 rowKey，以便查找或存储自定义资源。

除了添加方法和类，我们还需要更新函数的 using 方法。 在文件的顶部，添加以下内容：

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

如果在本教程的任何地方遇到问题，可以在[自定义提供程序 C# RESTful 终结点参考](./reference-custom-providers-csharp-endpoint.md)中查找完整的代码示例。 函数完成以后，请保存可以用来触发函数的函数 URL，以便在后续教程中使用。

## <a name="next-steps"></a>后续步骤

在本文中，我们创作了一个适用于 Azure 自定义提供程序`endpoint`的 RESTful 终结点。 若要了解如何创建自定义提供程序，请继续学习下一篇文章。

- [教程：创建自定义提供程序](./tutorial-custom-providers-create.md)

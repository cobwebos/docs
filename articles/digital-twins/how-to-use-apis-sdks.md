---
title: 使用 Azure 数字孪生 API 和 SDK
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure 数字孪生 Api，包括 via SDK。
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 39dd9604cf0e58eda94acf6528ab31eca26355d0
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936769"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>使用 Azure 数字孪生 API 和 SDK

Azure 数字孪生附带了 **控制平面 api** 和 **数据平面 api** ，用于管理实例及其元素。 本文概述了可用的 Api 以及与它们进行交互的方法。 可以直接将 REST Api 与关联的 Swagger 或 SDK 一起使用。

## <a name="overview-control-plane-apis"></a>概述：控制平面 Api

控制平面 Api 用于作为一个整体管理 Azure 数字孪生实例，因此它们涵盖了创建或删除整个实例等操作。 你还将使用这些终结点来创建和删除终结点。

公共预览版的最新控制面 API 版本为 _**2020-03-01-preview**_。

使用控制平面 Api：
* 可以通过引用最新的 [Swagger 文件夹](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins)直接调用 api。 此存储库还包含演示使用情况的示例文件夹。
* 当前可在中访问控件 Api 的 Sdk .。。
  - [.Net (c # ) ](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([源](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))  ([引用 [自动生成]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview)) 
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([源](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))  ([参考 [自动生成]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview)) 
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([源](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins)) 
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([源](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins)) 
  - [源](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

还可以通过 [Azure 门户](https://portal.azure.com) 和 [CLI](how-to-use-cli.md)与 Azure 数字孪生交互，来运动控制平面 api。

## <a name="overview-data-plane-apis"></a>概述：数据平面 Api

数据平面 Api 用于管理 Azure 数字孪生实例中的元素。 其中包括创建路由、上传模型、创建关系和管理孪生等操作。 它们可以大致分为以下几类：
* **DigitalTwinsModels** -DigitalTwinsModels 类别包含用于管理 Azure 数字孪生实例中的 [模型](concepts-models.md) 的 api。 管理活动包括上载、验证、检索和删除在 DTDL 中创作的模型。
* **DigitalTwins** -DigitalTwins 类别包含允许开发人员在 Azure 数字孪生实例中创建、修改和删除 [数字孪生](concepts-twins-graph.md) 及其关系的 api。
* **查询** -查询类别允许开发人员 [查找跨关系图形的数字孪生集](how-to-query-graph.md) 。
* **EventRoutes** -EventRoutes 类别包含用于 [路由数据](concepts-route-events.md)的 api，通过系统和下游服务。

公共预览版的最新数据平面 API 版本为 _**2020-05-31-preview**_。 用于数据平面操作的 _2020-03-01-preview_ API 版本现已弃用。

使用数据平面 Api：
* 可以通过 ... 直接调用 Api
   - 引用最新的 [Swagger 文件夹](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)。 此存储库还包含演示使用情况的示例文件夹。 
   - 查看 [API 参考文档](https://docs.microsoft.com/rest/api/azure-digitaltwins/)。
* 可以使用 .NET (c # ) SDK。 目前，这是用于与这些 Api 进行交互的唯一发布的 SDK。 使用 .NET SDK .。。
   - 可以在 NuGet 上查看包： [DigitalTwins](https://www.nuget.org/packages/Azure.DigitalTwins.Core)。 
   - 可以在 GitHub 中找到 SDK 源（包括示例的文件夹）：适用于 [.net 的 Azure IoT 数字孪生客户端库](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)。 
   - 您可以查看 [SDK 参考文档](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview)。
   - 若要查看详细信息和用法示例，请继续阅读本文的 [.net (c # ) SDK (数据平面) ](#net-c-sdk-data-plane) 部分。
* 可以使用 AutoRest 为其他语言生成 SDK。 按照 [*如何：创建适用于 Azure 数字孪生的自定义 Sdk AutoRest*](how-to-create-custom-sdks.md)中的说明进行操作。

还可以通过 [CLI](how-to-use-cli.md)与 Azure 数字孪生交互，来运用日期平面 api。

## <a name="net-c-sdk-data-plane"></a>.NET (c # ) SDK (数据平面) 

Azure 数字孪生 .NET (c # ) SDK 是用于 .NET 的 Azure SDK 的一部分。 它是开放源代码，基于 Azure 数字孪生数据平面 Api。

> [!NOTE]
> 有关 SDK 设计的详细信息，请参阅 [Azure sdk 的常规设计原则](https://azure.github.io/azure-sdk/general_introduction.html) 和特定 [.net 设计准则](https://azure.github.io/azure-sdk/dotnet_introduction.html)。

若要使用 SDK，请在项目中包括 NuGet 包 **DigitalTwins** 。 还需要最新版本的 **Azure. Identity** 包。

* 在 Visual Studio 中，可以使用 NuGet 包管理器添加包， (通过 *> Nuget 包管理器 > 管理解决方案) 的 Nuget 包* 。 
* 使用 .NET 命令行工具，你可以运行：

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

有关如何在实践中使用 Api 的详细演练，请参阅 [*教程：编写客户端应用代码*](tutorial-code.md)。 

### <a name="net-sdk-usage-examples"></a>.NET SDK 使用示例

下面是演示如何使用 .NET SDK 的一些代码示例。

对服务进行身份验证：

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

上传模型和列表模型：

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

创建和查询孪生：

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

有关此示例应用代码的演练，请参阅 [*教程：为客户端应用编写代码*](tutorial-code.md) 。 

你还可以在 [适用于 .net (c # ) SDK 的 GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)存储库中找到其他示例。

#### <a name="serialization-helpers"></a>序列化帮助器

序列化帮助程序是 SDK 内可用的帮助程序函数，用于快速创建或反序列化用于访问基本信息的数据。 由于核心 SDK 方法默认返回的是以 JSON 形式返回的数据，因此，使用这些帮助器类进一步打破了克隆数据会很有帮助。

可用的帮助程序类包括：
* `BasicDigitalTwin`：表示数字输出的核心数据
* `BasicRelationship`：表示关系的核心数据
* `UpdateOperationUtility`：表示更新调用中使用的 JSON 修补程序信息
* `WriteableProperty`：表示属性元数据

##### <a name="deserialize-a-digital-twin"></a>反序列化数字输出

始终可以使用所选的 JSON 库（如或）反序列化 `System.Test.Json` 数据 `Newtonsoft.Json` 。 对于对某个对等的基本访问，帮助器类使其更方便一些。

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

使用 `BasicDigitalTwin` helper 类，还可以通过访问在克隆上定义的属性 `Dictionary<string, object>` 。 若要列出克隆的属性，可以使用：

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>创建数字输出

使用 `BasicDigitalTwin` 类，您可以准备用于创建克隆实例的数据：

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

上面的代码等效于以下 "手动" 变体：

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>反序列化关系

始终可以使用所选的 JSON 库（如或）对关系数据进行反序列化 `System.Test.Json` `Newtonsoft.Json` 。 对于对关系的基本访问，帮助器类使其更方便一些。

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

`BasicRelationship`通过帮助器类，还可以通过实现对关系定义的属性的访问 `Dictionary<string, object>` 。 若要列出属性，你可以使用：

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>创建关系

通过使用 `BasicRelationship` 类，您还可以准备用于在克隆实例上创建关系的数据：

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>创建用于克隆更新的修补程序

针对孪生和关系的更新调用使用 [JSON 修补程序](http://jsonpatch.com/) 结构。 若要创建 JSON 修补程序操作的列表，可以使用 `UpdateOperationsUtility` 类，如下所示。

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>常规 API/SDK 使用说明

> [!NOTE]
> 请注意，在预览期间，Azure 数字孪生不支持 ** (CORS) 的跨域资源共享 **。 因此，如果从浏览器应用中调用 REST API， [API 管理 (APIM) ](../api-management/api-management-key-concepts.md) 接口或 [Power Apps](https://docs.microsoft.com/powerapps/powerapps-overview) 连接器，则可能会出现策略错误。
> 若要解决此错误，可以执行以下操作之一：
> * 从消息中去除 CORS 标头 `Access-Control-Allow-Origin` 。 此标头指示是否可以共享响应。 
> * 或者，创建 CORS 代理，并通过它 REST API 请求来请求 Azure 数字孪生。 

下面的列表提供了有关使用 Api 和 Sdk 的其他详细信息和一般准则。

* 若要使用 SDK，请实例化 `DigitalTwinsClient` 类。 构造函数需要可使用包中各种身份验证方法获取的凭据 `Azure.Identity` 。 有关的详细信息 `Azure.Identity` ，请参阅其 [命名空间文档](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet)。 
* 你可能会发现在入门 `InteractiveBrowserCredential` 时非常有用，但还有其他几个选项，包括 [托管标识](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)的凭据，你可能会使用这些选项来验证使用 MSI 对 azure 数字孪生 [设置的 azure 功能](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) 。 有关的详细信息 `InteractiveBrowserCredential` ，请参阅它的 [类文档](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)。
* 所有服务 API 调用都公开为类的成员函数 `DigitalTwinsClient` 。
* 所有服务函数都存在于同步和异步版本中。
* 所有服务函数对于400或更高版本的返回状态均引发异常。 请确保将调用包装到 `try` 部分，并至少捕获 `RequestFailedExceptions` 。 有关此类异常的详细信息，请参阅 [此处](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet)。
* 大多数服务方法 `Response<T>` `Task<Response<T>>` 为异步调用) 返回或 (，其中 `T` 是服务调用的返回对象的类。 [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet)类封装服务返回并在其字段中显示返回值 `Value` 。  
* 包含分页结果的服务方法返回 `Pageable<T>` 或 `AsyncPageable<T>` 结果。 有关类的详细信息 `Pageable<T>` ，请参阅 [此处](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview); 有关的详细信息 `AsyncPageable<T>` ，请参阅 [此处](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview)。
* 您可以使用循环来循环访问分页 `await foreach` 的结果。 有关此过程的详细信息，请参阅 [此处](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)。
* 基础 SDK 是 `Azure.Core` 。 请参阅 [Azure 命名空间文档](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview) ，了解 SDK 基础结构和类型。

服务方法尽可能返回强类型对象。 但是，因为 Azure 数字孪生基于) 用户在运行 (时由用户通过上传到服务的已配置的模型，因此，许多服务 Api 采用 JSON 格式获取并返回静态数据。

## <a name="monitor-api-metrics"></a>监视 API 指标

可以在 [Azure 门户](https://portal.azure.com/)中查看 API 指标，例如请求、延迟和失败率。 

在门户主页上，搜索 Azure 数字孪生实例以提取其详细信息。 从 Azure 数字孪生实例的菜单中选择 " **指标** " 选项，打开 " *指标* " 页。

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="显示 Azure 数字孪生的 "指标" 页的屏幕截图":::

在此处，你可以查看实例的度量值并创建自定义视图。

## <a name="next-steps"></a>后续步骤

请参阅如何使用 Api 设置 Azure 数字孪生实例和身份验证：
* [*操作说明：设置实例和身份验证*](how-to-set-up-instance-scripted.md)

或者，逐步完成创建客户端应用程序的步骤，如下所述：
* [*教程：* 为客户端应用编写代码](tutorial-code.md)

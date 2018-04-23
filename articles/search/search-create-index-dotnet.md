---
title: 创建索引（.NET API - Azure 搜索）| Microsoft Docs
description: 使用 Azure 搜索 .NET SDK 在代码中创建索引。
author: brjohnstmsft
manager: jlembicz
tags: azure-portal
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/22/2017
ms.author: brjohnst
ms.openlocfilehash: 429613b7699ada732c56b642141ff8c4505cbc33
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>使用 .NET SDK 创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-what-is-an-index.md)
> * [门户](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

本文介绍如何使用 [Azure 搜索 .NET SDK](https://aka.ms/search-sdk) 创建 Azure 搜索[索引](https://docs.microsoft.com/rest/api/searchservice/Create-Index)。

按照本指南创建索引之前，应已经 [创建 Azure 搜索服务](search-create-service-portal.md)。

> [!NOTE]
> 本文中的所有示例代码均用 C# 编写。 可以 [在 GitHub 上](http://aka.ms/search-dotnet-howto)找到完整的源代码。 也可参阅 [Azure 搜索 .NET SDK](search-howto-dotnet-sdk.md)，以便更详细地了解示例代码。


## <a name="identify-your-azure-search-services-admin-api-key"></a>确定 Azure 搜索服务的管理 API 密钥
现在，已预配 Azure 搜索服务，差不多可以使用 .NET SDK 向服务终结点发出请求了。 首先，需要获取已为预配的搜索服务生成的其中一个管理 API 密钥。 每次向服务发出请求时，NET SDK 都会发送这个 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

1. 若要查找服务的 API 密钥，请登录到 [Azure 门户](https://portal.azure.com/)
2. 转到 Azure 搜索服务的边栏选项卡
3. 单击“密钥”图标

服务将具有*管理密钥*和*查询密钥*。

* 主管理密钥和辅助 *管理密钥* 授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。 有两个密钥的作用是确保在决定重新生成主密钥时可以继续使用辅助密钥，反之亦然。
* *查询密钥* 授予对索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。

可以使用主管理密钥或辅助管理密钥来创建索引。

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>创建 SearchServiceClient 类的实例
若要开始使用 Azure 搜索 .NET SDK，需要创建 `SearchServiceClient` 类的实例。 此类具有几个构造函数。 需要将搜索服务名称和 `SearchCredentials` 对象用作参数。 `SearchCredentials` 包装 API 密钥。

以下代码使用应用程序配置文件（在使用[示例应用程序](http://aka.ms/search-dotnet-howto)时为 `appsettings.json`）中存储的搜索服务名称和 API 密钥的值创建一个新 `SearchServiceClient`：

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` 具有 `Indexes` 属性。 此属性提供创建、列出、更新或删除 Azure 搜索索引所需的所有方法。

> [!NOTE]
> `SearchServiceClient` 类管理与搜索服务的连接。 为了避免打开太多连接，应尝试在应用程序中共享 `SearchServiceClient` 的单个实例（如果可能）。 它的方法在启用此类共享时是线程安全的。
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>定义 Azure 搜索索引
只需调用一次 `Indexes.Create` 方法即可创建索引。 此方法以定义 Azure 搜索索引的 `Index` 对象作为参数。 需要创建 `Index` 对象，并对其进行初始化，如下所示：

1. 将 `Index` 对象的 `Name` 属性设置为索引的名称。
2. 将 `Index` 对象的 `Fields` 属性设置为 `Field` 对象的数组。 若要创建 `Field` 对象，最简单的方法是调用 `FieldBuilder.BuildForType`，为类型参数传递模型类。 模型类具有映射到索引的字段属性。 因此，可将来自搜索索引的文档绑定到模型类的实例。

> [!NOTE]
> 如果不打算使用模型类，仍可通过直接创建 `Field` 对象来定义索引。 可以向构造函数提供字段的名称以及数据类型（或分析器（用于字符串字段））。 还可以设置其他属性，如 `IsSearchable`、`IsFilterable` 等。
>
>

设计索引时，必须牢记搜索用户体验和业务需求，因为必须为每个字段分配[适当的属性](https://docs.microsoft.com/rest/api/searchservice/Create-Index)。 这些属性控制哪些搜索功能（筛选、分面、排序、全文搜索等）应用于哪些字段。 对于未显式设置的任何属性，`Field` 类默认禁用相应的搜索功能，除非用户专门启用它。

此示例中，索引命名为“hotels”，并使用模型类来定义字段。 模型类的每个属性都具有一些特性，这些特性决定了相应索引字段的与搜索相关的行为。 模型类定义如下：

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

根据每个字段在应用程序中的预期使用方式，谨慎选择每个属性的特性。 例如，搜索酒店的人员很可能对匹配 `description` 字段的关键字感兴趣，因此通过将 `IsSearchable` 特性添加到 `Description` 属性来为该字段启用全文搜索。

请注意，必须通过添加 `Key` 特性将索引中类型为 `string` 的一个字段指定为“key”字段（请参见上例中的 `HotelId`）。

上述索引定义对 `description_fr` 字段使用了语言分析器，因为它用于存储法语文本。 请参阅[语言支持主题](https://docs.microsoft.com/rest/api/searchservice/Language-support)以及相应的[博客文章](https://azure.microsoft.com/blog/language-support-in-azure-search/)，了解有关语言分析器的详细信息。

> [!NOTE]
> 默认情况下，模型类中的每个属性名称被用作索引中的对应字段的名称。 要将所有属性名映射到 camel 形式的字段名称，请使用 `SerializePropertyNamesAsCamelCase` 特性标记类。 若要映射到不同的名称，可以使用 `JsonProperty` 属性，如上面的 `DescriptionFr` 属性一样。 `JsonProperty` 特性优先于 `SerializePropertyNamesAsCamelCase` 特性。
> 
> 

定义了模型类后，现在便可轻松创建索引定义：

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="create-the-index"></a>创建索引
获得已初始化的 `Index` 对象后，只需对 `SearchServiceClient` 对象调用 `Indexes.Create` 即可创建索引：

```csharp
serviceClient.Indexes.Create(definition);
```

对于成功的请求，该方法将正常返回。 如果请求有问题（例如，参数无效），该方法将引发 `CloudException`。

创建完索引并想要删除它时，只需对 `SearchServiceClient` 调用 `Indexes.Delete` 方法。 例如，以下是删除“hotels”索引的方法：

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> 为简单起见，本文中的示例代码使用 Azure 搜索 .NET SDK 的同步方法。 建议用户在自己的应用程序中使用异步方法，使应用程序保持可缩放且响应迅速。 例如，在上面的示例中可以使用 `CreateAsync` 和 `DeleteAsync`，而不是 `Create` 和 `Delete`。
> 
> 

## <a name="next-steps"></a>后续步骤
创建 Azure 搜索索引后，就可以 [将内容上传到索引中](search-what-is-data-import.md)，以便可以开始搜索数据。


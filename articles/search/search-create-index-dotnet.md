---
title: "使用 .NET SDK 创建 Azure 搜索索引 | Microsoft Docs"
description: "使用 Azure 搜索 .NET SDK 在代码中创建索引。"
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 87757a16f1fa31be97f6f8a0e39c6adbf2513828


---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>使用 .NET SDK 创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-what-is-an-index.md)
> * [门户](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

本文介绍如何使用 [Azure 搜索 .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) 创建 Azure 搜索[索引](https://msdn.microsoft.com/library/azure/dn798941.aspx)。

按照本指南创建索引之前，应已经 [创建 Azure 搜索服务](search-create-service-portal.md)。

请注意，本文中的所有示例代码均用 C# 编写。 可以在 [GitHub](http://aka.ms/search-dotnet-howto)上找到完整的源代码。

## <a name="i-identify-your-azure-search-services-admin-apikey"></a>I. 确定 Azure 搜索服务的管理 API 密钥
现在，已预配 Azure 搜索服务，差不多可以使用 .NET SDK 向服务终结点发出请求了。 首先，需要获取已为预配的搜索服务生成的其中一个管理 API 密钥。 每次向服务发出请求时，NET SDK 都会发送这个 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

1. 若要查找服务的 API 密钥，必须登录到 [Azure 门户](https://portal.azure.com/)
2. 转到 Azure 搜索服务的边栏选项卡
3. 单击“密钥”图标

服务将具有*管理密钥*和*查询密钥*。

* 主管理密钥和辅助 *管理密钥* 授予所有操作的完全控制权限，包括管理服务以及创建和删除索引、索引器与数据源的能力。 有两个密钥的作用是确保在决定重新生成主密钥时可以继续使用辅助密钥，反之亦然。
* *查询密钥* 授予对索引和文档的只读访问权限，通常分发给发出搜索请求的客户端应用程序。

可以使用主管理密钥或辅助管理密钥来创建索引。

<a name="CreateSearchServiceClient"></a>

## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. 创建 SearchServiceClient 类的实例
若要开始使用 Azure 搜索 .NET SDK，需要创建 `SearchServiceClient` 类的实例。 此类具有几个构造函数。 需要将搜索服务名称和 `SearchCredentials` 对象用作参数。 `SearchCredentials` 包装 API 密钥。

以下代码使用应用程序配置文件（`app.config` 或 `web.config`）中存储的搜索服务名称和 API 密钥的值创建一个新 `SearchServiceClient`：

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` 具有 `Indexes` 属性。 此属性提供创建、列出、更新或删除 Azure 搜索索引所需的所有方法。

> [!NOTE]
> `SearchServiceClient` 类管理与搜索服务的连接。 为了避免打开太多连接，应尝试在应用程序中共享 `SearchServiceClient` 的单个实例（如果可能）。 它的方法在启用此类共享时是线程安全的。
> 
> 

<a name="DefineIndex"></a>

## <a name="iii-define-your-azure-search-index-using-the-index-class"></a>III. 使用 `Index` 类定义 Azure 搜索索引
只需调用一次 `Indexes.Create` 方法即可创建索引。 此方法以定义 Azure 搜索索引的 `Index` 对象作为参数。 需要创建 `Index` 对象，并对其进行初始化，如下所示：

1. 将 `Index` 对象的 `Name` 属性设置为索引的名称。
2. 将 `Index` 对象的 `Fields` 属性设置为 `Field` 对象的数组。 每个 `Field` 对象定义索引中的一个字段的行为。 可以向构造函数提供字段的名称以及数据类型（或分析器（用于字符串字段））。 还可以设置其他属性，如 `IsSearchable`、`IsFilterable` 等。

设计索引时，必须牢记搜索用户体验和业务需求，因为必须为每个 `Field` 分配[适当的属性](https://msdn.microsoft.com/library/azure/dn798941.aspx)。 这些属性控制哪些搜索功能（筛选、分面、排序、全文搜索等）应用于哪些字段。 对于未显式设置的任何属性，`Field` 类默认禁用相应的搜索功能，除非用户专门启用它。

在此示例中，已将索引命名为“hotels”并定义了字段，如下所示：

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

已根据所认为的每个 `Field` 在应用程序中使用的方式，谨慎地为这些字段选择索引属性。 例如，搜索酒店的人员很可能对匹配 `description` 字段的关键字感兴趣，因此我们通过将 `IsSearchable` 设置为 `true` 来为该字段启用全文搜索。

请注意，必须通过将 `IsKey` 设为 `true` 将索引中类型为 `DataType.String` 的仅一个字段指定为 *key* 字段（请参见上例中的 `hotelId`）。

上述索引定义对 `description_fr` 字段使用了自定义语言分析器，因为它用于存储法语文本。 请参阅 [MSDN 上的语言支持](https://msdn.microsoft.com/library/azure/dn879793.aspx)主题以及相应的[博客文章](https://azure.microsoft.com/blog/language-support-in-azure-search/)，了解有关语言分析器的详细信息。

> [!NOTE]
> 请注意，通过将 `AnalyzerName.FrLucene` 传入构造函数，`Field` 将自动为 `DataType.String` 类型，并且 `IsSearchable` 将设置为 `true`。
> 
> 

## <a name="iv-create-the-index"></a>IV. 创建索引
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

## <a name="next"></a>下一步
创建 Azure 搜索索引后，就可以 [将内容上载到索引中](search-what-is-data-import.md) ，以便可以开始搜索数据。




<!--HONumber=Nov16_HO2-->



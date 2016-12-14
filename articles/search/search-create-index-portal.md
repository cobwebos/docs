---
title: "使用 Azure 门户创建 Azure 搜索索引 | Microsoft Docs"
description: "使用 Azure 门户创建索引。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c54d8787-6dae-4943-85ed-c8928d2a5caf
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f0d468d0cb2cf76bb90e73d3fef3f6a8c14d1850


---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>使用 Azure 门户创建 Azure 搜索索引
> [!div class="op_single_selector"]
> * [概述](search-what-is-an-index.md)
> * [门户](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

本文介绍了使用 Azure 门户创建 Azure 搜索[索引](search-what-is-an-index.md)的过程。

按照本指南创建索引之前，应已经 [创建 Azure 搜索服务](search-create-service-portal.md)。

## <a name="i-go-to-your-azure-search-blade"></a>I. 转到“Azure 搜索”边栏选项卡
1. 在 [Azure 门户](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. 选择你的 Azure 搜索服务

## <a name="ii-add-and-name-your-index"></a>II. 添加并命名索引
1. 单击“添加索引”按钮
2. 命名 Azure 搜索索引。 由于我们要在本指南中创建一个用于搜索酒店的索引，因此我们将索引命名为“酒店”。
   * 索引名称必须以字母开头，并且只能包含小写字母、数字或短划线（“-”）。
   * 与服务名称类似，你选取的索引名称也将是终结点 URL 的一部分，你将在其中发送 Azure 搜索 API 的 HTTP 请求
3. 单击“字段”条目打开新边栏选项卡

![](./media/search-create-index-portal/add-index.png)

## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. 创建并定义你的索引的字段
1. 通过选择“字段”条目，将打开一个新边栏选项卡，带有可输入你的索引定义的窗体。
2. 使用窗体将字段添加到你的索引。
   
   * 为每个 Azure 搜索索引强制使用类型 Edm.String 的*密钥*字段。 默认情况下，使用字段名称“id”创建此密钥字段。 在我们的索引中，已将此名称更改为“hotelId”。
   * 索引架构的某些属性只能设置一次，以后不能更新。 因此，完成初始配置后，当前不支持任何需要重新编制索引的架构更新（如更改字段类型）。
   * 已根据所认为的每个字段在应用程序中使用的方式，谨慎地为这些字段选择属性值。 设计索引时，必须牢记搜索用户体验和业务需求，因为必须为每个字段分配[适当的属性](https://msdn.microsoft.com/library/azure/dn798941.aspx)。 这些属性控制哪些搜索功能（筛选、分面、排序、全文搜索等）应用于哪些字段。 例如，搜索酒店的人员很可能对匹配“描述”字段的关键字感兴趣，因此我们通过设置“Searchable”属性为该字段启用全文搜索。
     * 还可以通过单击边栏选项卡顶部的“分析器”选项卡，为每个字段设置“语言分析器”[](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx)。 如下所示，我们在适用于法语文本的索引中为字段选择了法语分析器。
3. 单击“字段”边栏选项卡上的“确定”，确认你的字段定义
4. 单击“添加索引”边栏选项卡上的“确定”，保存和创建你刚定义的索引。

在下面的屏幕截图中，你可以看到我们为“酒店”索引命名和定义字段的方式。

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>下一步
创建 Azure 搜索索引后，就可以 [将内容上载到索引中](search-what-is-data-import.md) ，以便可以开始搜索数据。




<!--HONumber=Nov16_HO3-->



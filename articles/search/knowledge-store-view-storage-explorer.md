---
title: 使用存储资源管理器查看知识 store （预览版）
titleSuffix: Azure Cognitive Search
description: 使用 Azure 门户的存储资源管理器查看和分析 Azure 认知搜索知识 store。 知识存储目前为公共预览版。
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3ea879a419aa14d3a6693e23f4f120aca8d9d51
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720064"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>查看包含存储资源管理器的知识库

> [!IMPORTANT] 
> 知识存储目前为公共预览版。 提供的预览功能不带服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-preview](search-api-preview.md)提供了预览功能。 目前支持的门户支持不受支持，也没有 .NET SDK 支持。

在本文中，你将学习如何使用 Azure 门户中的存储资源管理器连接到知识库并进行浏览。

## <a name="prerequisites"></a>必备组件

+ 按照在 Azure 门户中[创建知识库](knowledge-store-create-portal.md)中的步骤或[通过使用 REST 创建 Azure 认知搜索知识库](knowledge-store-create-rest.md)中的步骤创建本演练中使用的示例知识存储。

+ 还需要用于创建知识库的 Azure 存储帐户的名称，以及其从 Azure 门户的访问密钥。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>查看、编辑和查询中的知识库存储资源管理器

1. 在 Azure 门户中，打开用于创建知识库的[存储帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. 在存储帐户的左侧导航窗格中，单击 "**存储资源管理器**"。

1. 展开 "**表**" 列表可显示在旅馆上运行 "**导入数据**" 向导时创建的 Azure 表投影列表查看示例数据。

选择任何表以查看已扩充的数据，包括关键短语情绪评分、纬度和经度位置数据等。

   ![查看存储资源管理器中的表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "查看存储资源管理器中的表")

若要更改任何表值的数据类型或更改表中的单个值，请单击 "**编辑**"。 更改一个表行中任意列的数据类型时，它将应用于所有行。

   ![在存储资源管理器中编辑表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "在存储资源管理器中编辑表")

若要运行查询，请单击命令栏上的 "**查询**" 并输入条件。  

   ![存储资源管理器中的查询表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "存储资源管理器中的查询表")

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

## <a name="next-steps"></a>后续步骤

将此知识库连接到 Power BI，以便进行更深入的分析，或继续使用 REST API 和 Postman 创建不同的知识库。

> [!div class="nextstepaction"]
> [与 Power BI 连接](knowledge-store-connect-power-bi.md)
> [在 REST 中创建知识库](knowledge-store-howto.md)

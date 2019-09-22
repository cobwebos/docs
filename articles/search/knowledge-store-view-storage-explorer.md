---
title: 使用存储资源管理器查看知识存储 - Azure 搜索
description: 使用 Azure 门户的存储资源管理器查看和分析 Azure 搜索知识存储。
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 6c90cec91e85f64397f70b015ffde15a2ea6deaf
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963114"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>使用存储资源管理器查看知识存储

> [!Note]
> 知识存储目前以预览版提供，不应在生产环境中使用。 [Azure 搜索 REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前不支持 .NET SDK。
>
本文介绍如何使用 Azure 门户中的存储资源管理器来连接和浏览知识存储。 若要创建本演练中使用的知识存储示例，请参阅[在 Azure 门户中创建知识存储](knowledge-store-create-portal.md)。

## <a name="prerequisites"></a>先决条件

+ 按照[在 Azure 门户中创建知识存储](knowledge-store-create-portal.md)中的步骤创建本演练中使用的示例知识存储。

+ 还需要用于创建知识存储的 Azure 存储帐户的名称，以及从 Azure 门户获得的其访问密钥。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>在存储资源管理器中查看、编辑和查询知识存储

1. 在 Azure 门户中，打开用于创建知识存储的存储帐户。

1. 在存储帐户的左侧导航窗格中，单击“存储资源管理器”  。

1. 展开**表**列表以显示对酒店评论示例数据运行“导入数据”  向导时创建的 Azure 表投影列表。

选择任何表可查看已扩充的数据，包括关键短语情绪评分、纬度和经度位置数据等。

   ![在存储资源管理器中查看表](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "在存储资源管理器中查看表")

若要更改任何表值的数据类型或更改表中的各个值，请单击“编辑”  。 更改一个表行中任何列的数据类型时，它将应用于所有行。

   ![在存储资源管理器中编辑表](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "在存储资源管理器中编辑表")

若要运行查询，请单击命令栏上的“查询”  并输入条件。  

   ![在存储资源管理器中查询表](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "在存储资源管理器中查询表")

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接   ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

## <a name="next-steps"></a>后续步骤

若要了解如何将此知识存储连接到 Power BI，请参阅以下文章。

> [!div class="nextstepaction"]
> [使用 Power BI 进行连接](knowledge-store-connect-power-bi.md)

若要了解如何使用 REST API 和 Postman 创建知识存储，请参阅以下文章。  

> [!div class="nextstepaction"]
> [在 REST 中创建知识存储](knowledge-store-howto.md)

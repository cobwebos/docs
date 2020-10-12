---
title: 使用 Power BI 连接知识存储
titleSuffix: Azure Cognitive Search
description: 使用 Power BI 连接 Azure 认知搜索知识存储以进行分析和浏览。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89289165"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>使用 Power BI 连接知识存储

本文介绍如何使用 Power BI Desktop 应用中的 Power Query 来连接和浏览知识存储。 使用模板可以更快地开始操作，也可以从头开始构建自定义仪表板。 下面这个简短的视频演示了如何通过结合使用 Azure 认知搜索和 Power BI 来丰富你的数据体验。


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ 按照[在 Azure 门户中创建知识存储](knowledge-store-create-portal.md)或[使用 REST 创建 Azure 认知搜索知识存储](knowledge-store-create-rest.md)中的步骤创建本演练中使用的示例知识存储。 还需要用于创建知识存储的 Azure 存储帐户的名称，以及从 Azure 门户获得的其访问密钥。

+ [安装 Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>示例 Power BI 模板 - 仅限 Azure 门户

创建[使用 Azure 门户的知识存储](knowledge-store-create-portal.md)时，可以选择在“导入数据”向导的第二页下载 [Power BI 模板](https://github.com/Azure-Samples/cognitive-search-templates)。 此模板包含多种适用于基于文本的内容的可视化效果，如 WordCloud 和网络导航器。 

在“添加认知技能”页面单击“获取 Power BI 模板”，从其公共 GitHub 位置检索并下载模板 。 向导会修改模板，以适应数据形状，如在向导中指定的知识存储投影中所捕获的一样。 因此，每次运行该向导时，下载的模板会有所不同（假设数据输入和技能选择不同）。

![示例 Azure 认知搜索 Power BI 模板](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "示例 Power BI 模板")

> [!NOTE]
> 尽管该模板是在向导运行到中途时下载的，但必须等到 Azure 表存储中实际创建了知识存储后，才能使用它。

## <a name="connect-with-power-bi"></a>使用 Power BI 进行连接

1. 启动 Power BI Desktop 并单击“获取数据”。

1. 在“获取数据”窗口中，依次选择“Azure”、“Azure 表存储”。  

1. 单击“连接” 。

1. 对于“帐户名称或 URL”，请输入你的 Azure 存储帐户名称（系统将为你创建完整 URL）。

1. 如果有提示，请输入存储帐户密钥。

1. 选择包含前面演练创建的酒店审核数据的表。 

   + 对于门户演练，表名为 hotelReviewsSsDocument、hotelReviewsSsEntities、hotelReviewsSsKeyPhrases 和 hotelReviewsSsPages。 
   
   + 对于 REST 演练，表名为 hotelReviewsDocument、hotelReviewsPages、hotelReviewsKeyPhrases 和 hotelReviewsSentiment。

1. 单击“加载”。

1. 在顶部功能区中，单击“编辑查询”打开“Power Query 编辑器”。 

   ![打开 Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "打开 Power Query")

1. 选择“hotelReviewsSsDocument”，然后删除“PartitionKey”、“RowKey”和“Timestamp”列。    
   ![编辑表](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "编辑表")

1. 单击表右上方带有反向箭头的图标，以展开“内容”。 显示列的列表后，选择所有列，并取消选择以“metadata”开头的列。 单击“确定”显示所选列。

   ![扩展内容](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "扩展内容")

1. 单击列左上方的 ABC-123 图标，更改以下列的数据类型。

   + 对于“content.latitude”和“Content.longitude”，请选择“十进制数”。 
   + 对于“Content.reviews_date”和“Content.reviews_dateAdded”，请选择“日期/时间”。 

   ![更改数据类型](media/knowledge-store-connect-power-bi/powerbi-change-type.png "更改数据类型")

1. 选择“hotelReviewsSsPages”，然后重复步骤 9 和 10 删除列并展开“内容”。 
1. 将“Content.SentimentScore”的数据类型更改为“十进制数”。
1. 选择“hotelReviewsSsKeyPhrases”，然后重复步骤 9 和 10 删除列并展开“内容”。  对于此表，无需修改数据类型。

1. 在命令栏上，单击“关闭并应用”。

1. 单击左侧导航窗格中的“模型”磁贴，验证 Power BI 是否显示了所有三个表之间的关系。

   ![验证关系](media/knowledge-store-connect-power-bi/powerbi-relationships.png "验证关系")

1. 双击每个关系，确保“交叉筛选方向”设置为“双向”。   这样，在应用筛选器时，视觉对象将会刷新。

1. 单击左侧导航窗格上的“报表”磁贴，以通过可视化浏览数据。 对于文本字段，表和卡是有用的可视化效果。 可以从三个表中的每个表中选择字段，以填充表或卡。 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>清除

使用自己的订阅时，最好在项目结束时确定是否仍然需要所创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接 ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

## <a name="next-steps"></a>后续步骤

若要了解如何使用存储资源管理器浏览此知识存储，请参阅以下文章。

> [!div class="nextstepaction"]
> [使用存储资源管理器查看](knowledge-store-view-storage-explorer.md)
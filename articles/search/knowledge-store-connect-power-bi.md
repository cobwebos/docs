---
title: 使用 Power BI 连接到知识存储 - Azure 搜索
description: 使用 Power BI 连接 Azure 搜索知识存储以进行分析和浏览。
author: lisaleib
services: search
ms.service: search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: de282213535a2e49f73bc30e476bae02d470fdb2
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265655"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>使用 Power BI 连接知识存储

> [!Note]
> 知识存储目前以预览版提供，不应在生产环境中使用。 [Azure 搜索 REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前不支持 .NET SDK。
>
本文介绍如何使用 Power BI Desktop 应用中的 Power Query 来连接和浏览知识存储。 若要创建本演练中使用的知识存储示例，请参阅[在 Azure 门户中创建知识存储](knowledge-store-create-portal.md)。

## <a name="prerequisites"></a>先决条件

+ 按照[在 Azure 门户中创建知识存储](knowledge-store-create-portal.md)中的步骤创建本演练中使用的示例知识存储。 还需要用于创建知识存储的 Azure 存储帐户的名称，以及从 Azure 门户获得的其访问密钥。

+ [安装 Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>使用 Power BI 进行连接

1. 启动 Power BI Desktop 并单击“获取数据”。 

1. 在“获取数据”窗口中，依次选择“Azure”、“Azure 表存储”。   

1. 单击“连接”  。

1. 对于“帐户名称或 URL”，请输入你的 Azure 存储帐户名称（系统将为你创建完整 URL）。 

1. 如果有提示，请输入存储帐户密钥。

1. 选择“hotelReviewsSsDocument”、“hotelReviewsSsKeyPhrases”和“hotelReviewsSsPages”表。    这些表是酒店评论示例数据的 Azure 表投影，其中包含创建知识存储时选择的认知服务扩充。

1. 单击“加载”。 

1. 在顶部功能区中，单击“编辑查询”打开“Power Query 编辑器”。  

   ![打开 Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "打开 Power Query")

1. 选择“hotelReviewsSsDocument”，然后删除“PartitionKey”、“RowKey”和“Timestamp”列。     

   ![编辑表](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "编辑表")

1. 单击表右上方带有反向箭头的图标，以展开“内容”。  显示列的列表后，选择所有列，并取消选择以“metadata”开头的列。 单击“确定”显示所选列。 

   ![编辑表](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "展开内容")

1. 单击列左上方的 ABC-123 图标，更改以下列的数据类型。

   + 对于“content.latitude”和“Content.longitude”，请选择“十进制数”。   
   + 对于“Content.reviews_date”和“Content.reviews_dateAdded”，请选择“日期/时间”。   

   ![更改数据类型](media/knowledge-store-connect-power-bi/powerbi-change-type.png "更改数据类型")

1. 选择“hotelReviewsSsPages”，然后重复步骤 9 和 10 删除列并展开“内容”。  
1. 将“Content.SentimentScore”的数据类型更改为“十进制数”。  
1. 选择“hotelReviewsSsKeyPhrases”，然后重复步骤 9 和 10 删除列并展开“内容”。   对于此表，无需修改数据类型。

1. 在命令栏上，单击“关闭并应用”。 

1. 单击左侧导航窗格中的“模型”磁贴，验证 Power BI 是否显示了所有三个表之间的关系。

   ![验证关系](media/knowledge-store-connect-power-bi/powerbi-relationships.png "验证关系")

1. 双击每个关系，确保“交叉筛选方向”设置为“双向”。    这样，在应用筛选器时，视觉对象将会刷新。

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>清理

在自己的订阅中操作时，最好在项目结束时确定是否仍需要已创建的资源。 持续运行资源可能会产生费用。 可以逐个删除资源，也可以删除资源组以删除整个资源集。

可以使用左侧导航窗格中的“所有资源”或“资源组”链接   ，在门户中查找和管理资源。

如果使用的是免费服务，请记住只能设置三个索引、索引器和数据源。 可以在门户中删除单个项目，以不超出此限制。

## <a name="next-steps"></a>后续步骤

若要了解如何使用存储资源管理器浏览此知识存储，请参阅以下文章。

> [!div class="nextstepaction"]
> [使用存储资源管理器查看](knowledge-store-view-storage-explorer.md)

若要了解如何使用 REST API 和 Postman 创建知识存储，请参阅以下文章。  

> [!div class="nextstepaction"]
> [在 REST 中创建知识存储](knowledge-store-howto.md)

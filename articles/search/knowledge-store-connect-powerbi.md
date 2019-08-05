---
title: 使用 Power BI 连接到知识存储 - Azure 搜索
description: 使用 Azure 门户中的导入数据向导创建知识存储，然后使用 Power BI 建立连接以进行分析和探索。
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: heidist
ms.openlocfilehash: 04b04e8080590aa6e9fe1c17369e83fa5bb6b894
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668216"
---
# <a name="create-an-azure-search-knowledge-store-and-connect-using-power-bi"></a>使用 Power BI 创建 Azure 搜索知识存储并进行连接

> [!Note]
> 知识存储目前以预览版提供，不应在生产环境中使用。 [Azure 搜索 REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前不支持 .NET SDK。
>

知识存储是 Azure 搜索中的一项功能，它可以保留 AI 扩充管道的输出供后续分析或进行其他下游处理。 AI 扩充的管道接受来自受支持数据源的图像文件或非结构化文本文件，将此内容发送到 Azure 搜索索引，应用认知服务中的 AI 扩充（例如图像分析和自然语言处理），然后将结果保存到 Azure 存储中的知识存储。 在知识存储中，可以附加 Power BI 或存储资源管理器等工具来探索结果。

本文介绍如何在门户中创建知识存储，然后在 Power BI Desktop 中使用 Power Query 进行连接和探索。 

本演练使用一个由客户评论和评级、来自认知服务的情绪评分构成的数据集，然后使用 Power Query 查询文档。 数据来源于 Kaggle.com 上的酒店评论数据。 

## <a name="prerequisites"></a>先决条件

+ [下载酒店评论 CSV 文件 (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 此数据集包含客户对酒店的反馈记录。

+ [Power BI Desktop](https://powerbi.microsoft.com/downloads/)

+ [Azure 搜索](search-create-service-portal.md)。 可在本演练中使用免费服务。 

+ [Azure 存储](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)。 确保帐户是“常规用途”帐户：默认的“StorageV2（常规用途 V2）”或“Storage（常规用途 V1）”帐户。   选择 Azure 搜索所在的同一区域。
 
## <a name="prepare-data"></a>准备数据 

将 .csv 文件载入 Azure Blob 存储，以便 Azure 搜索索引器可以访问该文件。

1. [登录到 Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户，单击“Blob”，然后单击“+ 容器”   。

1. [创建一个 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)用于包含示例数据： 

   1. 将该容器命名为 `hotel-reviews`。 
   
   1. 将“公共访问级别”设为任何有效值。 我们使用了默认值。

1. 创建容器后，将其打开，然后在命令栏中选择“上传”  。

1. 导航到包含 **HotelReviews-Free.csv** 的文件夹，选择该文件，然后单击“上传”。 

   ![上传 .csv 文件](media/knowledge-store-howto-powerbi/hotel-reviews-blob-container.png "上传 .csv 文件")

1. 在 Azure 存储中获取连接字符串和容器名称。  创建数据源对象时需要这两个字符串：

   1. 在概述页中单击“访问密钥”并复制一个连接字符串。   该字符串以 `DefaultEndpointsProtocol=https;` 开头，以 `EndpointSuffix=core.windows.net` 结尾。 帐户名和密钥位于该字符串的中间。 

   1. 容器名称应为 `hotel-reviews` 或你分配的任意名称。 

## <a name="create-and-run-ai-enrichments"></a>创建并运行 AI 扩充

使用导入数据向导创建知识存储。 你将要导入数据集，选择扩充，配置知识存储和索引，然后执行。

1. 在 Azure 门户上[找到你的搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 在命令栏上单击“导入数据”。 

1. 在向导的第一个页面中创建数据源对象。

   - 选择“Azure Blob 存储”。 

   - 指定数据源名称，例如 *hotel-reviews-ds*。

   - 由于数据为 .csv 格式，因此请选择“分隔的文本”作为分析模式，选择“第一行包含标头”，并确保分隔符为逗号。  

   - 可在门户中的“访问密钥”下获取 Azure 存储帐户的连接字符串。 

   - 还可以从门户中的“Azure 存储 Blob”列表获取容器名称。

      ![创建数据源对象](media/knowledge-store-howto-powerbi/hotel-reviews-ds.png "创建数据源对象")

1. 在向导的第二个页面中添加扩充并配置知识存储。

   - 在“附加认知服务”中，可以使用免费资源（每天最多允许 20 个事务）。  HotelReviews-Free.csv 中的记录数小于 20。

   - 在“添加扩充”中，将技能集命名为 *hotel-reviews-ss*，选择“reviews_text”字段，选择粒度级“页面(包含 5000 个字符的区块)”，然后选择下面三个认知技能：    “提取关键短语”、“检测语言”、“检测情绪”。   

      ![创建技能集](media/knowledge-store-howto-powerbi/hotel-reviews-ss.png "创建技能集")

   - 在“将扩充保存到知识存储”中，提供常规用途 Azure 存储帐户的连接字符串。  在此部分选择“Azure 表项目”选项时，将在 Azure 表存储中创建一个知识存储。 

      ![配置知识存储](media/knowledge-store-howto-powerbi/hotel-reviews-ks.png "配置知识存储")
   
   单击“下一步:  自定义目标索引”以继续下一步。

1. 在 Azure 搜索中为可选的全文搜索查询配置索引。 尽管本演练重点介绍如何使用 Power BI 连接到 Azure 表存储，但“导入数据”向导也可以在 Azure 搜索中创建用于全文搜索的索引。  

   该向导将对数据源采样，以推断字段和数据类型，因此，你只需选择必要的属性即可实现所需的行为。 例如，“可检索”表示可从服务检索字段内容，而“可搜索”可对所选字段启用全文搜索。  

   - 指定索引的名称，例如 *hotel-reviews-idx*。
   - 将所有字段设置为“可检索”。 
   - 将 *city*、*name*、*reviews_text*、*language*、*keyphrases* 设置为“可搜索”。 
   - 将 *sentiment*、*language*、*keyphrases* 设置为“可筛选”和“可分面”。   
   
    索引应与下图类似：

     ![配置索引](media/knowledge-store-howto-powerbi/hotel-reviews-idx.png "配置索引")

   单击“下一步:  创建索引器”以继续下一步。

1. 为索引器指定名称和执行频率，以配置索引器。 本演练使用 *hotel-reviews-idxr* 作为索引器名称，使用默认的“一次”计划立即运行索引器。 

   索引器执行会使以前的所有配置保持运动。 提取、处理和引入操作都在此步骤中发生。

1. 在门户上的“通知”队列中监视索引编制。 完成执行需要几分钟时间。

## <a name="connect-with-power-bi"></a>使用 Power BI 进行连接

1. 启动 Power BI Desktop 并选择“获取数据”。 

1. 在“获取数据”中选择“Azure”，然后选择“Azure 表存储”。   单击“连接”  。

1. 在“帐户名称”或“URL”中，粘贴你的 Azure 存储帐户名称（系统已解析完整的 URL）。

1. 输入帐户密钥。

1. 选择“文档”、“关键短语”和“页面”。 这是当你在知识存储配置中选择同名的项时，导入数据向导创建的表。 单击“加载”。 

1. 单击“编辑查询”命令打开 Power Query。 

   ![打开 Power Query](media/knowledge-store-howto-powerbi/powerbi-edit-queries.png "打开 Power Query")

1. 对于文档：

   - 请删除 Azure 表存储创建的 PartitionKey、RowKey 和 Timestamp 列。 知识存储提供此分析中使用的关系。

   - 展开内容列。

     ![编辑表](media/knowledge-store-howto-powerbi/powerbi-edit-table.png "编辑表")

1. 选择所有字段，然后取消选择以“metadata”开头的字段。

1. 使用每个列上的 ABC-123 图标更正以下列的数据类型：

   - “日期”列的数据类型应是“日期时间” 
   - “纬度”的数据类型应是“十进制数字”  
   - “经度”的数据类型应是“十进制数字”  

1. 针对“关键短语”重复上述步骤，删除 PartitionKey 和其他列，展开内容列，将“SentimentScore”设置为“十进制数字”。  

1. 同样，针对“页面”重复上述步骤，删除 PartitionKey 和其他列，展开内容列。 对于此表，无需修改数据类型。

1. 在 Power Query 命令栏的最左侧单击“关闭并应用”。 

1. 验证 Power BI 是否能够识别知识存储在数据中创建的关系。 在左侧导航窗格中单击“关系”磁贴。 所有三个表都应该相关。

   ![验证关系](media/knowledge-store-howto-powerbi/powerbi-relationships.png "验证关系")

## <a name="try-with-larger-data-sets"></a>尝试处理更大的数据集

我们特意保留了较小的数据集，以免在演练中产生费用。 若要获得更真实的体验，可以创建并附加一个计费的认知服务资源，以针对情绪分析器、关键短语提取和语言分析器技能启用更多的事务。

在 Azure Blob 存储中创建新容器，并将每个 CSV 文件上传到其自身的容器。 在导入数据向导的数据源创建步骤中指定上述某个容器。

| 说明 | 链接 |
|-------------|------|
| 免费层   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| 小型（500 条记录） | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| 中型（6000 条记录）| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| 大型（包含 35000 条记录的完整数据集） | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) 请注意，极大型数据集的处理费用很高。 此数据集的处理费用大约为 1000 美元。|

在向导的扩充步骤中，附加一个在 Azure 搜索所在的同一区域中的 *S0* 层创建的计费[认知服务](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)资源，以使用更大的数据集。 

  ![创建认知服务资源](media/knowledge-store-howto-powerbi/create-cognitive-service.png "创建认知服务资源")

## <a name="next-steps"></a>后续步骤

若要重复此练习或要执行其他 AI 扩充演练，请删除刚刚创建的 *hotel-reviews-idx* 索引器。 删除索引器会将每日的免费事务计数器重置为零。 

有关更详细的知识存储演练教程，请继续学习下一篇文章，其中介绍了如何使用 REST API 和 Postman 创建知识存储。

> [!div class="nextstepaction"]
> [知识存储入门](knowledge-store-howto.md)
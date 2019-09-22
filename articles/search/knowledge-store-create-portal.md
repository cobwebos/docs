---
title: 在 Azure 门户中创建知识存储 - Azure 搜索
description: 使用 Azure 门户中的“导入数据”向导创建 Azure 搜索知识存储，用于保存认知搜索管道的扩充内容。
author: lisaleib
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: v-lilei
ms.openlocfilehash: 14996d0ac9ee4e086a5dccd9275ef694adca06ca
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70963184"
---
# <a name="create-an-azure-search-knowledge-store-in-the-azure-portal"></a>在 Azure 门户中创建 Azure 搜索知识存储

> [!Note]
> 知识存储目前以预览版提供，不应在生产环境中使用。 [Azure 搜索 REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前不支持 .NET SDK。
>

知识存储是 Azure 搜索中的一项功能，它可以保留 AI 扩充管道的输出供后续分析或进行其他下游处理。 AI 扩充的管道接受图像文件或非结构化文本文件，使用 Azure 搜索为其编制索引，应用认知服务中的 AI 扩充（例如图像分析和自然语言处理），并将结果保存到 Azure 存储中的知识存储。 然后，可以使用 Power BI 或存储资源管理器等工具来浏览知识存储。

在本文中，你将使用 Azure 门户上的“导入数据”向导来引入 AI 扩充、为其编制索引，然后将其应用到一系列酒店评论。 酒店评论已导入到 Azure Blob 存储，结果已作为知识存储保存在 Azure 表存储中。

创建知识存储后，可以了解如何使用存储资源管理器或 Power BI 来访问此知识存储。

## <a name="prerequisites"></a>先决条件

+ [创建 Azure 搜索服务](search-create-service-portal.md)或在当前订阅下[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可在本教程中使用免费服务。

+ [创建一个 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)用于存储示例数据和知识存储。 存储帐户必须使用与 Azure 搜索服务相同的位置（例如 US-WE），“帐户类型”必须是“StorageV2 (常规用途 V2)”（默认设置）或“Storage (常规用途 V1)”。   

## <a name="load-the-data"></a>加载数据

将酒店评论 CSV 文件载入 Azure Blob 存储，使之可由 Azure 搜索索引器访问，并可通过 AI 扩充管道进行馈送。

### <a name="create-an-azure-blob-container-with-the-data"></a>创建包含数据的 Azure Blob 容器

1. [下载已保存到 CSV 文件中的酒店评论数据 (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)。 此数据来源于 Kaggle.com，包含客户对酒店的反馈。
1. [登录到 Azure 门户](https://portal.azure.com)，导航到你的 Azure 存储帐户。
1. [创建 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)为此，请在存储帐户的左侧导航栏中单击“Blob”，然后单击命令栏上的“+ 容器”。  
1. 请输入 `hotel-reviews` 作为新容器的**名称**。
1. 选择任何“公共访问级别”。  我们使用了默认值。
1. 单击“确定”创建 Azure Blob 容器。 
1. 打开新的 `hotels-review` 容器，单击“上传”，然后选择在第一个步骤中下载的“HotelReviews-Free.csv”文件。  

    ![上传数据](media/knowledge-store-create-portal/upload-command-bar.png "上传酒店评论")

1. 单击“上传”，将该 CSV 文件导入 Azure Blob 存储。  随后会显示新容器。

    ![创建 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "创建 Azure Blob 容器")

### <a name="get-the-azure-storage-account-connection-string"></a>获取 Azure 存储帐户连接字符串

1. 在门户上导航到你的 Azure 存储帐户。
1. 在服务的左侧导航栏中，单击“访问密钥”。 
1. 在“密钥 1”下，复制并保存“连接字符串”。   该字符串以 `DefaultEndpointsProtocol=https` 开头。 存储帐户名称和密钥已嵌入到该字符串中。 请保管好此字符串， 在后续步骤中需要用到。

## <a name="create-and-run-ai-enrichments"></a>创建并运行 AI 扩充

使用导入数据向导创建知识存储。 你将要创建一个数据源，选择扩充，配置知识存储和索引，然后执行。

### <a name="start-the-import-data-wizard"></a>启动“导入数据”向导

1. 在 Azure 门户上[找到你的搜索服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. 在命令栏上，单击“导入数据”启动导入向导。 

### <a name="connect-to-your-data-import-data-wizard"></a>连接到数据（“导入数据”向导）

在此向导步骤中，你将从包含酒店数据的 Azure Blob 创建一个数据源。

1. 在“数据源”列表中，选择“Azure Blob 存储”。  
1. 对于“名称”，请输入 `hotel-reviews-ds`。 
1. 对于“分析模式”，请选择“分隔文本”，然后选中“第一行包含标头”复选框。    确保“分隔符”是逗号 (,)。 
1. 输入在上一步骤中保存的存储服务**连接字符串**。
1. 对于“容器名称”，请输入 `hotel-reviews`。 
1. 单击“下一步:  添加认知搜索(可选)”。

      ![创建数据源对象](media/knowledge-store-create-portal/hotel-reviews-ds.png "创建数据源对象")

## <a name="add-cognitive-search-import-data-wizard"></a>添加认知搜索（“导入数据”向导）

在此向导步骤中，你将创建一个包含认知技能扩充的技能集。 本示例中使用的技能将提取关键短语并检测语言和情绪。 这些扩充内容将以 Azure 表的形式“投影”到知识存储。

1. 展开“附加认知服务”。  默认已选择“免费(受限扩充)”。  之所以可以使用此资源，是因为 HotelReviews-Free.csv 中的记录数为 19 个，并且此免费资源每天最多允许 20 个事务。
1. 展开“添加扩充”。 
1. 对于“技能集名称”，请输入 `hotel-reviews-ss`。 
1. 对于“源数据字段”，请选择“*reviews_text”。  
1. 对于“扩充粒度级别”，请选择“页面(5000 个字符区块)”。  
1. 选择以下认知技能：
    + **提取关键短语**
    + **检测语言**
    + **检测情绪**

      ![创建技能集](media/knowledge-store-create-portal/hotel-reviews-ss.png "创建技能集")

1. 展开“将扩充内容保存到知识存储”。 
1. 输入在上一步骤中保存的**存储帐户连接字符串**。
1. 选择以下 **Azure 表投影**：
    + 文档 
    + **页**
    + **关键短语**

    ![配置知识存储](media/knowledge-store-create-portal/hotel-reviews-ks.png "配置知识存储")

1. 单击“下一步:  自定义目标索引”。

### <a name="import-data-import-data-wizard"></a>导入数据（“导入数据”向导）

在此向导步骤中，你将为可选的全文搜索查询配置索引。 向导将对数据源进行采样，以推断字段和数据类型。 你只需为所需的行为选择属性。 例如，“可检索”属性将允许搜索服务返回一个字段值，而“可搜索”属性将对字段启用全文搜索。  

1. 对于“索引名称”，请输入 `hotel-reviews-idx`。 
1. 对于属性，请做出以下选择：
    + 为所有字段选择“可检索”。 
    + 为以下字段选择“可筛选”和“可分面”：   “情绪”、“语言”和“关键短语”   
    + 为以下字段选择“可搜索”：“城市”、“名称”、“reviews_text”、“语言”和“关键短语”      

    索引应与下图类似： 由于该列表很长，图像中未显示所有字段。

    ![配置索引](media/knowledge-store-create-portal/hotel-reviews-idx.png "配置索引")

1. 单击“下一步:  创建索引器”。

### <a name="create-an-indexer"></a>创建索引器

在此向导步骤中，你将配置一个索引器，用于统一提取前面向导步骤中定义的数据源、技能集和索引。

1. 对于“名称”  ，请输入 `hotel-reviews-idxr`。
1. 对于“计划”，请保留默认设置“一次”。  
1. 单击“提交”运行索引器。  数据提取、索引编制和应用认知技能的操作都在此步骤中发生。

### <a name="monitor-the-notifications-queue-for-status"></a>监视通知队列的状态

1. 在 Azure 门户中，监视可单击的“Azure 搜索通知”状态链接的通知活动日志。  执行过程可能需要几分钟才能完成。

## <a name="next-steps"></a>后续步骤

使用认知服务扩充数据并将结果投影到知识存储后，接下来可以使用存储资源管理器或 Power BI 来浏览扩充的数据集。

若要了解如何使用存储资源管理器浏览此知识存储，请参阅以下演练。

> [!div class="nextstepaction"]
> [使用存储资源管理器查看](knowledge-store-view-storage-explorer.md)

若要了解如何将此知识存储连接到 Power BI，请参阅以下演练。

> [!div class="nextstepaction"]
> [使用 Power BI 进行连接](knowledge-store-connect-power-bi.md)

若要重复此练习或尝试其他 AI 扩充演练，请删除 *hotel-reviews-idxr* 索引器。 删除该索引器会将每日的免费事务计数器重置为零。

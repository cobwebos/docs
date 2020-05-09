---
title: 在 Azure 门户中创建知识存储（预览版）
titleSuffix: Azure Cognitive Search
description: 使用“导入数据”向导创建用于保存扩充内容的知识存储。 连接到知识存储以便从其他应用进行分析，或将扩充内容发送到下游流程。 此功能目前处于公开预览状态。
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: 21279b2b4735a25210e8373d76d0d63f9c711bfc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "77472360"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Azure 认知搜索知识存储

> [!IMPORTANT] 
> 知识存储目前以公开预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

知识存储是 Azure 认知搜索的一项功能，它可以保存认知技能管道的输出，以进行后续分析或下游处理。 

管道接受非结构化文本和图像作为原始内容，通过认知服务（例如 OCR、图像分析和自然语言处理）应用 AI，并提取信息和输出新结构和信息。 管道创建的物理项目之一是[知识存储](knowledge-store-concept-intro.md)，可通过工具访问该知识存储以分析和浏览内容。

在本快速入门中，你将合并 Azure 云中的服务和数据以创建知识存储。 一切准备就绪后，可在门户中运行“导入数据”向导，以将这些数据提取到一起。  最终结果是可以在门户（[存储资源管理器](knowledge-store-view-storage-explorer.md)）中查看的原始文本内容和 AI 生成的内容。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-services-and-load-data"></a>创建服务并加载数据

本快速入门使用 Azure 认知搜索、Azure Blob 存储和用于 AI 的 [Azure 认知服务](https://azure.microsoft.com/services/cognitive-services/)。 

由于工作负载很小，因此，认知服务在幕后会抽调一部分算力来免费处理事务（每天最多 20 个）。 由于数据集很小，因此可以跳过创建或附加认知服务资源的过程。

1. [下载 HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 此数据是保存在某个 CSV 文件中的酒店评论数据（源自 Kaggle.com），其中包含客户对一家酒店的 19 条反馈。 

1. [创建 Azure 存储帐户](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)，或在当前订阅下[查找现有帐户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 你将使用 Azure 存储来保存要导入的原始内容，并使用知识存储（最终结果）。

   选择“StorageV2 (常规用途 V2)”帐户类型  。

1. 打开 Blob 服务页并创建一个名为 hotel-reviews 的容器  。

1. 单击“上载” 。 

    ![上传数据](media/knowledge-store-create-portal/upload-command-bar.png "上传酒店评论")

1. 选择在第一个步骤中下载的 **HotelReviews-Free.csv** 文件。

    ![创建 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "创建 Azure Blob 容器")

1. 对此资源的操作即将完成，但在退出这些页面之前，请使用左侧导航窗格中的链接打开“访问密钥”页。  获取用于从 Blob 存储检索数据的连接字符串。 连接字符串类似于以下示例：`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. 仍然在门户中，切换到 Azure 认知搜索。 [新建服务](search-create-service-portal.md)或[查找现有服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 可以使用本快速入门的免费服务。

现在可以在“导入数据”向导中转到下一步。

## <a name="run-the-import-data-wizard"></a>运行“导入数据”向导

在搜索服务的“概述”页中，单击命令栏上的“导入数据”以通过四个步骤创建知识存储。 

  ![导入数据命令](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>步骤 1：创建数据源

1. 在“连接到数据”中，选择“Azure Blob 存储”，再选择创建的帐户和容器   。 
1. 对于“名称”，请输入 `hotel-reviews-ds`。 
1. 对于“分析模式”，请选择“分隔文本”，然后选中“第一行包含标头”复选框。    确保“分隔符”是逗号 (,)。 
1. 在“连接字符串”中，粘贴从 Azure 存储的“访问密钥”页面复制的连接字符串   。
1. 在“容器”中，输入保存数据的 blob 容器的名称  。

    页面应类似于以下屏幕截图。

    ![创建数据源对象](media/knowledge-store-create-portal/hotel-reviews-ds.png "创建数据源对象")

1. 继续转到下一页。

### <a name="step-2-add-cognitive-skills"></a>步骤 2：添加认知技能

在此向导步骤中，你将创建一个包含认知技能扩充的技能集。 源数据由多种语言的客户评论构成。 与此数据集相关的技能包括关键短语提取、情绪检测和文本翻译。 在后续步骤中，这些扩充内容将以 Azure 表的形式“投影”到知识存储。

1. 展开“附加认知服务”。  默认已选择“免费(受限扩充)”。  之所以可以使用此资源，是因为 HotelReviews-Free.csv 中的记录数为 19 个，并且此免费资源每天最多允许 20 个事务。
1. 展开“添加扩充”。 
1. 对于“技能集名称”，请输入 `hotel-reviews-ss`。 
1. 对于“源数据字段”，请选择“reviews_text”。  
1. 对于“扩充粒度级别”，请选择“页面(5000 个字符区块)”。  
1. 选择以下认知技能：
    + **提取关键短语**
    + **翻译文本**
    + **检测情绪**

      ![创建技能集](media/knowledge-store-create-portal/hotel-reviews-ss.png "创建技能集")

1. 展开“将扩充内容保存到知识存储”。 
1. 选择以下 **Azure 表投影**：
    + 文档 
    + **页**
    + **关键短语**
1. 输入在上一步骤中保存的**存储帐户连接字符串**。

    ![配置知识存储](media/knowledge-store-create-portal/hotel-reviews-ks.png "配置知识存储")

1. 或者，下载 Power BI 模板。 从向导中访问模板时，会更改本地 .pbit 文件以反映数据的形状。

1. 继续转到下一页。

### <a name="step-3-configure-the-index"></a>步骤 3：配置索引

在此向导步骤中，你将为可选的全文搜索查询配置索引。 向导将对数据源进行采样，以推断字段和数据类型。 你只需为所需的行为选择属性。 例如，“可检索”属性将允许搜索服务返回一个字段值，而“可搜索”属性将对字段启用全文搜索。  

1. 对于“索引名称”，请输入 `hotel-reviews-idx`。 
1. 对于属性，请接受默认选项：“可检索”和“可搜索”（对于管道正在创建的新字段）   。

    索引应与下图类似： 由于该列表很长，图像中未显示所有字段。

    ![配置索引](media/knowledge-store-create-portal/hotel-reviews-idx.png "配置索引")

1. 继续转到下一页。

### <a name="step-4-configure-the-indexer"></a>步骤 4：配置索引器

在此向导步骤中，你将配置一个索引器，用于统一提取前面向导步骤中定义的数据源、技能集和索引。

1. 对于“名称”  ，请输入 `hotel-reviews-idxr`。
1. 对于“计划”，请保留默认设置“一次”。  
1. 单击“提交”运行索引器。  数据提取、索引编制和应用认知技能的操作都在此步骤中发生。

## <a name="monitor-status"></a>监视状态

与典型的基于文本的索引相比，认知技能索引编制需要花费更长的时间才能完成。 向导应在概述页打开索引器列表，以便你能够跟踪进度。 若要进行自导航，请转到“概述”页，然后单击“索引器”  。

在 Azure 门户中，还可以监视可单击的“Azure 认知搜索通知”状态链接的通知活动日志。  执行过程可能需要几分钟才能完成。

## <a name="next-steps"></a>后续步骤

使用认知服务扩充数据并将结果投影到知识存储后，接下来可以使用存储资源管理器或 Power BI 来浏览扩充的数据集。

可以在存储资源管理器中查看内容，或进一步使用 Power BI 通过可视化来获取见解。

> [!div class="nextstepaction"]
> [使用存储资源管理器查看](knowledge-store-view-storage-explorer.md)
> [使用 Power BI 进行连接](knowledge-store-connect-power-bi.md)

> [!Tip]
> 若要重复此练习或尝试其他 AI 扩充演练，请删除 *hotel-reviews-idxr* 索引器。 删除该索引器会将认知服务处理功能的每日免费事务计数器重置为零。

---
title: 将认知服务资源与技能集联系起来 - Azure 搜索
description: 向 Azure 搜索中的认知扩充管道附加认知服务的相关说明。
manager: nitinme
author: LuisCabrer
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: luisca
ms.openlocfilehash: 113286f829b628d4740fbba34e7279741a934aef
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "71265926"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>将认知服务资源与 Azure 搜索中的技能集联系起来 

AI 算法在 Azure 搜索中驱动用于文档扩充的[认知索引管道](cognitive-search-concept-intro.md)。 这些算法基于 Azure 认知服务资源，其中包括用于图像分析的[计算机视觉](https://azure.microsoft.com/services/cognitive-services/computer-vision/)，以及用于实体识别、关键短语提取和其他根据的光学字符识别（OCR）和[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/). 与用于文档扩充的 Azure 搜索所使用的算法一样，算法被包装在某个*技能*中，放在*技能组合*中，并在索引期间由*索引器*引用。

你可以免费获取有限数量的文档。 或者，可以将计费认知服务资源附加到*技能组合*，以实现更大、更频繁的工作负荷。 在本文中，你将了解如何附加可计费认知服务资源，以便在 Azure 搜索[索引](search-what-is-an-index.md)期间丰富文档。

> [!NOTE]
> 可计费事件包括在 Azure 搜索中对认知服务 API 和图像提取进行的调用，作为文档解密阶段的一部分。 从文档中提取文本或没有调用认知服务的技能不收取任何费用。
>
> 计费技能的执行是[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)。 有关图像提取的定价，请参阅[Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)。

## <a name="same-region-requirement"></a>相同区域要求

我们要求 Azure 搜索和 Azure 认知服务位于同一区域。 否则，将在运行时收到此消息： `"Provided key is not a valid CognitiveServices type key for the region of your search service."` 

无法跨区域移动服务。 如果收到此错误，应在 Azure 搜索所在的同一区域中创建新的认知服务资源。

> [!NOTE]
> 某些内置技能基于非区域认知服务（例如，[文本翻译技能](cognitive-search-skill-text-translation.md)）。 请注意，如果将任何这些技能添加到技能组合，则不能保证你的数据与 Azure 搜索或认知服务资源位于同一区域。 有关更多详细信息，请参阅[服务状态页](https://aka.ms/allinoneregioninfo)。

## <a name="use-free-resources"></a>使用免费资源

可以使用有限的免费处理选项来完成认知搜索教程和快速入门练习。

每个订阅每天的免费（受限根据）资源限制为20个文档。

1. 打开 "导入数据" 向导：

   ![打开导入数据向导](media/search-get-started-portal/import-data-cmd.png "打开导入数据向导")

1. 选择数据源并继续**添加认知搜索（可选）** 。 有关此向导的分步演练，请参阅[使用门户工具进行导入、索引和查询](search-get-started-portal.md)。

1. 展开 "**附加认知服务**"，然后选择 "**免费（受限根据）** "：

   ![扩展的附加认知服务部分](./media/cognitive-search-attach-cognitive-services/attach1.png "扩展的附加认知服务部分")

1. 继续执行下一步，**添加根据**。 有关门户中可用的技能的说明，请参阅 "认知搜索" 快速入门中的[步骤2：添加认知技巧](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)。

## <a name="use-billable-resources"></a>使用付费资源

对于每天创建20多个根据的工作负荷，请确保附加可计费认知服务资源。 建议始终附加可计费认知服务资源，即使您从未打算调用认知服务 API。 附加资源会重写每日限制。

仅对调用认知服务 API 的技能收费。 对于[自定义技能](cognitive-search-create-custom-skill-example.md)或不基于 API 的[文本合并](cognitive-search-skill-textmerger.md)、[文本拆分器](cognitive-search-skill-textsplit.md)和[整形](cognitive-search-skill-shaper.md)器等技能，无需付费。

1. 打开 "导入数据" 向导，选择数据源，然后继续**添加认知搜索（可选）** 。

1. 展开 "**附加认知服务**"，然后选择 "**创建新的认知服务资源**"。 此时将打开一个新选项卡，以便您可以创建资源：

   ![创建认知服务资源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "创建认知服务资源")

1. 在 "**位置**" 列表中，选择 Azure 搜索服务所在的区域。 出于性能方面的原因，请务必使用此区域。 使用此区域还会将跨区域的出站带宽收费。

1. 在 "**定价层**" 列表中，选择 " **S0** " 以获取认知服务功能的多功能集合，包括备份 Azure 搜索所使用的预定义技能的视觉和语言功能。

   对于 S0 层，可以在 "[认知服务定价" 页](https://azure.microsoft.com/pricing/details/cognitive-services/)上查找特定工作负荷的费率。
  
   + 在 "**选择产品**/服务" 列表中，确保选择 "**认知服务**"。
   + 在 "**语言**功能" 下，**文本分析标准**的费率适用于 AI 索引。
   + 在**远景**功能下，适用**计算机视觉 S1**的费率。

1. 选择 "**创建**" 设置新的认知服务资源。

1. 返回到上一个选项卡，其中包含导入数据向导。 选择 "**刷新**" 以显示认知服务资源，然后选择资源：

   ![选择认知服务资源](./media/cognitive-search-attach-cognitive-services/attach2.png "选择认知服务资源")

1. 展开 "**添加根据**" 部分，选择要对数据运行的特定认知技能。 完成向导的剩余部分。 有关门户中可用的技能的说明，请参阅 "认知搜索" 快速入门中的[步骤2：添加认知技巧](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>将现有技能集附加到认知服务资源

如果你有现有的技能集，可将其附加到新的或不同的认知服务资源。

1. 在 "**服务概述**" 页上，选择 "**技能集**：

   ![技能集选项卡](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "技能集选项卡")

1. 选择技能组合的名称，然后选择现有的资源或创建一个新的资源。 选择“确定”以确认所做的更改。

   ![技能组合资源列表](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能组合资源列表")

   请记住，**免费（受限根据）** 选项每天会限制你使用20个文档，你可以使用 "**创建新认知服务" 资源**来预配新的可计费资源。 如果创建了新资源，请选择“刷新”以刷新认知服务资源的列表，然后选择该资源。

## <a name="attach-cognitive-services-programmatically"></a>以编程方式附加认知服务

以编程方式定义技能集时，请将 `cognitiveServices` 节添加到该技能集。 在该部分中，包含要与技能组合关联的认知服务资源的键。 请记住，资源必须与 Azure 搜索资源位于同一区域。 另外请包含 `@odata.type`，并将其设置为 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

以下示例演示了此模式。 请注意定义末尾的 `cognitiveServices` 部分。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2019-05-06
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>示例：估算成本

若要估计与认知搜索索引相关的成本，请首先了解平均值文档的外观，以便可以运行一些数字。 例如，你可能会大致接近：

+ 1000个 Pdf。
+ 每个页面六页。
+ 每页一个图像（6000个图像）。
+ 每页3000个字符。

假设管道由每个 PDF、图像和文本提取的文档破解、图像的光学字符识别（OCR）和组织的实体识别组成。

本文中所示的价格是假设的。 它们用于说明估算过程。 你的成本可能会降低。 有关事务的实际价格，请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 破解包含文本和图像内容的文档时，文本提取目前是免费的。 对于6000映像，假设每个提取的1000图像均为 $1。 对于此步骤，这是 $6.00 的代价。

2. 对于英语环境中 6000 个图像的 OCR，OCR 认知技能使用最佳算法 (DescribeText)。 假设每分析 1,000 个图像的成本为 $2.50，则这一步需要支付 $15.00。

3. 对于实体提取，每页总共有三个文本记录。 每条记录包含 1,000 个字符。 每页三个文本记录乘以6000页，等于18000个文本记录。 假设 1000 个文本记录的成本为 $2.00，则这一步的成本为 $36.00。

将其全部放在一起，你需要支付 $57.00，以将此类型的 1000 PDF 文档引入所述的技能组合。

## <a name="next-steps"></a>后续步骤
+ [Azure 搜索定价页](https://azure.microsoft.com/pricing/details/search/)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能集 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)

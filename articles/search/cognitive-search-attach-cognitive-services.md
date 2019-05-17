---
title: 将认知服务资源与技能集联系起来 - Azure 搜索
description: 有关将认知服务-一体订阅附加到 Azure 搜索中的认知扩充管道的说明。
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 69b03bd24abcdf502bf80cfce4221f4958058932
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541708"
---
# <a name="attach-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>将认知服务资源与 Azure 搜索中的技能集联系起来 

AI 算法驱动器[认知索引管道](cognitive-search-concept-intro.md)用于处理 Azure 搜索中的非结构化的数据。 这些算法基于[Azure 认知服务资源](https://azure.microsoft.com/services/cognitive-services/)，其中包括[计算机视觉](https://azure.microsoft.com/services/cognitive-services/computer-vision/)进行图像分析和光学字符识别 (OCR) 和[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)实体识别、 关键短语提取和其他正。

可以免费扩充有限数量的文档，或者附加付费的认知服务资源来处理更大、更频繁的工作负荷。 在本文中，将了解如何将认知服务资源关联与您的认知技能以来丰富数据期间[Azure 搜索索引](search-what-is-an-index.md)。

即使你的管道包括认知服务 Api 不相关的技能，你仍应该附加认知服务资源。 执行此操作将覆盖限制为很小的每日到你的免费资源。 你不会收取未绑定到认知服务 Api 的技能。 这些技能包括[自定义技能](cognitive-search-create-custom-skill-example.md)，[文本合并](cognitive-search-skill-textmerger.md)，[文本拆分器](cognitive-search-skill-textsplit.md)，以及[整形程序](cognitive-search-skill-shaper.md)。

> [!NOTE]
> 随着通过增加处理、 添加更多文档，或添加更多的 AI 算法的频率扩展作用域，您需要将附加可计费的认知服务资源。 用于调用认知服务中的 Api 和图像提取 Azure 搜索中的文档破解阶段的一部分，你将需要付费。 你不会收取从文档中提取文本。
>
> 执行的内置技能收费[认知服务付款现转价格](https://azure.microsoft.com/pricing/details/cognitive-services/)。 有关图像提取定价信息，请参阅[Azure 搜索定价页](https://go.microsoft.com/fwlink/?linkid=2042400)。

## <a name="use-free-resources"></a>使用免费资源

可以使用有限的免费处理选项来完成认知搜索教程和快速入门练习。

免费 （有限的正） 资源被限制为每一天，每个订阅 20 个文档。

1. 打开导入数据向导：

   ![打开导入数据向导](media/search-get-started-portal/import-data-cmd2.png "打开导入数据向导")

1. 选择数据源，然后继续**添加认知搜索 （可选）**。 有关此向导的分步演练，请参阅[导入、 索引和查询通过使用门户工具](search-get-started-portal.md)。

1. 展开**附加认知服务**，然后选择**免费 （有限正）**:

   ![展开附加的认知服务部分](./media/cognitive-search-attach-cognitive-services/attach1.png "展开附加的认知服务部分")

1. 下一步，继续**添加到**。 技能在门户中提供的说明，请参阅[步骤 2:添加认知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)认知搜索快速入门中。

## <a name="use-billable-resources"></a>使用付费资源

对于每日 20 多个正创建的工作负荷，需要将附加可计费的认知服务资源。

我们仅对调用认知服务 Api 的技能的收费。 不支付[自定义技能](cognitive-search-create-custom-skill-example.md)，或技能包括[文本合并](cognitive-search-skill-textmerger.md)，[文本拆分器](cognitive-search-skill-textsplit.md)，以及[整形程序](cognitive-search-skill-shaper.md)，这不是基于 API 的。

1. 打开导入数据向导，选择数据源，并继续**添加认知搜索 （可选）**。

1. 展开**附加认知服务**，然后选择**创建新的认知服务资源**。 一个新选项卡将打开，以便您可以创建资源：

   ![创建认知服务资源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "创建认知服务资源")

1. 在中**位置**列表中，选择你的 Azure 搜索服务所在的区域。 需要使用此区域，出于性能原因。 使用此区域还使无效出站带宽费用跨区域。

1. 在中**定价层**列表中，选择**S0**若要获取的认知服务功能，包括返回 Azure 搜索使用的预定义的技能的视觉和语言功能的多功能一体集合。

   S0 层上，您可以找到费率针对特定工作负荷上[认知服务定价页面](https://azure.microsoft.com/pricing/details/cognitive-services/)。
  
   + 在中**选择产品/服务**列表中，请确保**认知服务**处于选中状态。
   + 下**语言**功能的速率**文本分析标准**适用于 AI 的索引。
   + 下**愿景**功能的速率**计算机视觉 S1**应用。

1. 选择**创建**来预配新的认知服务资源。

1. 返回到上一个选项卡，其中包含导入数据向导。 选择**刷新**要显示的认知服务资源，然后选择相应资源：

   ![选择认知服务资源](./media/cognitive-search-attach-cognitive-services/attach2.png "选择认知服务资源")

1. 展开**添加到**部分，选择你想要对数据运行的特定认知技能。 完成向导的其余部分。 技能在门户中提供的说明，请参阅[步骤 2:添加认知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)认知搜索快速入门中。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>将现有技能集附加到认知服务资源

如果你有现有的技能集，可将其附加到新的或不同的认知服务资源。

1. 上**服务概述**页上，选择**技能集**:

   ![“技能集”选项卡](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "“技能集”选项卡")

1. 选择名称方面的技能，然后选择一个现有资源或创建一个新。 选择“确定”以确认所做的更改。

   ![技能集资源列表](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能集资源列表")

   请记住， **（限制到） 免费**选项限制为 20 文档每日，并且你可以用**创建新的认知服务资源**来预配新的可计费资源。 如果创建了新资源，请选择“刷新”以刷新认知服务资源的列表，然后选择该资源。

## <a name="attach-cognitive-services-programmatically"></a>以编程方式附加认知服务

以编程方式定义技能集时，请将 `cognitiveServices` 节添加到该技能集。 在该部分中，包含你想要将与技能组合相关联的认知服务资源的键。 请记住，资源必须与 Azure 搜索资源位于同一区域中。 另外请包含 `@odata.type`，并将其设置为 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

以下示例演示了此模式。 请注意`cognitiveServices`定义的末尾部分。

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

若要估计与认知搜索索引关联的成本，请使用的平均文档使你可以运行一些数字的如下一个构想开始。 例如，可能会估计：

+ 1,000 Pdf。
+ 六页每个。
+ 每个页面 （6,000 映像） 的一个映像。
+ 每页的 3,000 个字符。

假设管道包含的每个 PDF、 图像和文本提取，光学字符识别 (OCR) 的映像，文档破解和实体识别的组织。

在本文中所示的价格是假设索引。 它们用于说明估算过程。 你的成本可能较低。 有关事务的实际价格，请参阅，请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 破解包含文本和图像内容的文档时，文本提取目前是免费的。 对于 6,000 映像，假定每 1,000 张图像中提取 1 美元。 这是成本为 $6.00 用于此步骤。

2. 对于英语环境中 6000 个图像的 OCR，OCR 认知技能使用最佳算法 (DescribeText)。 假设每分析 1,000 个图像的成本为 $2.50，则这一步需要支付 $15.00。

3. 用于实体提取，必须每页的三个文本记录的总数。 每条记录包含 1,000 个字符。 每页乘以 6,000 页面的三个文本记录等于 18000 文本记录。 假设 1000 个文本记录的成本为 $2.00，则这一步的成本为 $36.00。

将所有组合在一起，您需要支付大约 $57.00 引入 1,000 个 PDF 文档的此类型与所述的技能组合。

## <a name="next-steps"></a>后续步骤
+ [Azure 搜索定价页](https://azure.microsoft.com/pricing/details/search/)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)

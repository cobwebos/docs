---
title: 将认知服务附加到技能集 - Azure 搜索
description: 说明如何将认知服务一体化订阅附加到认知技能集
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7f604d1b94e51db11e6d6992b7f070d64ae869dd
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317230"
---
# <a name="associate-cognitive-services-resource-with-a-skillset"></a>将认知服务资源与技能集相关联 

> [!NOTE]
> 自 2018 年 12 月 21 日起，你将能够将认知服务资源与 Azure 搜索技能集进行关联。 这将使我们能够开始收取技能集执行的费用。 在这一天，我们还会开始将图像提取作为文档破解阶段的一部分进行收费。 我们将继续提供文档文本提取服务（不收取额外费用）。
>
> 内置技能的执行将按现有[认知服务的即付即用价格](https://azure.microsoft.com/pricing/details/cognitive-services/)进行收费。 我们将按预览版定价收取图像提取费用，详见 [Azure 搜索定价页面](https://go.microsoft.com/fwlink/?linkid=2042400)。


认知搜索可提取和扩充数据，使之能够在 Azure 搜索中可供搜索。 我们将提取和扩充步骤称作认知技能。 在进行内容索引的过程中调用的技能的集合在技能集中定义。 技能集可以使用[预定义技能](cognitive-search-predefined-skills.md)或自定义技能（有关详细信息，请参阅[示例：创建自定义技能](cognitive-search-create-custom-skill-example.md)）。

本文介绍如何将[认知服务](https://azure.microsoft.com/services/cognitive-services/)资源关联到认知技能集。

所选认知服务资源会强化内置认知技能。 此资源也会用于计费目的。 使用内置认知技能执行的任何扩充都会根据所选认知服务资源收费。 它们的费率与使用认知服务资源执行同一任务的费率相同。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/)。

## <a name="limits-when-no-cognitive-services-resource-is-selected"></a>不选择认知服务资源情况下的限制
自 2019 年 2 月 1 日起，如果不将认知服务订阅与技能集相关联，则只能免费扩充少量文档（每天 20 个文档）。 

## <a name="associating-a-cognitive-services-resource-with-a-new-skillset"></a>将认知服务资源与新技能集相关联

1. 在“导入数据”体验过程中，在连接到数据源以后，需导航到“添加认知搜索”可选步骤。 

1. 展开“附加认知服务”部分。 这样会显示搜索服务所在区域中的认知服务资源。 
![展开的“附加认知服务”](./media/cognitive-search-attach-cognitive-services/attach1.png "展开的“附加认知服务”")

1. 选择现有的认知服务资源，或者创建新的认知服务资源。 如果选择“免费(有限扩充)资源”，则只能免费扩充少量文档（每天 20 个文档）。 如果单击了“创建新的认知服务资源”，则会打开新的用于创建认知服务资源的选项卡。 

1. 如果创建了某个新的资源，请单击“刷新”对认知服务资源列表进行刷新，然后选择该资源。 
![所选认知服务资源](./media/cognitive-search-attach-cognitive-services/attach2.png "所选认知服务资源")

1. 完成此操作以后，即可展开“添加扩充”部分，选择要对数据运行的特定认知技能，然后继续完成流程的其余部分。

## <a name="associating-a-cognitive-services-resource-with-an-existing-skillset"></a>将认知服务资源与现有技能集相关联

1. 在“服务概览”页上，选择“技能集”选项卡。![“技能集”选项卡](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "“技能集”选项卡")

1. 单击要修改的技能集。 此时会打开一个用于编辑技能集的边栏选项卡。

1. 选择现有的认知服务资源，或者创建新的认知服务资源。 如果选择“免费(有限扩充)资源”，则只能免费扩充少量文档（每天 20 个文档）。 如果单击了“创建新的认知服务资源”，则会打开新的用于创建认知服务资源的选项卡。 <n/> 
<img src="./media/cognitive-search-attach-cognitive-services/attach-existing2.png" width="350">

1. 如果创建了某个新的资源，请单击“刷新”对认知服务资源列表进行刷新，然后选择该资源。
1. 单击“确定”，确认所做的更改

## <a name="associating-a-cognitive-services-resource-programmatically"></a>以编程方式关联认知服务资源

以编程方式定义此技能集时，请添加 `cognitiveServices` 节。 此节应该包括要与技能集相关联的认知服务资源的键，以及包括应该设置为 "#Microsoft.Azure.Search.CognitiveServicesByKey" 的 @odata.type。 以下示例中显示了此模式。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```
```json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey"
        "description": "mycogsvcs",
        "key": "your key goes here"
    }
}
```
## <a name="example-estimating-the-cost-of-document-cracking-and-enrichment"></a>示例：估算文档破解和扩充的成本
可能需要估算扩充给定类型的文档的具体成本。 下面的练习只是一个示例，但可能对你有帮助。

假设我们有 1000 个 PDF，并且估计这些文档平均每个有 6 个页面。 假设在本练习中，每个文档的每个页面有一个图像。 另外假设平均起来每个页面有大约 3,000 个字符。 

现在，假设我们需要在完成扩充管道的过程中执行以下步骤：
1. 在文档破解过程中，从文档提取内容和图像。
1. 在扩充过程中，对提取的每个页面执行 OCR 操作，将所有页面的文本组合起来，然后在所有图像的组合文本中提取每个组织。

让我们一步步地估算一下，引入这些文档的成本是多少。

对于这 1000 个文档：

1. 进行文档破解时，我们会提取总共 6,000 个图像。 假设每提取 1000 个图像需要 $1，则所有图像的成本为 $6.00。

2. 这 6,000 个图像中，每一个都需要提取文本。 在英语中，OCR 认知技能使用最佳算法 (DescribeText)。 假设每分析 1,000 个图像的成本为 $2.50，则这一步需支付 $15.00。

3. 进行实体提取时，每页总共有 3 个文本记录（每个记录 1,000 个字符）。 3 文本记录/页 * 6,000 页 = 18,000 文本记录。 假设 1000 个文本记录的成本为 $2.00，则这一步的成本为 $36.00。

综合起来，在使用上述技能集引入 1000 个这种性质的 pdf 文档时，需支付 $57.00。  在此练习中，我们假定的是单个交易的最贵价格，但如果考虑到阶梯定价，实际价格会更低。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services)。



## <a name="next-steps"></a>后续步骤
+ [Azure 搜索定价页](https://azure.microsoft.com/pricing/details/search/)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)

---
title: 将认知服务资源与技能集相关联 - Azure 搜索
description: 说明如何将认知服务一体化订阅附加到 Azure 搜索中的认知扩充管道。
manager: cgronlun
author: LuisCabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 509125e7c93f34b9ce28c58cb1ec96db1074d995
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119639"
---
# <a name="associate-a-cognitive-services-resource-with-a-skillset-in-azure-search"></a>将认知服务资源与 Azure 搜索中的技能集相关联 

驱动[认知搜索](cognitive-search-concept-intro.md)管道处理非结构化数据的 AI 算法基于[**认知服务资源**](https://azure.microsoft.com/services/cognitive-services/)。 例如，[**计算机视觉**](https://azure.microsoft.com/services/cognitive-services/computer-vision/)资源提供图像分析和光学字符识别 (OCR) 用于从图像文件中提取文本和结构，而[**文本分析**](https://azure.microsoft.com/services/cognitive-services/text-analytics/)资源则提供自然语言处理，如实体识别、关键短语提取，等等。

可以免费扩充有限数量的文档，或者附加付费的认知服务资源来处理更大、更频繁的工作负荷。 本文介绍如何将认知服务资源关联到认知技能集，以便在编制索引期间扩充数据。

如果管道仅由[自定义技能](cognitive-search-create-custom-skill-example.md)组成，则不需要附加认知服务资源。

> [!NOTE]
> 自 2018 年 12 月 21 日起，可将认知服务资源与 Azure 搜索技能集进行关联。 这样，我们就可以开始收取技能集的执行费。 在此日期，我们还会开始将图像提取视为文档破解阶段的一部分进行计费。 我们将继续提供文档文本提取服务而不收取额外费用。
>
> 执行[内置认知技能](cognitive-search-predefined-skills.md)将按[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)进行计费，其费率与以往独立于 Azure 搜索执行任务的费率相同。 图像提取费用将按预览版定价进行计费，详见 [Azure 搜索定价页面](https://go.microsoft.com/fwlink/?linkid=2042400)。


## <a name="use-free-resources"></a>使用免费资源

可以使用有限的免费处理选项每日扩充 20 个文档，这足以完成认知搜索教程和快速入门练习。 

> [!Important]
> 从 2019 年 2 月 1 日开始，“免费(有限扩充)”选项限制为每日 20 个文档。 

1. 打开“导入数据”向导。

   ![导入数据命令](media/search-get-started-portal/import-data-cmd2.png "导入数据命令")

1. 选择数据源，然后转到“添加认知搜索(可选)”。 有关此向导的分步演练，请参阅[使用门户工具进行导入、索引编制和查询](search-get-started-portal.md)。

1. 展开“附加认知服务”，然后选择“免费(有限扩充)”。

   ![展开的“附加认知服务”部分](./media/cognitive-search-attach-cognitive-services/attach1.png "展开的“附加认知服务”")

转到下一步骤“添加扩充”。 有关门户中提供的技能的说明，请参阅认知搜索快速入门中的[步骤 2：添加认知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)。

## <a name="use-billable-resources"></a>使用付费资源

对于每日处理 20 个以上文档的工作负荷，需要使用付费的认知服务资源。

1. 在“导入数据”向导的“附加认知服务”中选择现有资源，或单击“创建新的认知服务资源”。

1. 如果选择“创建新的认知服务资源”，则会打开一个新的选项卡用于创建资源。 为资源指定唯一名称。

1. 选择 Azure 搜索所在的同一位置。 目前，以下区域支持认知技能索引编制：

  * 美国中西部
  * 美国中南部
  * 美国东部
  * 美国东部 2
  * 美国西部 2
  * 加拿大中部
  * 西欧
  * 英国南部
  * 北欧
  * 巴西南部
  * 东南亚
  * 印度中部
  * 澳大利亚东部

1. 选择一体化定价层 **S0**。 此层提供视觉和语言功能，认知搜索中的预定义技能基于这些功能。

    ![创建新的认知服务资源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "创建新的认知服务资源")

1. 单击“创建”预配新的认知服务资源。 

1. 返回到包含“导入数据”向导的上一选项卡。 单击“刷新”显示该认知服务资源，然后选择该资源。

   ![选择的认知服务资源](./media/cognitive-search-attach-cognitive-services/attach2.png "选择的认知服务资源")

1. 展开“添加扩充”部分，选择要对数据运行的特定认知技能，然后继续完成余下的流程。 有关门户中提供的技能的说明，请参阅认知搜索快速入门中的[步骤 2：添加认知技能](cognitive-search-quickstart-blob.md#create-the-enrichment-pipeline)。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>将现有技能集附加到认知服务资源

如果你有现有的技能集，可将其附加到新的或不同的认知服务资源。

1. 在“服务概述”页上，单击“技能集”。

   ![“技能集”选项卡](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "“技能集”选项卡")

1. 单击技能集的名称，然后选择现有资源或新建资源。 单击“确定”以确认所做的更改。 

   ![技能集资源列表](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能集资源列表")

如前所述，“免费(有限扩充)”选项限制为每日 20 个文档，“创建新的认知服务资源”用于预配新的付费资源。 如果创建了新资源，请选择“刷新”以刷新认知服务资源的列表，然后选择该资源。

## <a name="attach-cognitive-services-programmatically"></a>以编程方式附加认知服务

以编程方式定义技能集时，请将 `cognitiveServices` 节添加到该技能集。 在该节中，包含要与该技能集关联的认知服务资源的键。 如前所述，该资源必须位于 Azure 搜索所在的同一区域。 另外请包含 `@odata.type`，并将其设置为 `#Microsoft.Azure.Search.CognitiveServicesByKey`。 

以下示例演示了此模式。 请注意定义底部的 cognitiveServices 节

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
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>示例：估算成本

若要估算认知搜索索引编制的相关成本，请先构想一下文档的大致结构，以便能够得出一些数字。 例如，使用以下似近值估算成本：

+ 1000 个 PDF
+ 每个 PDF 有 6 个页面
+ 每个页面包含一个图像（共 6000 个图像）
+ 每个页面包含 3000 个字符

假设管道的功能包括：使用图像和文本提取破解每个 PDF，使用光学字符识别 (OCR) 处理图像，以及组织的命名实体识别。 

在此练习中，我们将为每笔交易应用最贵的价格。 考虑到阶梯定价，实际成本可能更低。 请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 破解包含文本和图像内容的文档时，文本提取目前是免费的。 对于 6000 个图像，假设每提取 1000 个图像需要 $1，则此步骤的成本是 $6.00。

2. 对于英语环境中 6000 个图像的 OCR，OCR 认知技能使用最佳算法 (DescribeText)。 假设每分析 1,000 个图像的成本为 $2.50，则这一步需要支付 $15.00。

3. 提取实体时，每页总共有 3 个文本记录。 每条记录包含 1,000 个字符。 每页 3 个文本记录 * 6,000 页 = 18,000 个文本记录。 假设 1000 个文本记录的成本为 $2.00，则这一步的成本为 $36.00。

综合起来，在使用上述技能集引入 1,000 个这种性质的 PDF 文档时，需要支付大约 $57.00。 

## <a name="next-steps"></a>后续步骤
+ [Azure 搜索定价页](https://azure.microsoft.com/pricing/details/search/)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)

---
title: 使用文本分析和 Power Automate 在 Excel 中提取信息
titleSuffix: Azure Cognitive Services
description: 了解如何通过文本分析和 Power Automate 在不编写代码的情况下提取 Excel 文本。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: b67de07777fa3f4f2b6190d8b003eb0495e66d15
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400479"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>使用文本分析和 Power Automate 在 Excel 中提取信息 

本教程将创建 Power Automate 流，以便在 Excel 电子表格中提取文本，而无需编写代码。 

此流将获取有关公寓大楼的问题的电子表格，并将其分为两个类别：管道和其他。 它还将提取发送问题的租户的姓名和电话号码。 最后，此流将此信息附加到 Excel 工作表。 

在本教程中，你将学习如何执行以下操作：

> [!div class="checklist"]
> * 使用 Power Automate 创建流
> * 从 OneDrive for Business 上传 Excel 数据
> * 从 Excel 提取文本，并将其发送到文本分析 API 
> * 使用 API 中的信息更新 Excel 工作表。

## <a name="prerequisites"></a>先决条件

- 一个 Microsoft Azure 帐户。 [创建免费帐户](https://azure.microsoft.com/free/cognitive-services/)或[登录](https://portal.azure.com/)。
- 文本分析资源。 如果没有，可以[在 Azure 门户中创建一个](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)，并使用免费层完成本教程。
- 注册期间为你生成的[密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
- 包含租户问题的电子表格。 示例数据在 GitHub 上提供
- Microsoft 365，包含 OneDrive for business。

## <a name="add-the-excel-file-to-onedrive-for-business"></a>将 Excel 文件复制到 OneDrive for Business

从 [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx) 下载示例 Excel 文件。 此文件必须存储在 OneDrive for Business 帐户。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 文件中的示例。":::

问题以原始文本报告。 将使用文本分析 API 的命名实体识别来提取人名和电话号码。 然后该流将在描述中查找“管道”一词，以对问题进行分类。 

## <a name="create-a-new-power-automate-workflow"></a>创建新的 Power Automate 工作流

转到 [Power Automate 站点](https://preview.flow.microsoft.com/)并登录。  单击“创建”，然后单击“计划流”。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="流创建屏幕。":::


在“生成计划流”页上，使用以下字段初始化流：

|字段 |值  |
|---------|---------|
|**流名称**     | 计划的审查或其他名称。         |
|**正在启动**     |  输入当前日期和时间。       |
|**重复间隔**     | 1 小时        |

## <a name="add-variables-to-the-flow"></a>向流添加变量

> [!NOTE]
> 若要查看已完成流的图像，可从 [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams) 下载它。 

创建表示将添加到 Excel 文件的信息的变量。  单击“新建步骤”并搜索“初始化变量”。 执行此操作四次，创建四个变量。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="初始化变量。":::

将以下信息添加到创建的变量中。 它们表示 Excel 文件的列。 如果任何变量已折叠，可以单击它们将其展开。

| 操作 |名称   | 类型 | 值 |
|---------|---------|---|---|
| 初始化变量 | var_person | 字符串 | 人员 |
| 初始化变量 2 | var_phone | 字符串 | Phone_Number |
| 初始化变量 3 | var_plumbing | 字符串 | 管道 |
| 初始化变量 4 | var_other | 字符串 | 其他 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="流变量中包含的信息":::

## <a name="read-the-excel-file"></a>读取 Excel 文件

  单击“新建步骤”，键入“Excel”，然后从操作列表中选择“列出表中显示的行”。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="添加 Excel 行。":::

通过填写此操作中的字段，将 Excel 文件添加到流。 本教程要求文件已上传到 OneDrive for Business。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="添加 Excel 行。":::

 单击“新建步骤”，添加“应用到每一个”操作。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="添加应用命令。":::

单击“从先前的步骤中选择一个输出”。 在显示的动态内容框中选择“值”。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="从 Excel 文件选择输出。":::

## <a name="send-a-request-to-the-text-analytics-api"></a>将请求发送到文本分析 API

如果尚未这样做，需要在 Azure 门户中创建[文本分析资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。

### <a name="create-a-text-analytics-connection"></a>创建文本分析连接

 在“应用到每一个”中单击“添加操作”。 转到 Azure 门户中文本分析资源的“密钥和终结点”页，获取文本分析资源的密钥和终结点。

在流中输入以下信息以创建新的文本分析连接。

> [!NOTE]
> 如果已创建文本分析连接并想要更改连接详细信息，请单击右上角的省略号，然后单击“+ 添加新连接”。

| 字段           | 值                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 连接名称 | 文本分析资源的连接的名称。 例如 `TAforPowerAutomate`。 |
| 帐户密钥     | 文本分析资源的密钥。                                                                                   |
| 站点 URL        | 文本分析资源的终结点。                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="将文本分析凭据添加到流。":::

## <a name="extract-the-excel-content"></a>提取 Excel 内容 

 创建连接后，搜索“文本分析”并选择“实体”。 这将从问题的说明列中提取信息。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="将文本分析凭据添加到流。":::

 单击“文本”字段，然后从显示的“动态内容”窗口中选择“说明”。 为“语言”输入 `en`。 （如果看不到“语言”，请单击“显示高级选项”）

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="将文本分析凭据添加到流。":::


## <a name="extract-the-person-name"></a>提取人名

接下来，我们将在文本分析输出中查找人员实体类型。   在“应用到每一个”中单击“添加操作”，并创建另一个“应用到每一个”操作。 在文本框内单击，然后在出现的“动态内容”窗口中选择“实体”。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="将文本分析凭据添加到流。":::

  在新创建的“应用到每一个 2”操作中，单击“添加操作”并添加“条件”控件。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="将文本分析凭据添加到流。":::

在“条件”窗口中，单击第一个文本框。 在“动态内容”窗口中搜索“实体类型”并选择它。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="将文本分析凭据添加到流。":::

确保第二个框设为“等于”。 然后选择第三个框，并在“动态内容”窗口中搜索 `var_person`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="将文本分析凭据添加到流。":::

 在“如果是”条件中，在 Excel 中键入并选择“更新行”。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="将文本分析凭据添加到流。":::

  输入 Excel 信息，并更新“键列”、“键值”和“人名”字段。 这将 API 检测到的名字附加到 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="将文本分析凭据添加到流。":::

## <a name="get-the-phone-number"></a>获取电话号码

通过单击名称，最小化“应用到每一个 2”操作。 然后像之前那样，添加另一个“应用到每一个”操作。 它将命名为“应用到每一个 3”。 选择文本框，并添加“实体”作为此操作的输出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="将文本分析凭据添加到流。":::

 在“应用到每一个 3”中添加“条件”控件。 它将命名为“条件 2”。 在第一个文本框中，从“动态内容”窗口中搜索并添加“实体类型”。 确保第中间框设为“等于”。 然后在右侧文本框中输入 `var_phone`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="将文本分析凭据添加到流。":::

 在“如果是”条件中添加“更新行”操作。 然后像之前那样输入 Excel 工作表电话号码列的信息。 这将 API 检测到的电话号码附加到 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="将文本分析凭据添加到流。":::


## <a name="get-the-plumbing-issues"></a>获取管道问题

通过单击名称，最小化“应用到每一个 3”操作。 然后在父操作中创建另一个“应用到每一个”操作。 选择文本框，从“动态内容”窗口添加“实体”作为此操作的输出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="将文本分析凭据添加到流。":::


接下来，流将检查 Excel 表行中的问题说明是否包含“管道”一词。 如果是，它将在“问题类型”列中添加“管道”。 如果不是，我们将输入“其他”。

在“应用到每一个 4”操作中添加“条件”控件 。 它将命名为“条件 3”。 在第一个文本框中，使用“动态内容”窗口搜索并添加 Excel 文件中的“说明”。 确保中间框显示“包含”。 然后在右侧文本框中查找并选择 `var_plumbing`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="将文本分析凭据添加到流。":::


  在“如果是”条件中单击“添加操作”，然后选择“更新行”。 然后像之前那样输入信息。 在“问题类型”列中选择 `var_plumbing`。 这将向行应用“管道”标签。

  在“如果不是”条件中单击“添加操作”，并选择“更新行”。 然后像之前那样输入信息。 在“问题类型”列中选择 `var_other`。 这将向行应用“其他”标签。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="将文本分析凭据添加到流。":::

## <a name="test-the-workflow"></a>测试工作流

 在屏幕右上角，依次单击“保存”和“测试”。 选择“我将执行触发器操作”。   依次单击“保存并测试”、“运行流”、“完成”。

Excel 文件将在 OneDrive 帐户中更新。 它看起来如下。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="已更新的 Excel 电子表格。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解更多解决方案](../text-analytics-user-scenarios.md)

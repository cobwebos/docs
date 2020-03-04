---
title: 使用文本分析和电源自动功能在 Excel 中提取信息
titleSuffix: Azure Cognitive Services
description: 了解如何在不编写代码的情况下提取 Excel 文本，使用文本分析和电源自动化。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201184"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>使用文本分析和电源自动功能在 Excel 中提取信息 

在本教程中，您将创建一个电源自动流，以便在 Excel 电子表格中提取文本，而无需编写代码。 

此流程将获取有关单元复杂情况所报告的问题的电子表格，并将其分类为两个类别： "管道" 和 "其他"。 它还将提取发送它们的租户的姓名和电话号码。 最后，此流程会将此信息追加到 Excel 工作表。 

本教程介绍以下操作：

> [!div class="checklist"]
> * 使用 Power 自动化创建流
> * 从 OneDrive for Business 上传 Excel 数据
> * 从 Excel 中提取文本，并将其发送到文本分析 API 
> * 使用 API 中的信息更新 Excel 工作表。

## <a name="prerequisites"></a>必备条件

- 一个 Microsoft Azure 帐户。 [启动免费试用版](https://azure.microsoft.com/free/)或[登录](https://portal.azure.com/)。
- 文本分析资源。 如果没有，可以[在 Azure 门户中创建一个](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)，并使用免费层完成本教程。
- 注册过程中生成的[密钥和终结点](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。
- 包含租户问题的电子表格。 GitHub 上提供的示例数据
- Office 365，包含 OneDrive for Business。

## <a name="add-the-excel-file-to-onedrive-for-business"></a>将 Excel 文件添加到 OneDrive for business

从[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx)下载示例 Excel 文件。 此文件必须存储在 OneDrive for Business 帐户中。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Excel 文件中的示例。":::

问题在原始文本中报告。 我们将使用文本分析 API 的命名实体识别来提取人员姓名和电话号码。 然后，流将在描述中查找 "检测" 一词，以对问题进行分类。 

## <a name="create-a-new-power-automate-workflow"></a>创建新的 Power 自动化工作流

中转到 " [Power 自动执行" 站点](https://preview.flow.microsoft.com/)，并登录。 然后单击 "**创建**并**安排流**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="流创建屏幕。":::


在 "**生成计划的流**" 页上，使用以下字段初始化流：

|字段 |值  |
|---------|---------|
|**流名称**     | **计划的审阅**或其他名称。         |
|**正在启动**     |  输入当前日期和时间。       |
|**重复间隔**     | 1 小时        |

## <a name="add-variables-to-the-flow"></a>向流中添加变量

> [!NOTE]
> 若要查看已完成流的映像，可以从[GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams)下载。 

创建表示将添加到 Excel 文件的信息的变量。 单击 "**新建步骤**"，然后搜索 "**初始化变量**"。 执行此操作四次，创建四个变量。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="初始化变量。":::

将以下信息添加到您创建的变量中。 它们表示 Excel 文件的列。 如果任何变量已折叠，你可以单击它们以将其展开。

| 操作 |名称   | 类型 | 值 |
|---------|---------|---|---|
| 初始化变量 | var_person | String | 人员 |
| 初始化变量2 | var_phone | String | Phone_Number |
| 初始化变量3 | var_plumbing | String | 管线 |
| 初始化变量4 | var_other | String | 其他 | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="流变量中包含的信息":::

## <a name="read-the-excel-file"></a>读取 excel 文件

单击 "**新建步骤**"，键入 " **Excel**"，然后从操作列表中选择 "**列出表中存在的行**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="添加 excel 行。":::

通过填写此操作中的字段，将 Excel 文件添加到流中。 本教程要求文件已上传到 OneDrive for business。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="添加 excel 行。":::

单击 "**新建步骤**"，并添加 "**应用到每个**" 操作。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="添加 apply 命令。":::

单击 "**选择上一步中的输出**"。 在出现的 "动态内容" 框中，选择 "**值**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="从 excel 文件中选择 输出。":::

## <a name="send-a-request-to-the-text-analytics-api"></a>向文本分析 API 发送请求

如果尚未这样做，则需要在 Azure 门户中创建[文本分析资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)。

### <a name="create-a-text-analytics-connection"></a>创建文本分析连接

在 "**应用到每个**" 中，单击 "**添加操作**"。 转到 Azure 门户中的文本分析资源的 "**密钥" 和 "终结点**" 页，获取文本分析资源的密钥和终结点。

在流中，输入以下信息以创建新的文本分析连接。

> [!NOTE]
> 如果已创建文本分析连接并想要更改连接详细信息，请单击右上角的省略号，并单击 " **+ 添加新连接**"。

| 字段           | 值                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| 连接名称 | 与文本分析资源的连接的名称。 例如，`TAforPowerAutomate` 。 |
| 帐户密钥     | 文本分析资源的键。                                                                                   |
| 站点 URL        | 文本分析资源的终结点。                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="将文本分析凭据添加到流中。":::

## <a name="extract-the-excel-content"></a>提取 excel 内容 

创建连接后，搜索 "**文本分析**" 并选择 "**实体**"。 这将从问题的 "说明" 列中提取信息。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="将文本分析凭据添加到流中。":::

在**文本**字段中单击，并从显示的动态内容窗口中选择 "**说明**"。 输入语言 `en`。 （如果看不到语言，请单击 "显示高级选项"）

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="将文本分析凭据添加到流中。":::


## <a name="extract-the-person-name"></a>提取人员姓名

接下来，我们将在文本分析输出中找到 person 实体类型。 在 "**应用到每个**" 中，单击 "**添加操作**"，然后创建另一个 "**应用于每个**操作"。 在文本框内单击，然后在显示的动态内容窗口中选择 "**实体**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="将文本分析凭据添加到流中。":::

在新创建的 "**应用于每个 2** " 操作中，单击 "**添加操作**"，然后添加**条件**控件。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="将文本分析凭据添加到流中。":::

在 "条件" 窗口中，单击第一个文本框。 在 "动态内容" 窗口中，搜索 "**实体类型**" 并选择它。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="将文本分析凭据添加到流中。":::

确保第二个框的设置为**等于**。 然后选择第三个框，在动态内容窗口中搜索 `var_person`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="将文本分析凭据添加到流中。":::

在 "**如果是"** 条件下，在 Excel 中键入，然后选择 "**更新行**"。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="将文本分析凭据添加到流中。":::

输入 Excel 信息，并更新 "**键列**"、"**键值**" 和 " **PersonName** " 字段。 这会将 API 检测到的名称附加到 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="将文本分析凭据添加到流中。":::

## <a name="get-the-phone-number"></a>获取电话号码

通过单击名称，最小化**应用于每个 2**操作。 然后，添加另一个 "**应用于每个**操作，例如之前"。 它将应用于**每个 3**。 选择文本框，然后添加**实体**作为此操作的输出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="将文本分析凭据添加到流中。":::

在 "**应用到每个**" 中，添加 "**条件**" 控件。 它名为**Condition 2**。 在第一个文本框中，搜索并从动态内容窗口中添加**实体类型**。 确保将中心框的设置为**等于**。 然后，在右侧的文本框中，输入 `var_phone`。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="将文本分析凭据添加到流中。":::

在 "**如果是"** 条件下，添加 "**更新行**" 操作。 然后在 Excel 工作表的 "电话号码" 列中输入上述信息，如上文所示。 这会将 API 检测到的电话号码追加到 Excel 工作表。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="将文本分析凭据添加到流中。":::


## <a name="get-the-plumbing-issues"></a>获取管道问题

通过单击名称，最小化**应用于每个 3** 。 然后，在父操作中创建另一个**应用于每个**。 选择文本框，然后从动态内容窗口中添加**实体**作为此操作的输出。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="将文本分析凭据添加到流中。":::


接下来，流将检查 Excel 表行中的问题说明是否包含 "管道" 一词。 如果是，它将在 IssueType 列中添加 "检测"。 如果没有，我们将输入 "其他"。

在 "**应用到每个 4** " 操作中，添加 "**条件**" 控件。 它将被命名为**条件 3**。 在第一个文本框中，使用动态内容窗口搜索，并从 Excel 文件添加**说明**。 请确保中心框显示 "**包含**"。 然后，在右侧的文本框中，查找并选择 "`var_plumbing`"。 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="将文本分析凭据添加到流中。":::


在 "**如果是"** 条件下，单击 "**添加操作**"，然后选择 "**更新行**"。 然后输入与之前类似的信息。 在 IssueType 列中，选择 `var_plumbing`。 这会将 "管道" 标签应用到该行。

在 "**如果没有**条件" 中单击 "**添加操作**"，然后选择 "**更新行**"。 然后输入与之前类似的信息。 在 IssueType 列中，选择 `var_other`。 这会将 "其他" 标签应用于该行。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="将文本分析凭据添加到流中。":::

## <a name="test-the-workflow"></a>测试工作流

在屏幕的右上角，单击 "**保存**"，然后单击 "**测试**"。 选择**我将执行触发器操作**。 单击 "**保存 & 测试**"、"**运行流**"，然后单击 "**完成**"。

Excel 文件将在你的 OneDrive 帐户中更新。 它将如下所示。

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="已更新的 excel 电子表格。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解更多解决方案](../text-analytics-user-scenarios.md)

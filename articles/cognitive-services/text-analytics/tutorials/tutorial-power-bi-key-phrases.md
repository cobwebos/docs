---
title: 教程：将 Power BI 与文本分析认知服务集成
titleSuffix: Azure Cognitive Services
description: 了解如何使用文本分析从存储在 Power BI 中的文本提取关键短语。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: tutorial
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 4489fc82f836d8c311fcd776e211670897618b54
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889471"
---
# <a name="tutorial-integrate-power-bi-with-the-text-analytics-cognitive-service"></a>教程：将 Power BI 与文本分析认知服务集成

Microsoft Power BI Desktop 是免费的应用程序，可让你连接、转换和可视化你的数据。 文本分析服务是 Microsoft Azure 认知服务的一部分，它提供自然语言处理。 给定原始的非结构化文本，它可以提取最重要的短语、分析情绪和确定已知实体（例如品牌）。 可以综合使用这些工具快速了解客户谈论的内容和客户的感受。

本教程介绍以下操作：

> [!div class="checklist"]
> * 使用 Power BI Desktop 导入和转换数据
> * 在 Power BI Desktop 中创建自定义函数
> * 将 Power BI Desktop 与文本分析关键短语 API 集成
> * 使用文本分析关键短语 API 从客户反馈中提取最重要的短语
> * 从客户反馈创建词云

## <a name="prerequisites"></a>先决条件
<a name="Prerequisites"></a>

- Microsoft Power BI Desktop。 [免费下载](https://powerbi.microsoft.com/get-started/)。
- 一个 Microsoft Azure 帐户。 [启动免费试用版](https://azure.microsoft.com/free/)或[登录](https://portal.azure.com/)。
- 包含文本分析 API 的认知服务 API 帐户。 如果没有帐户，可以[注册](../../cognitive-services-apis-create-account.md)并使用 5,000 个事务/月的免费层级（请参阅[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)以完成本教程）。
- 在注册期间生成的[文本分析访问密钥](../how-tos/text-analytics-how-to-access-key.md)。
- 客户评论。 可以使用[我们的示例数据](https://aka.ms/cogsvc/ta)或你自己的数据。 本教程假定你使用我们的示例数据。

## <a name="load-customer-data"></a>加载客户数据
<a name="LoadingData"></a>

打开 Power BI Desktop 并加载你在[先决条件](#Prerequisites)中下载的逗号分隔值 (CSV) 文件 `FabrikamComments.csv` 即可开始。 此文件代表某个虚构小公司的支持论坛中一天的虚构活动。

> [!NOTE]
> Power BI 可以使用各种源（例如 Facebook 或 SQL 数据库）提供的数据。 有关详细信息，请参阅 [Facebook 与 Power BI 集成](https://powerbi.microsoft.com/integrations/facebook/)和 [SQL Server 与 Power BI 集成](https://powerbi.microsoft.com/integrations/sql-server/)。

在 Power BI Desktop 主窗口中，选择“开始”功能区。 在功能区的“外部数据”组中，打开“获取数据”下拉菜单，然后选择“文本/CSV”。

![[“获取数据”按钮]](../media/tutorials/power-bi/get-data-button.png)

此时会显示“打开”对话框。 导航到“下载”文件夹，或你下载 `FabrikamComments.csv` 文件的文件夹。 单击“`FabrikamComments.csv`”，然后单击“打开”按钮。 此时会显示 CSV 导入对话框。

![[CSV 导入对话框]](../media/tutorials/power-bi/csv-import.png)

CSV 导入对话框用于验证 Power BI Desktop 是否已正确检测到字符集、分隔符、标头行和列类型。 这些信息全都正确，因此单击“加载”。

若要查看加载的数据，请单击 Power BI 工作区左侧的“数据视图”按钮。 将会打开一个包含数据的表，就像在 Microsoft Excel 中一样。

![[已导入数据的初始视图]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="prepare-the-data"></a>准备数据
<a name="PreparingData"></a>

你可能需要先在 Power BI Desktop 中转换数据，然后它才可准备好由文本分析服务的关键短语 API 进行处理。

示例数据包含一个 `subject` 列和一个 `comment` 列。 使用 Power BI Desktop 中的“合并列”功能，你可以从这两个列（而非仅仅 `comment` 列）的数据中提取关键短语。

在 Power BI Desktop 中，选择“开始”功能区。 在“外部数据”组中，单击“编辑查询”。

![[“主页”功能区中的“外部数据”组]](../media/tutorials/power-bi/edit-queries.png)

选择窗口左侧“查询”列表中的 `FabrikamComments`（如果尚未选择）。

现在请选择表中的 `subject` 和 `comment` 列。 可能需要进行水平滚动才能看到这些列。 首先单击 `subject` 列标题，然后在按住 Ctrl 键的同时单击 `comment` 列标题。

![[选择要合并的字段]](../media/tutorials/power-bi/select-columns.png)

选择“转换”功能区。 在功能区的“文本列”组中，单击“合并列”。 此时会显示“合并列”对话框。

![[使用“合并列”对话框合并字段]](../media/tutorials/power-bi/merge-columns.png)

在“合并列”对话框中，选择 `Tab` 作为分隔符，然后单击“确定”。

也可考虑使用“删除空白”筛选器筛选掉空白消息，或者使用“洁净转换”删除无法打印的字符。 如果数据包含的一个列类似于示例文件中的 `spamscore` 列，则可使用“数字筛选器”跳过“垃圾”评论。

## <a name="understand-the-api"></a>了解 API
<a name="UnderstandingAPI"></a>

对于每个 HTTP 请求，文本分析服务的[关键短语 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) 最多可以处理一千个文本文档。 Power BI 偏好一次处理一个记录，因此在本教程中，对 API 的每次调用将只包含一个文档。 对于每个要处理的文档，关键短语 API 要求提供以下字段。

| | |
| - | - |
| `id`  | 请求中此文档的唯一标识符。 响应也包含此字段。 这样，在处理多个文档时，就可以轻松地将提取的关键短语与所在文档相关联。 在本教程中，由于每个请求只处理一个文档，你可以将 `id` 值硬编码为针对每个请求均相同。|
| `text`  | 要处理的文本。 此字段的值来自于你在[先前部分](#PreparingData)中创建的 `Merged` 列，其包含合并的主题行和评论文本。 关键短语 API 要求该数据不得超出 5,120 个字符。|
| `language` | 表示撰写文档时所采用的自然语言的代码。 示例数据中的所有消息均为英语，因此你可以为此字段硬编码 `en` 值。|

## <a name="create-a-custom-function"></a>创建自定义函数
<a name="CreateCustomFunction"></a>

现在你可以创建集成 Power BI 与文本分析的自定义函数了。 此函数将要处理的文本作为参数接收。 它将数据转换为所需的 JSON 格式或从该格式进行转换，并对关键短语 API 发出 HTTP 请求。 然后该函数会分析来自 API 的响应，并返回包含所提取关键短语的逗号分隔列表的字符串。

> [!NOTE]
> Power BI Desktop 自定义函数是以 [Power Query M 公式语言](https://msdn.microsoft.com/library/mt211003.aspx)（简称“M”）编写的。 M 是基于 [F#](https://docs.microsoft.com/dotnet/fsharp/) 的函数编程语言。 不过，完成本教程不需要你是程序员；所需代码已在下面提供。

在 Power BI Desktop 中，确保你仍处于“查询编辑器”窗口。 如果并非如此，请选择“开始”功能区，然后在“外部数据”组中，单击“编辑查询”。

现在，在“开始”功能区的“新建查询”组中，打开“新建源”下拉菜单并选择“空白查询”。 

此时会在“查询”列表中显示新查询（初始名称为 `Query1`）。 双击此条目，将其命名为 `KeyPhrases`。

现在，在“开始”功能区的“查询”组中，单击“高级编辑器”以打开高级编辑器窗口。 删除该窗口中的现有代码，将以下代码粘贴到其中。 

> [!NOTE]
> 以下示例假定文本分析 API 终结点以 `https://westus.api.cognitive.microsoft.com` 开头。 文本分析允许你在 13 个不同的区域创建订阅。 如果注册了另一区域的服务，请确保使用所选区域的终结点。 你可以通过登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)、选择文本分析订阅并选择“概述”页面来找到此终结点。

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

将 `YOUR_API_KEY_HERE` 替换为你的文本分析访问密钥。 你还可以通过登录到 [Azure 门户](https://azure.microsoft.com/features/azure-portal/)、选择文本分析订阅并选择“概述”页面来找到此密钥。 请务必保留密钥前后的引号。 然后单击“完成”。

## <a name="use-the-custom-function"></a>使用自定义函数
<a name="UseCustomFunction"></a>

现在可以使用自定义函数从每个客户评论中提取关键短语并将其存储在表的新列中。 

在 Power BI Desktop 的查询编辑器窗口中，切换回 `FabrikamComments` 查询。 选择“添加列”功能区。 在“常规”组中，单击“调用自定义函数”。

![[“调用自定义函数”按钮]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

将出现“调用自定义函数”对话框。 在“新列名”中，输入 `keyphrases`。 在“函数查询”中，选择你创建的自定义函数 `KeyPhrases`。

对话框中将出现新字段“文本(可选)”。 此字段会询问我们希望使用哪一列来提供关键短语 API 的 `text` 参数的值。 （请记住，你已经硬编码了 `language` 和 `id` 参数的值。）从下拉菜单中选择 `Merged`（[此前](#PreparingData)通过合并主题和消息字段所创建的列）。

![[调用自定义函数]](../media/tutorials/power-bi/invoke-custom-function.png)

最后，单击“确定”。

如果一切准备就绪，Power BI 将针对表中的每一行调用自定义函数一次。 它将查询发送到关键短语 API，并为表添加一个新列来存储结果。 但在这些操作开始之前，可能需要指定身份验证和隐私设置。

## <a name="authentication-and-privacy"></a>身份验证和隐私
<a name="Authentication"></a>

在关闭“调用自定义函数”对话框之后，可能会出现一个横幅，要求你指定如何连接到关键短语 API。

![[凭据横幅]](../media/tutorials/power-bi/credentials-banner.png)

单击“编辑凭据”，确保选中对话框中的 `Anonymous`，然后单击“连接”。 

> [!NOTE]
> 你选择 `Anonymous` 是因为文本分析服务使用你的访问密钥对你进行身份验证，这样 Power BI 便不需要为 HTTP 请求自身提供凭据。

![[将身份验证设置为匿名]](../media/tutorials/power-bi/access-web-content.png)

如果在选择匿名访问后仍看到“编辑凭据”横幅，则可能是因为忘记将文本分析访问密钥粘贴到 `KeyPhrases` [自定义函数](#CreateCustomFunction)的代码中。

接下来可能会出现一个横幅，要求你提供有关数据源隐私的信息。 

![[隐私横幅]](../media/tutorials/power-bi/privacy-banner.png)

对于对话框中的每个数据源，请单击“继续”，然后选择 `Public`。 然后单击“保存”。

![[设置数据源隐私]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="create-the-word-cloud"></a>创建词云
<a name="WordCloud"></a>

处理完显示的横幅以后，请单击“主页”功能区中的“关闭并应用”，关闭查询编辑器。

Power BI Desktop 需要时间来发出必需的 HTTP 请求。 对于表中的每一行，新的 `keyphrases` 列都包含关键短语 API 在文本中检测到的关键短语。 

现在将使用此列生成一个词云。 首先，单击工作区左侧 Power BI Desktop 主窗口中的“报表”按钮。

> [!NOTE]
> 为何使用提取的关键短语而不是每个评论的完整文本来生成词云？ 关键短语提供的是客户评论中的重要词汇，而不仅仅是最常见词汇。 另外，生成的云中的单词大小调整不会因某个词在相对少数评论中的频繁使用而扭曲。

如果尚未安装词云自定义视觉对象，请安装它。 在工作区右侧的“可视化效果”面板中，单击三点形式的省略号 (**...**)，然后选择“从存储导入”。 然后搜索“云”并单击词云视觉对象旁边的“添加”按钮。 Power BI 会安装词云视觉对象并会让你知道它已成功安装。

![[添加自定义视觉对象]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

首先，单击“可视化效果”面板中的“词云”图标。

![[“可视化效果”面板中的“词云”图标]](../media/tutorials/power-bi/visualizations-panel.png)

此时会在工作区中显示新的报表。 将 `keyphrases` 字段从“字段”面板拖至“可视化效果”面板中的“类别”字段。 词云会显示在报表中。

现在，请切换到“可视化效果”面板的“格式”页面。 在“非索引字”类别中启用“默认非索引字”，以便从云中消除短的常用词，例如“of”。 

![[激活默认非索引字]](../media/tutorials/power-bi/default-stop-words.png)

在此面板中再往下稍微移动一点，禁用“旋转文本”和“标题”。

![[激活焦点模式]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

单击报表中的“焦点模式”工具可以更好地查看词云。 此工具可将词云展开，使之填充整个工作区，如下所示。

![[一个词云]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>更多文本分析服务
<a name="MoreServices"></a>

作为 Microsoft Azure 提供的认知服务之一，文本分析服务还提供情绪分析和语言检测功能。 语言检测尤其适用于客户反馈不完全是英语的情况。

这两个其他的 API 类似于关键短语 API。 这意味着可以使用与你在本教程中所创建的近乎相同的自定义函数，将它们与 Power BI Desktop 集成。 直接创建一个空白查询，然后将下面的相应代码粘贴到高级编辑器中，就像此前的操作那样。 （请勿忘记访问密钥！）然后，像以前那样，使用函数向表添加新列。

下面的情绪分析函数返回一个分数，指示文本中表达的情绪的正面程度。

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

下面是两个版本的语言检测函数。 第一个返回 ISO 语言代码（例如，表示英语的 `en`），而第二个则返回“友好”名称（例如 `English`）。 可以看到，这两个版本仅正文的最后一行有差异。

```fsharp
// Returns the two-letter language code (for example, 'en' for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (for example, 'English') of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

最后，下面是此前已提供的关键短语函数的变体，它返回的短语是列表对象，而不是单个字符串（包含逗号分隔的短语）。 

> [!NOTE]
> 返回单个字符串简化了词云示例。 而列表则是更灵活的格式，可以与 Power BI 中返回的短语配合使用。 可以使用查询编辑器的“转换”功能区中的“结构化列”组来操作 Power BI Desktop 中的列表对象。

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>后续步骤
<a name="NextSteps"></a>

详细了解文本分析服务、Power Query M 公式语言或 Power BI。

> [!div class="nextstepaction"]
> [文本分析 API 参考](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Power Query M reference](//msdn.microsoft.com/library/mt211003.aspx)（Power Query M 参考）

> [!div class="nextstepaction"]
> [Power BI 文档](//powerbi.microsoft.com/documentation/powerbi-landing-page/)

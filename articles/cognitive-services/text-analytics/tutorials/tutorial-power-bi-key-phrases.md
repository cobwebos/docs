---
title: 使用 Power BI 进行文本分析 - Azure 认知服务 | Microsoft Docs
description: 了解如何使用文本分析从存储在 Power BI 中的文本提取关键短语。
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889311"
---
# <a name="text-analytics-with-power-bi"></a>使用 Power BI 进行文本分析

Microsoft Power BI 根据组织数据生成美观的可以跨组织分发的报表，让用户可以更快获得更深入的见解。 文本分析服务是 Microsoft Azure 中的认知服务的一部分，可以通过其关键短语 API 从文本中提取最重要的短语。 可以综合使用这些工具快速了解客户谈论的内容和客户的感受。

本教程介绍如何集成 Power BI Desktop 和关键短语 API，以便使用自定义 Power Query 函数从客户反馈中提取最重要的短语。 我们还会根据这些短语创建词云。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要：

> [!div class="checklist"]
> * Microsoft Power BI Desktop。 [免费下载](https://powerbi.microsoft.com/get-started/)。
> * 一个 Microsoft Azure 帐户。 [启动免费试用版](https://azure.microsoft.com/free/)或[登录](https://portal.azure.com/)。
> * 文本分析的访问密钥。 先[注册](../../cognitive-services-apis-create-account.md)，然后[获取密钥](../how-tos/text-analytics-how-to-access-key.md)。
> * 客户评论。 [获取我们的示例数据](https://aka.ms/cogsvc/ta)，或者使用你自己的。

## <a name="loading-customer-data"></a>加载客户数据

打开 Power BI Desktop 并加载逗号分隔值 (CSV) 文件 **FabrikamComments.csv** 即可开始。 此文件代表某个虚构小公司的支持论坛中一天的虚构活动。

> [!NOTE]
> Power BI 可以使用各种源（例如 Facebook 或 SQL 数据库）提供的数据。 有关详细信息，请参阅 [Facebook 与 Power BI 集成](https://powerbi.microsoft.com/integrations/facebook/)和 [SQL Server 与 Power BI 集成](https://powerbi.microsoft.com/integrations/sql-server/)。

在 Power BI Desktop 主窗口中，找到“主页”功能区的“外部数据”组。 在该组的“获取数据”下拉菜单中选择“文本/CSV”。

![[“获取数据”按钮]](../media/tutorials/power-bi/get-data-button.png)

此时会显示“打开”对话框。 导航到 Downloads 文件夹或任何包含示例数据文件的文件夹。 单击“`FabrikamComments.csv`”，然后单击“打开”按钮。 此时会显示 CSV 导入对话框。

![[CSV 导入对话框]](../media/tutorials/power-bi/csv-import.png)

CSV 导入对话框用于验证 Power BI Desktop 是否已正确检测到字符集、分隔符、标头行和列类型。 这些信息全都正确，因此我们单击“加载”。

若要查看加载的数据，请使用 Power BI 工作区左侧的“数据视图”按钮切换到“数据”视图。 将会打开一个包含数据的表，就像在 Microsoft Excel 中一样。

![[已导入数据的初始视图]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>准备数据

在将数据提供给关键短语服务处理之前，可能需要先在 Power BI Desktop 中对数据进行转换。

例如，我们的数据包含 `subject` 字段和 `comment` 字段。 在提取关键短语时，这两个字段中的文本都应考虑，不能仅考虑 `comment` 中的文本。 可以使用 Power BI Desktop 中的“合并列”功能轻松完成此任务。

在“主页”功能区的“外部数据”组中单击“编辑查询”，在 Power BI Desktop 窗口中打开查询编辑器。 

![[“主页”功能区中的“外部数据”组]](../media/tutorials/power-bi/edit-queries.png)

选择窗口左侧“查询”列表中的“FabrikamComments”（如果尚未选择）。

现在请选择表中的 `subject` 和 `comment` 列。 可能需要进行水平滚动才能看到这些列。 首先单击 `subject` 列标题，然后在按住 Ctrl 键的同时单击 `comment` 列标题。

![[选择要合并的字段]](../media/tutorials/power-bi/select-columns.png)

在“转换”功能区的“文本列”组中，单击“合并列”。 此时会显示“合并列”对话框。

![[使用“合并列”对话框合并字段]](../media/tutorials/power-bi/merge-columns.png)

在“合并列”对话框中，选择 Tab 键作为分隔符，然后单击“确定”。 完成！

也可考虑使用“删除空白”筛选器筛选掉空白消息，或者使用“清除转换”删除无法打印的字符。 如果数据包含的一个列类似于示例文件中的 `spamscore` 列，则可使用“数字筛选器”跳过“垃圾”评论。

## <a name="understanding-the-api"></a>了解 API

对于每个 HTTP 请求，关键短语 API 最多可以处理一千个文本文档。 不过，Power BI 偏好一次处理一个记录，因此对 API 的调用只能包含一个文档。 对于每个要处理的文档，[此 API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) 要求提供以下字段。

| | |
| - | - |
| `id`  | 请求中此文档的唯一标识符。 响应也包含此字段。 这样，在处理多个文档时，就可以轻松地将提取的关键短语与所在文档相关联。 由于每次请求只处理一个文档，因此我们已经知道与响应相关联的文档，而 `id` 在每次请求中可以是一样的。|
| `text`  | 要处理的文本。 从已创建的 `Merged` 列获取，该列包含组合的主题行和评论文本。 关键短语 API 要求该数据不得超出 5,000 个字符。|
| `language` | 一种代码，表示撰写文档时采用的语言。 我们的所有消息都是英语，因此可将语言 `en` 硬编码到查询中。|

需将这些字段组合到 JSON（JavaScript 对象表示法）文档中，以便提交到关键短语 API。 此请求的响应也是 JSON 文档，我们必须对其进行分析并提取关键短语。

## <a name="creating-a-custom-function"></a>创建自定义函数

现在可以创建集成 Power BI 与文本分析的自定义函数了。 Power BI Desktop 针对表的每一行调用此函数，并根据结果创建新列。

此函数将要处理的文本作为参数接收。 它转换出入所需 JavaScript 对象表示法 (JSON) 的数据，并向关键短语 API 终结点发出 HTTP 请求。 在分析响应以后，此函数返回一个包含逗号分隔列表的字符串，列表的内容为所提取的关键短语。

> [!NOTE]
> Power BI Desktop 自定义函数是以 [Power Query M 公式语言](https://msdn.microsoft.com/library/mt211003.aspx)（简称“M”）编写的。 M 是基于 [F#](https://docs.microsoft.com/dotnet/fsharp/) 的函数编程语言。 不过，完成本教程不需要你是程序员；所需代码已在下面提供。

此时仍应在“查询编辑器”窗口中。 在“主页”功能区中单击“新建资源”（位于“新建查询”组中），然后在下拉菜单中选择“空白查询”。 

此时会在“查询”列表中显示新查询（一开始称为“Query1”）。 双击此条目，将其命名为 `KeyPhrases`。

现在请单击“主页”功能区的“查询”组中的“高级编辑器”，以便打开“高级编辑器”窗口。 删除该窗口中的现有代码，将以下代码粘贴到其中。 

> [!NOTE]
> 在下面的示例中，假定终结点位于 https://westus.api.cognitive.microsoft.com。  文本分析支持在 13 个不同的区域创建订阅。 如果注册了另一区域的服务，请确保使用所选区域的终结点。 它应该会在你选择文本分析订阅时显示在 Azure 门户的“概览”页中。

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

另请将从 Microsoft Azure 仪表板获得的文本分析 API 密钥粘贴到以 `apikey` 开头的行中。 （确保保留密钥前后的引号。）然后单击“完成”。

## <a name="using-the-function"></a>使用函数

现在可以使用自定义函数获取包含在每个客户评论中的关键短语并将其存储在表的新列中。 

此时仍在查询编辑器中，请切换回 FabrikamComments 查询，转到“添加列”功能区，然后单击“常规”组中的“调用自定义函数”按钮。

![[“调用自定义函数”按钮]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

在“调用自定义函数”对话框中，输入 `keyphrases` 作为新列的名称。 选择自定义函数 `KeyPhrases` 作为“函数查询”。 

此时会在对话框中显示一个新字段，询问我们需要将哪个字段传递给函数作为 `text` 参数。 从下拉菜单中选择 `Merged`（我们此前将 subject 字段和 message 字段合并后创建的列）。

![[调用自定义函数]](../media/tutorials/power-bi/invoke-custom-function.png)

最后，单击“确定”。

如果一切就绪，Power BI 会针对表中的每一行调用一次函数，执行关键短语查询并向表添加新列。 但在这些操作开始之前，可能需要指定身份验证和隐私设置。

## <a name="authentication-and-privacy"></a>身份验证和隐私

在关闭“调用自定义函数”对话框之后，可能会出现一个横幅，要求你指定如何连接到关键短语终结点。

![[凭据横幅]](../media/tutorials/power-bi/credentials-banner.png)

单击“编辑凭据”，确保选中对话框中的“匿名”，然后单击“连接”。 

> [!NOTE]
> 为何匿名？ 文本分析服务使用 API 密钥进行身份验证，因此 Power BI 不需为 HTTP 请求本身提供凭据。

![[将身份验证设置为匿名]](../media/tutorials/power-bi/access-web-content.png)

如果在选择匿名访问后仍看到“编辑凭据”横幅，则可能是因为忘记粘贴 API 密钥。 检查高级编辑器中的 `KeyPhrases` 自定义函数，确保该函数在其中。

接下来可能会出现一个横幅，要求你提供有关数据源隐私的信息。 

![[隐私横幅]](../media/tutorials/power-bi/privacy-banner.png)

对于对话框中的每个数据源，请单击“继续”，然后选择“公开”。 然后单击“保存”。

![[设置数据源隐私]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>创建词云

处理完显示的横幅以后，请单击“主页”功能区中的“关闭并应用”，关闭查询编辑器。

Power BI Desktop 需要时间来发出必需的 HTTP 请求。 对于表中的每一行，新的 `keyphrases` 列都包含关键短语 API 在文本中检测到的关键短语。 

让我们使用此列生成一个词云。 首先，单击工作区左侧 Power BI Desktop 主窗口中的“报表”按钮。

> [!NOTE]
> 为何使用提取的关键短语而不是每个评论的完整文本来生成词云？ 关键短语提供的是客户评论中的重要词汇，而不仅仅是最常见词汇。 另外，生成的云中的单词大小调整不会因某个词在相对少数评论中的频繁使用而扭曲。

如果尚未安装词云自定义视觉对象，请安装它。 在工作区右侧的“可视化效果”面板中，单击三点形式的省略号 (**...**)，然后选择“从存储导入”。 然后搜索“云”并单击词云视觉对象旁边的“添加”按钮。 Power BI 会安装词云视觉对象并会让你知道它已成功安装。

![[添加自定义视觉对象]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

现在，让我们生成自己的词云！

![[“可视化效果”面板中的“词云”图标]](../media/tutorials/power-bi/visualizations-panel.png)

首先，单击“可视化效果”面板中的“词云”图标。 此时会在工作区中显示新的报表。 将 `keyphrases` 字段从“字段”面板拖至“可视化效果”面板中的“类别”字段。 词云会显示在报表中。

现在，请切换到“可视化效果”面板的“格式”页面。 在“非索引字”类别中启用“默认非索引字”，以便从云中消除短的常用词，例如“of”。 

![[激活默认非索引字]](../media/tutorials/power-bi/default-stop-words.png)

在此面板中再往下稍微移动一点，禁用“旋转文本”和“标题”。

![[激活焦点模式]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

单击报表中的“焦点模式”工具可以更好地查看词云。 此工具可将词云展开，使之填充整个工作区，如下所示。

![[一个词云]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>更多文本分析服务

作为 Microsoft Azure 提供的认知服务之一，文本分析服务还提供情绪分析和语言检测功能。 语言检测尤其适用于客户反馈不完全是英语的情况。

这两个其他的 API 非常类似于关键短语 API。 因此，可以使用几乎相同的自定义函数将它们与 Power BI Desktop 集成。 直接创建一个空白查询，然后将下面的相应代码粘贴到高级编辑器中，就像此前的操作那样。 （请勿忘记访问密钥！）然后，像以前那样，使用函数向表添加新列。

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
// Returns the two-letter language code (e.g. en for English) of the text
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
// Returns the name (e.g. English) of the language in which the text is written
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

详细了解文本分析服务、Power Query M 公式语言或 Power BI。

> [!div class="nextstepaction"]
> [文本分析 API 参考](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Power Query M reference](//msdn.microsoft.com/library/mt211003.aspx)（Power Query M 参考）

> [!div class="nextstepaction"]
> [Power BI 文档](//powerbi.microsoft.com/documentation/powerbi-landing-page/)

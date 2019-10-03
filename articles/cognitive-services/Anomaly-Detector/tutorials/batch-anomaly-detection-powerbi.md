---
title: 使用批量检测和 Power BI 将异常可视化
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 和 Power BI 将时序数据中的异常可视化。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: 74b51d04f2706d890475c500e1e730cff75397c5
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721490"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>教程：使用批量检测和 Power BI 将异常可视化

使用本教程查找设置为批的时序数据中的异常。 使用 Power BI Desktop 时，需获取一个 Excel 文件，为异常检测器 API 准备数据，然后将其中的统计异常可视化。

本教程介绍以下操作：

> [!div class="checklist"]
> * 使用 Power BI Desktop 导入和转换时序数据集
> * 将 Power BI Desktop 与异常检测器 API 集成，以便进行批量异常检测
> * 将在数据中发现的异常可视化，其中包括预期的和看到的值，以及异常检测边界。

## <a name="prerequisites"></a>先决条件

* 免费提供的 [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)。
* 一个 Excel 文件 (.xlsx)，其中包含时序数据点。 可在 [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962) 上找到本快速入门的示例数据

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>加载时序数据并设置其格式

打开 Power BI Desktop 并加载你在先决条件部分下载的时序数据即可开始。 此 Excel 文件包含一系列协调世界时 (UTC) 时间戳和值对。  

> [!NOTE]
> Power BI 可以使用各种源（例如 .csv 文件、SQL 数据库、Azure Blob 存储等）提供的数据。  

在 Power BI Desktop 主窗口中，单击“开始”  功能区。 在功能区的“外部数据”  组中，打开“获取数据”  下拉菜单，然后单击“Excel”  。

![Power BI 中“获取数据”按钮的图像](../media/tutorials/power-bi-get-data-button.png)

对话框出现以后，导航到下载了示例 .xlsx 文件的文件夹，然后将其选中。 在“导航器”对话框出现以后，单击“Sheet1”，然后单击“编辑”。   

![Power BI 中数据源“导航器”屏幕的图像](../media/tutorials/navigator-dialog-box.png)

Power BI 会将第一列中的时间戳转换为 `Date/Time` 数据类型。 这些时间戳必须转换为文本才能发送到异常检测器 API。 如果 Power Query 编辑器没有自动打开，请单击主页选项卡上的“编辑查询”。  

单击 Power Query 编辑器中的“转换”  功能区。 在“任何列”  组中，打开“数据类型:”  下拉菜单，然后选择“文本”  。

![Power BI 中数据源“导航器”屏幕的图像](../media/tutorials/data-type-drop-down.png)

获得有关更改列类型的通知后，请单击“替换当前项”。  然后，在“主页”  功能区中单击“关闭并应用”  或“应用”  。 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>创建一个函数来发送数据并设置响应的格式

若要设置数据文件的格式并将其发送到异常检测器 API，可以调用一个在上面创建的表的查询。 在 Power Query 编辑器的“开始”  功能区中，打开  “新建源”  下拉菜单并单击“空白查询”。

确保选中新建查询，然后单击“高级编辑器”  。 

![Power BI 中“高级编辑器”按钮的图像](../media/tutorials/advanced-editor-screen.png)

在高级编辑器中，使用以下 Power Query M 代码片段从表中提取列并将其发送到 API。 然后，查询会根据 JSON 响应创建一个表并将其返回。 将 `apiKey` 变量替换为有效的异常检测器 API 密钥，并将 `endpoint` 替换为终结点。 将查询输入高级编辑器以后，单击“完成”。 

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

调用数据工作表上的查询，方法是：在“输入参数”下选择 `Sheet1`，  然后单击“调用”。  

![“高级编辑器”按钮的图像](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>数据源隐私和身份验证

> [!NOTE]
> 请注意组织的数据隐私和访问政策。 有关详细信息，请参阅 [Power BI Desktop 隐私级别](https://docs.microsoft.com/power-bi/desktop-privacy-levels)。

在尝试运行此查询时，可能会出现警告消息，因为此查询使用外部数据源。 

![一个显示由 Power BI 创建的警告的图像](../media/tutorials/blocked-function.png)

若要修复此问题，请单击“文件”，然后单击“选项和设置”  。  然后单击“选项”  。 在“当前文件”下选择“隐私”，然后选择“忽略隐私级别并潜在地改善性能”。    

另外，可能会出现一条消息，要求指定连接到 API 的方式。

![一个图像，其中显示了一条请求，要求指定访问凭据](../media/tutorials/edit-credentials-message.png)

若要修复此问题，请单击消息中的“编辑凭据”  。 在对话框出现后选择“匿名”，以匿名方式连接到 API。  然后单击“连接”  。 

然后在“主页”  功能区中单击“关闭并应用”  ，应用所做的更改。

## <a name="visualize-the-anomaly-detector-api-response"></a>可视化异常检测器 API 响应

在 Power BI 主屏幕中，开始使用上面创建的查询将数据可视化。 首先选择“可视化效果”中的“折线图”。   然后，将已调用函数中的时间戳添加到折线图的“轴”。  右键单击它，并选择“时间戳”。  

![右键单击时间戳值](../media/tutorials/timestamp-right-click.png)

将“已调用函数”中的以下字段添加到图的“值”字段。   根据以下屏幕截图来构建图表。

    * 值
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![新快速度量屏幕的图像](../media/tutorials/chart-settings.png)

添加字段后，单击图表并重设其大小，以便显示所有数据点。 图表的外观应类似于以下屏幕截图：

![新快速度量屏幕的图像](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>显示异常数据点

在 Power BI 窗口右侧的“字段”窗格下面，  右键单击“已调用函数查询”下的“值”   ，然后单击“新建快速度量”。 

![新快速度量屏幕的图像](../media/tutorials/new-quick-measure.png)

在出现的屏幕上，选择“筛选的值”进行计算。  将“基础值”  设置为`Sum of Value`。 然后将 `IsAnomaly` 从“已调用函数”字段拖至“筛选器”。   从“筛选器”下拉菜单中选择 `True`。 

![新快速度量屏幕的图像](../media/tutorials/new-quick-measure-2.png)

单击“确定”后，会在字段列表底部出现一个  `Value for True`字段。 右键单击它，将其重命名为“异常”。  将其添加到图表的“值”。  然后选择“格式”工具，  将 X 轴类型设置为“分类”。 

![新快速度量屏幕的图像](../media/tutorials/format-x-axis.png)

为图表应用颜色，方法是：单击“格式”工具和“数据颜色”。   图表的外观应如下所示：

![新快速度量屏幕的图像](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用 Azure Databricks 流式处理异常检测](anomaly-detection-streaming-databricks.md)

---
title: 可视化使用批处理检测和 Power BI 的异常
titlesuffix: Azure Cognitive Services
description: 使用异常检测器 API 和 Power BI 可视化整个时序数据异常。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: de353c946da46b2bbe4e755e21e355f6bd18725b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65827137"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>教程：可视化使用批处理检测和 Power BI 的异常

使用本教程来查找时间系列数据集作为批处理内的异常。 使用 Power BI desktop，你将获取 Excel 文件、 准备数据以异常检测器 API，并可视化整个过程的统计异常。

本教程介绍以下操作：

> [!div class="checklist"]
> * 使用 Power BI Desktop 导入和转换时间系列数据集
> * 将 Power BI Desktop 与批处理异常情况检测异常情况检测器 API 集成
> * 可视化你的数据，包括预期和看到值和异常情况检测边界内找到的异常。

## <a name="prerequisites"></a>必备组件

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/)、 可用免费。
* Excel (.xlsx) 文件包含时间序列数据点。 本快速入门教程的示例数据都位于[GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>加载和设置时间系列数据的格式

若要开始，打开 Power BI Desktop 并加载时间序列数据从系统必备组件下载。 此 excel 文件包含一系列的协调世界时 (UTC) 时间戳和值对。  

> [!NOTE]
> Power BI 可以使用来自各种源，例如.csv 文件、 SQL 数据库、 Azure blob 存储和的详细信息的数据。  

在 Power BI Desktop 主窗口中，单击**主页**功能区。 在中**外部数据**组的功能区中，打开**获取数据**下拉列表菜单，然后单击**Excel**。

![在 Power BI 中的"获取数据"按钮的图像](../media/tutorials/power-bi-get-data-button.png)

此时将显示对话框后，导航到下载示例.xlsx 文件的文件夹并选择它。 之后**导航器**对话框出现后，单击**Sheet1**，然后**编辑**。

![在 Power BI 中的数据源"导航器"屏幕的图像](../media/tutorials/navigator-dialog-box.png)

Power BI 会将转换到的第一列中的时间戳`Date/Time`数据类型。 这些时间戳必须转换为文本，才能将其发送到异常情况检测器 API。 如果未自动打开 Power Query 编辑器中，单击**编辑查询**主页选项卡上。 

单击**转换**功能区 Power Query 编辑器中。 在中**任何列**组中，打开**数据类型：** 下拉列表菜单，然后选择**文本**。

![在 Power BI 中的数据源"导航器"屏幕的图像](../media/tutorials/data-type-drop-down.png)

获取有关更改列类型的通知，请单击**替换为当前**。 然后，单击**关闭并应用**或**应用**中**主页**功能区。 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>创建一个函数来发送数据及设置响应的格式

若要设置格式并将数据文件发送到异常情况检测器 API，可以调用上面创建的表的查询。 在 Power 查询编辑器中，从**主页**功能区中，打开**新源**下拉列表菜单，然后单击**空白查询**。

请确保选择您的新查询，然后单击**高级编辑器**。 

![在 Power BI 中的"高级编辑器"按钮的图像](../media/tutorials/advanced-editor-screen.png)

在高级编辑器中，使用以下 Power Query M 代码片段从表中提取列并将其发送到 API。 之后，该查询将从 JSON 响应中，创建一个表，并将其返回。 替换`apiKey`有效的异常情况检测器 API 密钥，使用变量和`endpoint`与终结点。 高级编辑器中输入查询后，单击**完成**。

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

通过选择调用数据工作表上的查询`Sheet1`如下**输入参数**，然后单击**Invoke**。 

!["高级编辑器"按钮的图像](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>数据源的保密性和身份验证

> [!NOTE]
> 请注意的有关数据隐私和访问你组织的策略。 请参阅[Power BI Desktop 隐私级别](https://docs.microsoft.com/power-bi/desktop-privacy-levels)有关详细信息。

当您尝试运行查询，因为它利用外部数据源时，可能会收到一条警告消息。 

![显示通过 Power BI 创建一条警告的图像](../media/tutorials/blocked-function.png)

若要解决此问题，请单击**文件**，并**选项和设置**。 然后单击**选项**。 下面**当前文件**，选择**隐私**，并**忽略隐私级别并潜在地提高性能**。 

此外，可能会收到消息，询问您要指定你想要连接到 API。

![显示指定访问凭据的请求的图像](../media/tutorials/edit-credentials-message.png)

若要解决此问题，请单击**编辑凭据**消息中。 对话框的显示后，请选择**Anonymous**以匿名方式连接到 API。 然后单击“连接”  。 

然后，单击**关闭并应用**中**主页**功能区应用所做的更改。

## <a name="visualize-the-anomaly-detector-api-response"></a>可视化异常检测器 API 响应

在 Power BI 主屏幕中，开始使用上面的创建来可视化数据的查询。 首先选择**折线图**中**可视化效果**。 然后将从调用的函数的时间戳添加到折线图**轴**。 右键单击它，并选择**时间戳**。 

![右键单击的时间戳值](../media/tutorials/timestamp-right-click.png)

添加以下字段从**调用的函数**到图表的**值**字段。 使用下面的屏幕快照以帮助您构建您的图表。

    * 值
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![新快速度量值屏幕的图像](../media/tutorials/chart-settings.png)

后添加以下字段，单击图表并调整其大小以显示所有数据点。 图表外观将类似于下面的屏幕快照：

![新快速度量值屏幕的图像](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>显示异常数据点

Power BI 窗口中，右侧下面**字段**窗格中，右键单击**值**下**调用函数查询**，然后单击**新快速度量值**。

![新快速度量值屏幕的图像](../media/tutorials/new-quick-measure.png)

在出现的屏幕，选择**筛选值**的计算。 设置**基础值**到`Sum of Value`。 然后将拖`IsAnomaly`从**调用函数**字段设置为**筛选器**。 选择`True`从**筛选器**下拉列表菜单。

![新快速度量值屏幕的图像](../media/tutorials/new-quick-measure-2.png)

单击后**确定**，将具有`Value for True`字段中，在您的字段列表的底部。 右键单击它，其重命名为**异常情况**。 将其添加到图表的**值**。 然后选择**格式**工具，并将 x 轴类型设置为**分类**。

![新快速度量值屏幕的图像](../media/tutorials/format-x-axis.png)

通过单击将颜色应用到您的图表**格式**工具并**数据颜色**。 图表应类似如下所示：

![新快速度量值屏幕的图像](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用 Azure Databricks 的流式处理异常情况检测](anomaly-detection-streaming-databricks.md)

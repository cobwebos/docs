---
title: 适用于大数据的认知服务 Scala 示例
description: 使用适用于 Azure Databricks 的认知服务来运行针对大数据的 MMLSpark 管道。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: 4546ef03c82f19d188a71a86f6964ca87c0f834e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90524957"
---
# <a name="quick-examples"></a>快速示例

以下代码片段已准备好运行，可帮助你开始在 Spark 上使用认知服务。 以下示例使用 Scala。

这些示例使用以下认知服务：

- 文本分析 - 获取一组句子的情绪。
- 计算机视觉 - 获取与一组图像相关联的标记（单字说明）。
- 必应图像搜索 - 在 Web 中搜索与自然语言查询相关的图像。
- 语音转文本 - 转录音频文件以提取基于文本的转录。
- 异常检测器 - 检测时间序列数据中的异常。

## <a name="prerequisites"></a>先决条件

1. 按照[入门](getting-started.md)中的步骤设置 Azure Databricks 和认知服务环境。 本教程将介绍如何安装 MMLSpark 以及如何在 Databricks 中创建 Spark 群集。
1. 在 Azure Databricks 中创建新笔记本后，复制下面的“共享代码”并粘贴到笔记本中的新单元格中。
1. 选择下面的服务示例，并将其复制粘贴到笔记本的第二个新单元格中。
1. 将所有服务订阅密钥占位符替换为自己的密钥。
1. 选择单元格右上角的运行按钮（三角形图标），然后选择“运行单元格”。
1. 在单元格下方的表格中查看结果。

## <a name="shared-code"></a>共享代码
若要开始，请将以下代码添加到项目中：

```scala
import com.microsoft.ml.spark.cognitive._
import spark.implicits._ 

val serviceKey = "ADD-YOUR-SUBSCRIPTION-KEY"
val location = "eastus"
```

## <a name="text-analytics"></a>文本分析

[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)服务提供了几种从文本中提取智能见解的算法。 例如，我们可以找到给定输入文本的情绪。 该服务将返回介于 `0.0` 和 `1.0` 之间的分数，其中低分数指示负面情绪，高分数指示正面情绪。  下面的示例使用三个简单的句子，并返回每个句子的情绪分数。

```scala
import org.apache.spark.sql.functions.col

val df = Seq(
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US")
).toDF("text", "language")

val sentiment = new TextSentiment()
    .setTextCol("text")
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language")

display(sentiment.transform(df).select(col("text"), col("sentiment")(0).getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>预期结果

| text                                      | 情绪                                             |
|:------------------------------------------|:------------------------------------------------------|
| 今天天气晴朗，我真高兴！           | 0.9789592027664185                                    |
| 交通高峰期让我很郁闷 | 0.023795604705810547                                  |
| Spark 的认知服务不差  | 0.8888956308364868                                    |

## <a name="computer-vision"></a>计算机视觉

[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)会分析图像以识别结构，例如人脸、对象和自然语言说明。
在此示例中，我们标记一系列图像。 标记是对图像中可识别的对象、人物、风景和动作等事物的单个词说明。

```scala
// Create a dataframe with the image URLs
val df = Seq(
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg"),
    ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg")
).toDF("image")

// Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
val analysis = new AnalyzeImage()
    .setLocation(location)
    .setSubscriptionKey(serviceKey)
    .setVisualFeatures(Seq("Categories","Color","Description","Faces","Objects","Tags"))
    .setOutputCol("results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

// Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select(col("image"), col("results").getItem("tags").getItem("name")).alias("results")))

// Uncomment for full results with all visual feature requests
//display(analysis.transform(df).select(col("image"), col("results")))
``` 

### <a name="expected-result"></a>预期结果

| image | 标记 |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group'] 

## <a name="bing-image-search"></a>必应图像搜索

[必应图像搜索](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)在 Web 中搜索以检索与用户自然语言查询相关的图像。 在此示例中，我们将使用一个文本查询来查找带引号的图像。 它返回包含与我们的查询相关的照片的图像 URL 列表。


```scala
import org.apache.spark.ml.Pipeline

// Number of images Bing will return per query
val imgsPerBatch = 10

// A list of offsets, used to page into the search results
val df = (0 until 100).map(o => Tuple1(o*imgsPerBatch)).toSeq.toDF("offset")

// Run the Bing Image Search service with our text query
val bingSearch = new BingImageSearch()
    .setSubscriptionKey(bingSearchKey)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images")

// Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
val getUrls = BingImageSearch.getUrlTransformer("images", "url")

// This displays the full results returned, uncomment to use
// display(bingSearch.transform(bingParameters))

// Since we have two services, they are put into a pipeline
val pipeline = new Pipeline().setStages(Array(bingSearch, getUrls))

// Show the results of your search: image URLs
display(pipeline.fit(df).transform(df))
```

### <a name="expected-result"></a>预期结果

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |

## <a name="speech-to-text"></a>语音转文本

[语音转文本](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text)服务将语音音频的流或文件转换为文本。 在此示例中，我们转录了两个音频文件。 第一个文件易于理解，第二个文件更具挑战性。

```scala
import org.apache.spark.sql.functions.col

// Create a dataframe with audio URLs, tied to the column called "url"
val df = Seq(("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav"),
             ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3")).toDF("url")

// Run the Speech-to-text service to translate the audio into text
val speechToText = new SpeechToTextSDK()
    .setSubscriptionKey(serviceKey)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked")

// Show the results of the translation
display(speechToText.transform(df).select(col("url"), col("text").getItem("DisplayText")))
```

### <a name="expected-result"></a>预期结果

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | 自定义语音提供了一些工具，通过比较音频数据和自定义语音门户的相应识别结果，你可以直观地检查模型的识别质量。 你可以播放上传的音频，并确定提供的识别结果是否正确。 使用此工具，可以快速检查 Microsoft 的基准语音转文本模型或经过训练的自定义模型的质量，而无需转录任何音频数据。 |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 增加一位绅士先生思考视觉检查。    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 我听到了我的声音。 |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 我也喜欢收音机给我带来的安心的感觉，因为我可以听得很清楚。 |

## <a name="anomaly-detector"></a>异常检测器

[异常检测器](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/)对于检测时间序列数据中的不规则性非常有用。 在此示例中，我们使用该服务来查找整个时间序列中的异常情况。

```scala
import org.apache.spark.sql.functions.{col, lit}

val anomalyKey = "84a2c303cc7e49f6a44d692c27fb9967"

val df = Seq(
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0),
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0)
  ).toDF("timestamp", "value").withColumn("group", lit("series1"))

// Run the Anomaly Detector service to look for irregular data
val anamolyDetector = new SimpleDetectAnomalies()
    .setSubscriptionKey(anomalyKey)
    .setLocation("eastus")
    .setTimestampCol("timestamp")
    .setValueCol("value")
    .setOutputCol("anomalies")
    .setGroupbyCol("group")
    .setGranularity("monthly")

// Show the full results of the analysis with the anomalies marked as "True"
display(anamolyDetector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
```

### <a name="expected-result"></a>预期结果

| timestamp            |   值 | isAnomaly   |
|:---------------------|--------:|:------------|
| 1972-01-01T00:00:00Z |     826 | False       |
| 1972-02-01T00:00:00Z |     799 | False       |
| 1972-03-01T00:00:00Z |     890 | False       |
| 1972-04-01T00:00:00Z |     900 | False       |
| 1972-05-01T00:00:00Z |     766 | False       |
| 1972-06-01T00:00:00Z |     805 | False       |
| 1972-07-01T00:00:00Z |     821 | False       |
| 1972-08-01T00:00:00Z |   20000 | True        |
| 1972-09-01T00:00:00Z |     883 | False       |
| 1972-10-01T00:00:00Z |     898 | False       |
| 1972-11-01T00:00:00Z |     957 | False       |
| 1972-12-01T00:00:00Z |     924 | False       |
| 1973-01-01T00:00:00Z |     881 | False       |
| 1973-02-01T00:00:00Z |     837 | False       |
| 1973-03-01T00:00:00Z |    9000 | True        |

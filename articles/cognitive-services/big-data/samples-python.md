---
title: 适用于大数据的认知服务 - Python 示例
description: 尝试使用 Python for Azure Databricks 中的认知服务示例来运行针对大数据的 MMLSpark 管道。
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.topic: sample
ms.date: 07/06/2020
ms.author: marhamil
ms.openlocfilehash: caf492c2cd3940fd7f37e2a4462c8376a127f393
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86189298"
---
# <a name="python-samples-for-cognitive-services-for-big-data"></a>适用于大数据的认知服务的 Python 示例

以下代码片段已准备好运行，可帮助你开始使用 Python 在 Spark 上使用认知服务。

本文中的示例使用以下认知服务：

- 文本分析 - 获取一组句子的情绪。
- 计算机视觉 - 获取与一组图像相关联的标记（单字说明）。
- 必应图像搜索 - 在 Web 中搜索与自然语言查询相关的图像。
- 语音转文本 - 转录音频文件以提取基于文本的转录。
- 异常检测器 - 检测时间序列数据中的异常。

## <a name="prerequisites"></a>先决条件

1. 按照[入门](getting-started.md)中的步骤设置 Azure Databricks 和认知服务环境。 本教程介绍如何安装 MMLSpark 以及如何在 Databricks 中创建 Spark 群集。
1. 在 Azure Databricks 中创建新笔记本后，复制下面的“共享代码”并粘贴到笔记本中的新单元格中。
1. 选择下面的服务示例，并将其复制粘贴到笔记本的第二个新单元格中。
1. 将所有服务订阅密钥占位符替换为自己的密钥。
1. 选择单元格右上角的运行按钮（三角形图标），然后选择“运行单元格”。
1. 在单元格下方的表格中查看结果。

## <a name="shared-code"></a>共享代码

若要开始，我们需要将此代码添加到项目中：

```python
from mmlspark.cognitive import *

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key = "ADD_YOUR_SUBSCRIPION_KEY"
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY"
# An Anomaly Dectector subscription key
anomaly_key = "ADD_YOUR_SUBSCRIPION_KEY"

# Validate the key
assert service_key != "ADD_YOUR_SUBSCRIPION_KEY"
```    

## <a name="text-analytics-sample"></a>文本分析示例

[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)服务提供了几种从文本中提取智能见解的算法。 例如，我们可以找到给定输入文本的情绪。 服务将返回介于 0.0 和 1.0 之间的分数，其中低分数表示负面情绪，高分表示正面情绪。  此示例使用三个简单的句子，并返回每个句子的情绪。

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"),
  ("I am frustrated by this rush hour traffic", "en-US"),
  ("The cognitive services on spark aint bad", "en-US"),
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format
display(sentiment.transform(df).select("text", col("sentiment")[0].getItem("score").alias("sentiment")))
```

### <a name="expected-result"></a>预期结果

| text                                      | 情绪                                             |
|:------------------------------------------|:------------------------------------------------------|
| 今天天气晴朗，我真高兴！           | 0.9789592027664185                                    |
| 交通高峰期让我很郁闷 | 0.023795604705810547                                  |
| Spark 的认知服务不差  | 0.8888956308364868                                    |

## <a name="computer-vision-sample"></a>计算机视觉示例

[计算机视觉](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)会分析图像以识别结构，例如人脸、对象和自然语言说明。 在此示例中，我们标记一系列图像。 标记是对图像中可识别的对象、人物、风景和动作等事物的单个词说明。

```python

# Create a dataframe with the image URLs
df = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg", ),
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts infortmation from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastus")
    .setSubscriptionKey(service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df).select("image", "analysis_results.description.tags"))
```

### <a name="expected-result"></a>预期结果

| image | 标记 |
|:------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------|
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg | ['skating' 'person' 'man' 'outdoor' 'riding' 'sport' 'skateboard' 'young' 'board' 'shirt' 'air' 'black' 'park' 'boy' 'side' 'jumping' 'trick' 'ramp' 'doing' 'flying']
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/dog.jpg | ['dog' 'outdoor' 'fence' 'wooden' 'small' 'brown' 'building' 'sitting' 'front' 'bench' 'standing' 'table' 'walking' 'board' 'beach' 'white' 'holding' 'bridge' 'track']                
| https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/house.jpg | ['outdoor' 'grass' 'house' 'building' 'old' 'home' 'front' 'small' 'church' 'stone' 'large' 'grazing' 'yard' 'green' 'sitting' 'leading' 'sheep' 'brick' 'bench' 'street' 'white' 'country' 'clock' 'sign' 'parked' 'field' 'standing' 'garden' 'water' 'red' 'horse' 'man' 'tall' 'fire' 'group']


## <a name="bing-image-search-sample"></a>必应图像搜索示例

[必应图像搜索](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)在 Web 中搜索以检索与用户自然语言查询相关的图像。 在此示例中，我们将使用一个文本查询来查找带引号的图像。 它返回包含与我们的查询相关的照片的图像 URL 列表。

```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 10
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(100)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bing_search_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")

# This displays the full results returned, uncomment to use
# display(bingSearch.transform(bingParameters))

# Since we have two services, they are put into a pipeline
pipeline = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
display(pipeline.transform(bingParameters))
```

### <a name="expected-result"></a>预期结果

| url |
|:-------------------------------------------------------------------------------------------------------------------|
| https://iheartintelligence.com/wp-content/uploads/2019/01/powerful-quotes-martin-luther-king-jr.jpg      |
| http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg             |
| http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg |
| https://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-18.jpg            |
| https://tsal-eszuskq0bptlfh8awbb.stackpathdns.com/wp-content/uploads/2018/01/MartinLutherKingQuotes.jpg  |


## <a name="speech-to-text-sample"></a>语音转文本示例
[语音转文本](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text)服务将语音音频的流或文件转换为文本。 在此示例中，我们转录了两个音频文件。 第一个文件易于理解，第二个文件更具挑战性。

```python

# Create a dataframe with our audio URLs, tied to the column called "url"
df = spark.createDataFrame([("https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav",),
                           ("https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3",)
                           ], ["url"])

# Run the Speech-to-text service to translate the audio into text
speech_to_text = (SpeechToTextSDK()
    .setSubscriptionKey(service_key)
    .setLocation("eastus")
    .setOutputCol("text")
    .setAudioDataCol("url")
    .setLanguage("en-US")
    .setProfanity("Masked"))

# Show the results of the translation
display(speech_to_text.transform(df).select("url", "text.DisplayText"))
```

### <a name="expected-result"></a>预期结果

| url | DisplayText |
|:------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio2.wav | 自定义语音提供了一些工具，通过比较音频数据和自定义语音门户的相应识别结果，你可以直观地检查模型的识别质量。 你可以播放上传的音频，并确定提供的识别结果是否正确。 使用此工具，可以快速检查 Microsoft 的基准语音转文本模型或经过训练的自定义模型的质量，而无需转录任何音频数据。 |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 增加一位绅士先生思考视觉检查。    |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 我听到了我的声音。 |
| https://mmlspark.blob.core.windows.net/datasets/Speech/audio3.mp3 | 我也喜欢收音机给我带来的安心的感觉，因为我可以听得很清楚。 |


## <a name="anomaly-detector-sample"></a>异常检测器示例

[异常检测器](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/)对于检测时间序列数据中的不规则性非常有用。 在此示例中，我们使用该服务来查找整个时间序列中的异常情况。

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df = spark.createDataFrame([
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
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomaly_key)
  .setLocation("eastus")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df).select("timestamp", "value", "anomalies.isAnomaly"))
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

## <a name="arbitrary-web-apis"></a>任意 Web API

借助 Spark 上的 HTTP，可以在大数据管道中使用任何 Web 服务。 在此示例中，我们使用 [World Bank API](http://api.worldbank.org/v2/country/) 获取有关全球各个国家/地区的信息。

```python
from requests import Request
from mmlspark.io.http import HTTPTransformer, http_udf
from pyspark.sql.functions import udf, col

# Use any requests from the python requests library
def world_bank_request(country):
  return Request("GET", "http://api.worldbank.org/v2/country/{}?format=json".format(country))

# Create a dataframe with spcificies which countries we want data on
df = (spark.createDataFrame([("br",),("usa",)], ["country"])
  .withColumn("request", http_udf(world_bank_request)(col("country"))))

# Much faster for big data because of the concurrency :)
client = (HTTPTransformer()
      .setConcurrency(3)
      .setInputCol("request")
      .setOutputCol("response"))

# Get the body of the response
def get_response_body(resp):
  return resp.entity.content.decode()

# Show the details of the country data returned
display(client.transform(df).select("country", udf(get_response_body)(col("response")).alias("response")))
```

### <a name="expected-result"></a>预期结果

| country   | 响应 |
|:----------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| br | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"BRA","iso2Code":"BR","name":"Brazil","region":{"id":"LCN","iso2code":"ZJ","value":"Latin America & Caribbean "},"adminregion":{"id":"LAC","iso2code":"XJ","value":"Latin America & Caribbean (excluding high income)"},"incomeLevel":{"id":"UMC","iso2code":"XT","value":"Upper middle income"},"lendingType":{"id":"IBD","iso2code":"XF","value":"IBRD"},"capitalCity":"Brasilia","longitude":"-47.9292","latitude":"-15.7801"}]] |
| usa  | [{"page":1,"pages":1,"per_page":"50","total":1},[{"id":"USA","iso2Code":"US","name":"United States","region":{"id":"NAC","iso2code":"XU","value":"North America"},"adminregion":{"id":"","iso2code":"","value":""},"incomeLevel":{"id":"HIC","iso2code":"XD","value":"High income"},"lendingType":{"id":"LNX","iso2code":"XX","value":"Not classified"},"capitalCity":"Washington D.C.","longitude":"-77.032","latitude":"38.8895"}]] |

## <a name="see-also"></a>另请参阅

* [方案：异常检测](./recipes/anomaly-detection.md)
* [方案：Art Explorer](./recipes/art-explorer.md)

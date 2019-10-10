---
title: 教程：使用 Azure Databricks 针对流数据进行异常情况检测
titleSuffix: Azure Cognitive Services
description: 使用异常检测器 API 和 Azure Databricks 来监视数据中的异常。
titlesuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: 75c2c8bf8b3baee1f9f89282840622e1e29d2a18
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837776"
---
# <a name="tutorial-anomaly-detection-on-streaming-data-using-azure-databricks"></a>教程：使用 Azure Databricks 针对流数据进行异常情况检测

[Azure Databricks](https://azure.microsoft.com/services/databricks/) 是基于Apache Spark 的快速、简单、协作型分析服务。 异常检测器 API 是 Azure 认知服务的一部分，可以用于监视时序数据。 按照本教程的说明，使用 Azure Databricks 以近实时方式对数据流运行异常检测。 我们将使用 Azure 事件中心引入推特数据，并使用 Spark 事件中心连接器将其导入 Azure Databricks。 然后，我们将使用 API 检测流式传输数据中的异常。 

下图演示了应用程序流：

![Azure Databricks 与事件中心和认知服务](../media/tutorials/databricks-cognitive-services-tutorial.png "Azure Databricks 与事件中心和认知服务")

本教程涵盖以下任务：

> [!div class="checklist"]
> * 创建 Azure Databricks 工作区
> * 在 Azure Databricks 中创建 Spark 群集
> * 创建用于访问流数据的 Twitter 应用
> * 在 Azure Databricks 中创建 Notebook
> * 附加事件中心和 Twitter API 的库
> * 创建异常检测器资源并检索访问密钥
> * 将推文发送到事件中心
> * 读取事件中心的推文
> * 在推文中运行异常检测

> [!Note]
> 本教程引入了一个方法，用于为异常检测器 API 实现建议的[解决方案体系结构](https://azure.microsoft.com/solutions/architecture/anomaly-detector-process/)。

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

> [!Note]
> 本教程不能使用异常检测器 API 的免费试用版密钥来完成。 若要使用免费帐户创建 Azure Databricks 群集，请在创建群集前转到你的配置文件并将订阅更改为**即用即付**。 有关详细信息，请参阅 [Azure 免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- [Azure 事件中心命名空间](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)和事件中心。

- 用于访问事件中心命名空间的[连接字符串](../../../event-hubs/event-hubs-get-connection-string.md)。 该连接字符串应采用类似于

    `Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<key name>;SharedAccessKey=<key value>`。 

- 事件中心的共享访问策略名称和策略密钥。

请参阅 Azure 事件中心[快速入门](../../../event-hubs/event-hubs-create.md)，了解如何创建命名空间和事件中心。

## <a name="create-an-azure-databricks-workspace"></a>创建 Azure Databricks 工作区

在本部分，使用 [Azure 门户](https://portal.azure.com/)创建 Azure Databricks 工作区。

1. 在 Azure 门户中，选择“创建资源”   >   “分析” >   “Azure Databricks”。

    ![Azure 门户上的 Databricks](../media/tutorials/azure-databricks-on-portal.png "Azure 门户上的 Databricks")

3. 在“Azure Databricks 服务”  下提供以下值，创建 Databricks 工作区：


    |属性  |说明  |
    |---------|---------|
    |**工作区名称**     | 提供 Databricks 工作区的名称        |
    |**订阅**     | 从下拉列表中选择自己的 Azure 订阅。        |
    |**资源组**     | 指定是要创建新的资源组还是使用现有的资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 有关详细信息，请参阅 [Azure 资源组概述](../../../azure-resource-manager/resource-group-overview.md)。 |
    |**位置**     | 选择“美国东部 2”或任何其他可用区域之一。  有关区域可用性，请参阅[各区域推出的 Azure 服务](https://azure.microsoft.com/regions/services/)。        |
    |**定价层**     |  选择“标准”或“高级”。   请勿选择“试用版”  。 有关这些层的详细信息，请参阅 [Databricks 价格页](https://azure.microsoft.com/pricing/details/databricks/)。       |

    选择“创建”  。

4. 创建工作区需要几分钟时间。 

## <a name="create-a-spark-cluster-in-databricks"></a>在 Databricks 中创建 Spark 群集

1. 在 Azure 门户中，转到所创建的 Databricks 工作区，然后选择“启动工作区”。 

2. 系统随后会将你重定向到 Azure Databricks 门户。 在门户中选择“新建群集”。 

    ![Azure 上的 Databricks](../media/tutorials/databricks-on-azure.png "Azure 上的 Databricks")

3. 在“新建群集”页中，提供用于创建群集的值。 

    ![在 Azure 上创建 Databricks Spark 群集](../media/tutorials/create-databricks-spark-cluster.png "在 Azure 上创建 Databricks Spark 群集")

    除以下值外，接受其他所有默认值：

   * 输入群集的名称。
   * 在本文中，请创建运行时为 **5.2** 的群集。 请勿选择 **5.3** 运行时。
   * 请务必选中“在不活动超过 \_\_ 分钟后终止”  复选框。 如果未使用群集，则请提供一个持续时间（以分钟为单位），超过该时间后群集会被终止。

     选择“创建群集”。  
4. 创建群集需要数分钟。 群集运行后，可将笔记本附加到该群集，并运行 Spark 作业。

## <a name="create-a-twitter-application"></a>创建 Twitter 应用程序

若要接收推文流，必须在 Twitter 中创建一个应用程序。 按步骤创建一个 Twitter 应用程序，并记下稍后需要在本教程中使用的值。

1. 在 Web 浏览器中，转到 [Twitter 应用程序管理](https://apps.twitter.com/) ，选择“创建新应用”  。

    ![创建 Twitter 应用程序](../media/tutorials/databricks-create-twitter-app.png "创建 Twitter 应用程序")

2. 在“创建应用程序”  页中提供新应用的详细信息，然后选择“创建 Twitter 应用程序”  。

    ![Twitter 应用程序详细信息](../media/tutorials/databricks-provide-twitter-app-details.png "Twitter 应用程序详细信息")

3. 在应用程序页中选择“密钥和访问令牌”  选项卡，复制“使用者密钥”  和“使用者机密”  的值。 此外，请选择“创建我的访问令牌”  以生成访问令牌。 复制“访问令牌”  和  “访问令牌机密”的值。

    ![Twitter 应用程序详细信息](../media/tutorials/twitter-app-key-secret.png "Twitter 应用程序详细信息")

保存 Twitter 应用程序的检索值。 稍后在本教程中需要用到这些值。

## <a name="attach-libraries-to-spark-cluster"></a>将库附加到 Spark 群集

本教程使用 Twitter API 将推文发送到事件中心。 也可以使用 [Apache Spark 事件中心连接器](https://github.com/Azure/azure-event-hubs-spark)在 Azure 事件中心读取和写入数据。 若要将这些 API 用作群集的一部分，请将其作为库添加到 Azure Databricks，然后将其与 Spark 群集相关联。 以下说明介绍如何将库添加到工作区中的 **Shared** 文件夹。

1. 在 Azure Databricks 工作区中选择“工作区”  ，然后右键单击“共享”  。 从上下文菜单中选择“创建”   >   “库”。

   ![“添加库”对话框](../media/tutorials/databricks-add-library-option.png "“添加库”对话框")

2. 在“新建库”  页中，选择“Maven”作为“源”  。 对于“坐标”  ，请输入要添加的包的坐标。 下面是本教程中使用的库的 Maven 坐标：

   * Spark 事件中心连接器 - `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`
   * Twitter API - `org.twitter4j:twitter4j-core:4.0.7`

     ![提供 Maven 坐标](../media/tutorials/databricks-eventhub-specify-maven-coordinate.png "提供 Maven 坐标")

3. 选择“创建”  。

4. 选择库添加到的文件夹，然后选择库名称。

    ![选择要添加的库](../media/tutorials/select-library.png "选择要添加的库")

5. 如果库页中没有群集，请选择“群集”并运行所创建的群集。  等到状态显示为“正在运行”，然后返回到库页面。
在库页上，选择要在其中使用该库的群集，然后选择“安装”。  将库成功关联到群集后，状态会立即更改为“已安装”  。

    ![将库安装到群集](../media/tutorials/databricks-library-attached.png "将库安装到群集")

6. 针对 Twitter 包 `twitter4j-core:4.0.7` 重复上述步骤。

## <a name="get-a-cognitive-services-access-key"></a>获取认知服务访问密钥

本教程介绍如何使用 [Azure 认知服务异常检测器 API](../overview.md) 以近实时的方式对推文流运行异常检测。 在使用这些 API 之前，必须在 Azure 上创建异常检测器资源，并检索使用异常检测器 API 所需的访问密钥。

1. 登录到 [Azure 门户](https://portal.azure.com/)。

2. 选择“+ 创建资源”。 

3. 在 Azure 市场下，选择“AI + 机器学习”   > “全部查看”   > “认知服务 - 更多”   >   “异常检测器”。 也可使用[此链接](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector)，直接转到“创建”对话框。 

    ![创建异常检测器资源](../media/tutorials/databricks-cognitive-services-anomaly-detector.png "创建异常检测器资源")

4. 在“创建”  对话框中，提供以下值：

    |值 |说明  |
    |---------|---------|
    |Name     | 异常检测器资源的名称。        |
    |Subscription     | 将要与资源关联的 Azure 订阅。        |
    |位置     | Azure 位置。        |
    |定价层     | 服务定价层。 有关异常检测器定价的详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/anomaly-detector/)。        |
    |Resource group     | 指定是要创建新的资源组还是选择现有的资源组。        |


     选择“创建”  。

5. 创建资源后，从“概览”选项卡复制并保存  **终结点** URL，如屏幕截图所示。 然后选择“显示访问密钥”  。

    ![显示访问密钥](../media/tutorials/cognitive-services-get-access-keys.png "显示访问密钥")

6. 在“密钥”  下针对要使用的密钥选择复制图标。 保存访问密钥。

    ![复制访问密钥](../media/tutorials/cognitive-services-copy-access-keys.png "复制访问密钥")

## <a name="create-notebooks-in-databricks"></a>在 Databricks 中创建 Notebook

在本部分，请使用以下名称在 Databricks 工作区中创建两个 Notebook

- **SendTweetsToEventHub** - 用于从 Twitter 获取推文并将其流式传输到事件中心的生成者 Notebook。
- **AnalyzeTweetsFromEventHub** - 用于从事件中心读取推文并运行异常检测的使用者 Notebook。

1. 在 Azure Databricks 工作区的左窗格中选择“工作区”  。 在“工作区”下拉列表中选择“创建”，然后选择“Notebook”。   

    ![在 Databricks 中创建笔记本](../media/tutorials/databricks-create-notebook.png "在 Databricks 中创建笔记本")

2. 在“创建 Notebook”  对话框中输入 **SendTweetsToEventHub** 作为名称，选择 **Scala** 作为语言，并选择前面创建的 Spark 群集。

    ![在 Databricks 中创建笔记本](../media/tutorials/databricks-notebook-details.png "在 Databricks 中创建笔记本")

    选择“创建”  。

3. 重复上述步骤，创建 **AnalyzeTweetsFromEventHub** Notebook。

## <a name="send-tweets-to-event-hubs"></a>将推文发送到事件中心

在 **SendTweetsToEventHub** Notebook 中粘贴以下代码，并将占位符替换为前面创建的事件中心命名空间和 Twitter 应用程序的值。 此笔记本从包含关键字“Azure”的推文中提取“赞”的创建时间和数目，然后将其作为事件以实时方式流式传输到事件中心。

```scala
//
// Send Data to Eventhub
//

import scala.collection.JavaConverters._
import com.microsoft.azure.eventhubs._
import java.util.concurrent._
import com.google.gson.{Gson, GsonBuilder, JsonParser}
import java.util.Date
import scala.util.control._
import twitter4j._
import twitter4j.TwitterFactory
import twitter4j.Twitter
import twitter4j.conf.ConfigurationBuilder

// Event Hub Config
val namespaceName = "[Placeholder: EventHub namespace]"
val eventHubName = "[Placeholder: EventHub name]"
val sasKeyName = "[Placeholder: EventHub access key name]"
val sasKey = "[Placeholder: EventHub access key key]"
val connStr = new ConnectionStringBuilder()
  .setNamespaceName(namespaceName)
  .setEventHubName(eventHubName)
  .setSasKeyName(sasKeyName)
  .setSasKey(sasKey)

// Connect to the Event Hub
val pool = Executors.newScheduledThreadPool(1)
val eventHubClient = EventHubClient.create(connStr.toString(), pool)

def sendEvent(message: String) = {
  val messageData = EventData.create(message.getBytes("UTF-8"))
  eventHubClient.get().send(messageData)
  System.out.println("Sent event: " + message + "\n")
}

case class MessageBody(var timestamp: Date, var favorite: Int)
val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").create()

val twitterConsumerKey = "[Placeholder: Twitter consumer key]"
val twitterConsumerSecret = "[Placeholder: Twitter consumer seceret]"
val twitterOauthAccessToken = "[Placeholder: Twitter oauth access token]"
val twitterOauthTokenSecret = "[Placeholder: Twitter oauth token secret]"

val cb = new ConfigurationBuilder()
cb.setDebugEnabled(true)
  .setOAuthConsumerKey(twitterConsumerKey)
  .setOAuthConsumerSecret(twitterConsumerSecret)
  .setOAuthAccessToken(twitterOauthAccessToken)
  .setOAuthAccessTokenSecret(twitterOauthTokenSecret)

val twitterFactory = new TwitterFactory(cb.build())
val twitter = twitterFactory.getInstance()

// Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

val query = new Query(" #Azure ")
query.setCount(100)
query.lang("en")

var finished = false
var maxStatusId = Long.MinValue
var preMaxStatusId = Long.MinValue
val innerLoop = new Breaks
while (!finished) {
  val result = twitter.search(query)
  val statuses = result.getTweets()
  var lowestStatusId = Long.MaxValue
  innerLoop.breakable {
    for (status <- statuses.asScala) {
      if (status.getId() <= preMaxStatusId) {
        preMaxStatusId = maxStatusId
        innerLoop.break
      }
      if(!status.isRetweet()) {
        sendEvent(gson.toJson(new MessageBody(status.getCreatedAt(), status.getFavoriteCount())))
      }
      lowestStatusId = Math.min(status.getId(), lowestStatusId)
      maxStatusId = Math.max(status.getId(), maxStatusId)
    }
  }
  
  if (lowestStatusId == Long.MaxValue) {
    preMaxStatusId = maxStatusId
  }
  Thread.sleep(10000)
  query.setMaxId(lowestStatusId - 1)
}

// Close connection to the Event Hub
eventHubClient.get().close()
pool.shutdown()
```

若要运行该 Notebook，请按 **SHIFT + ENTER**。 会显示一个输出，如以下代码片段所示。 输出中的每个事件是已引入事件中心的“赞”的时间戳和数目的组合。

    Sent event: {"timestamp":"2019-04-24T09:39:40.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:38:48.000Z","favorite":1}

    Sent event: {"timestamp":"2019-04-24T09:38:36.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:27.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:37:00.000Z","favorite":2}

    Sent event: {"timestamp":"2019-04-24T09:31:11.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:15.000Z","favorite":0}

    Sent event: {"timestamp":"2019-04-24T09:30:02.000Z","favorite":1}

    ...
    ...

## <a name="read-tweets-from-event-hubs"></a>读取事件中心的推文

在 **AnalyzeTweetsFromEventHub** Notebook 中粘贴以下代码，并将占位符替换为前面创建的异常检测器资源的值。 此 Notebook 读取前面使用 **SendTweetsToEventHub** Notebook 流式传输到事件中心的推文。

首先，编写一个客户端来调用异常检测器。 
```scala

//
// Anomaly Detection Client
//

import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
import java.net.URL
import java.sql.Timestamp

import com.google.gson.{Gson, GsonBuilder, JsonParser}
import javax.net.ssl.HttpsURLConnection

case class Point(var timestamp: Timestamp, var value: Double)
case class Series(var series: Array[Point], var maxAnomalyRatio: Double, var sensitivity: Int, var granularity: String)
case class AnomalySingleResponse(var isAnomaly: Boolean, var isPositiveAnomaly: Boolean, var isNegativeAnomaly: Boolean, var period: Int, var expectedValue: Double, var upperMargin: Double, var lowerMargin: Double, var suggestedWindow: Int)
case class AnomalyBatchResponse(var expectedValues: Array[Double], var upperMargins: Array[Double], var lowerMargins: Array[Double], var isAnomaly: Array[Boolean], var isPositiveAnomaly: Array[Boolean], var isNegativeAnomaly: Array[Boolean], var period: Int)

object AnomalyDetector extends Serializable {

  // Cognitive Services API connection settings
  val subscriptionKey = "[Placeholder: Your Anomaly Detector resource access key]"
  val endpoint = "[Placeholder: Your Anomaly Detector resource endpoint]"
  val latestPointDetectionPath = "/anomalydetector/v1.0/timeseries/last/detect"
  val batchDetectionPath = "/anomalydetector/v1.0/timeseries/entire/detect";
  val latestPointDetectionUrl = new URL(endpoint + latestPointDetectionPath)
  val batchDetectionUrl = new URL(endpoint + batchDetectionPath)
  val gson: Gson = new GsonBuilder().setDateFormat("yyyy-MM-dd'T'HH:mm:ss.SSS'Z'").setPrettyPrinting().create()

  def getConnection(path: URL): HttpsURLConnection = {
    val connection = path.openConnection().asInstanceOf[HttpsURLConnection]
    connection.setRequestMethod("POST")
    connection.setRequestProperty("Content-Type", "text/json")
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey)
    connection.setDoOutput(true)
    return connection
  }

  // Handles the call to Cognitive Services API.
  def processUsingApi(request: String, path: URL): String = {
    println(request)
    val encoded_text = request.getBytes("UTF-8")
    val connection = getConnection(path)
    val wr = new DataOutputStream(connection.getOutputStream())
    wr.write(encoded_text, 0, encoded_text.length)
    wr.flush()
    wr.close()

    val response = new StringBuilder()
    val in = new BufferedReader(new InputStreamReader(connection.getInputStream()))
    var line = in.readLine()
    while (line != null) {
      response.append(line)
      line = in.readLine()
    }
    in.close()
    return response.toString()
  }

  // Calls the Latest Point Detection API.
  def detectLatestPoint(series: Series): Option[AnomalySingleResponse] = {
    try {
      println("Process Timestamp: " + series.series.apply(series.series.length-1).timestamp.toString + ", size: " + series.series.length)
      val response = processUsingApi(gson.toJson(series), latestPointDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalySingleResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        e.printStackTrace()
        return None
      }
    }
  }

  // Calls the Batch Detection API.
  def detectBatch(series: Series): Option[AnomalyBatchResponse] = {
    try {
      val response = processUsingApi(gson.toJson(series), batchDetectionUrl)
      println(response)
      // Deserializing the JSON response from the API into Scala types
      val anomaly = gson.fromJson(response, classOf[AnomalyBatchResponse])
      Thread.sleep(5000)
      return Some(anomaly)
    } catch {
      case e: Exception => {
        println(e)
        return None
      }
    }
  }
}
```

若要运行该 Notebook，请按 **SHIFT + ENTER**。 会显示一个输出，如以下代码片段所示。

    import java.io.{BufferedReader, DataOutputStream, InputStreamReader}
    import java.net.URL
    import java.sql.Timestamp
    import com.google.gson.{Gson, GsonBuilder, JsonParser}
    import javax.net.ssl.HttpsURLConnection
    defined class Point
    defined class Series
    defined class AnomalySingleResponse
    defined class AnomalyBatchResponse
    defined object AnomalyDetector

然后，准备一个供将来使用的聚合函数。
```scala
//
// User Defined Aggregation Function for Anomaly Detection
//

import org.apache.spark.sql.Row
import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
import scala.collection.immutable.ListMap

class AnomalyDetectorAggregationFunction extends UserDefinedAggregateFunction {
  override def inputSchema: StructType = new StructType().add("timestamp", TimestampType).add("value", FloatType)
  
  override def bufferSchema: StructType = new StructType().add("point", MapType(TimestampType, FloatType))
  
  override def dataType: DataType = BooleanType
  
  override def deterministic: Boolean = false
  
  override def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = Map()
  }
  
  override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    buffer(0) = buffer.getAs[Map[java.sql.Timestamp, Float]](0) + (input.getTimestamp(0) -> input.getFloat(1))
  }
  
  override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getAs[Map[java.sql.Timestamp, Float]](0) ++ buffer2.getAs[Map[java.sql.Timestamp, Float]](0)
  }
  
  override def evaluate(buffer: Row): Any = {
    val points = buffer.getAs[Map[java.sql.Timestamp, Float]](0)
    if (points.size > 12) {
      val sorted_points = ListMap(points.toSeq.sortBy(_._1.getTime):_*)
      var detect_points: List[Point] = List()
      sorted_points.keys.foreach {
        key => detect_points = detect_points :+ new Point(key, sorted_points(key))
      }
      
      
      // 0.25 is maxAnomalyRatio. It represents 25%, max anomaly ratio in a time series.
      // 95 is the sensitivity of the algorithms.
      // Check Anomaly detector API reference (https://aka.ms/anomaly-detector-rest-api-ref)
      
      val series: Series = new Series(detect_points.toArray, 0.25, 95, "hourly")
      val response: Option[AnomalySingleResponse] = AnomalyDetector.detectLatestPoint(series)
      if (!response.isEmpty) {
        return response.get.isAnomaly
      }
    }
    
    return None
  }
}

```

若要运行该 Notebook，请按 **SHIFT + ENTER**。 会显示一个输出，如以下代码片段所示。

    import org.apache.spark.sql.Row
    import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
    import org.apache.spark.sql.types.{StructType, TimestampType, FloatType, MapType, BooleanType, DataType}
    import scala.collection.immutable.ListMap
    defined class AnomalyDetectorAggregationFunction

然后，从事件中心加载数据，以便进行异常检测。 将占位符替换为前面创建的 Azure 事件中心的值。

```scala
//
// Load Data from Eventhub
//

import org.apache.spark.eventhubs._
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

val connectionString = ConnectionStringBuilder("[Placeholder: EventHub namespace connection string]")
  .setEventHubName("[Placeholder: EventHub name]")
  .build

val customEventhubParameters =
  EventHubsConf(connectionString)
  .setConsumerGroup("$Default")
  .setMaxEventsPerTrigger(100)

val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()

val messages =
  incomingStream
  .withColumn("enqueuedTime", $"enqueuedTime".cast(TimestampType))
  .withColumn("body", $"body".cast(StringType))
  .select("enqueuedTime", "body")

val bodySchema = new StructType().add("timestamp", TimestampType).add("favorite", IntegerType)

val msgStream = messages.select(from_json('body, bodySchema) as 'fields).select("fields.*")

msgStream.printSchema

display(msgStream)

```

现在，输出如下图所示。 请注意，你在表中的日期可能不同于本教程中的日期，因为数据是实时的。
![从事件中心加载数据](../media/tutorials/load-data-from-eventhub.png "从事件中心加载数据")

现在已通过适用于 Apache Spark 的事件中心连接器接近实时地将数据从 Azure 事件中心流式传输到 Azure Databricks 中。 有关如何使用适用于 Spark 的事件中心连接器的详细信息，请参阅[连接器文档](https://github.com/Azure/azure-event-hubs-spark/tree/master/docs)。



## <a name="run-anomaly-detection-on-tweets"></a>在推文中运行异常检测

在此部分，请对使用异常检测器 API 接收到的推文运行异常检测。 对于此部分，请将代码片段添加到同一 **AnalyzeTweetsFromEventHub** Notebook。

若要进行异常检测，首先需按小时聚合指标计数。
```scala
//
// Aggregate Metric Count by Hour
//

// If you want to change granularity, change the groupBy window. 
val groupStream = msgStream.groupBy(window($"timestamp", "1 hour"))
  .agg(avg("favorite").alias("average"))
  .withColumn("groupTime", $"window.start")
  .select("groupTime", "average")

groupStream.printSchema

display(groupStream)
```
现在，输出如以下代码片段所示。
```
groupTime                       average
2019-04-23T04:00:00.000+0000    24
2019-04-26T19:00:00.000+0000    47.888888888888886
2019-04-25T12:00:00.000+0000    32.25
2019-04-26T09:00:00.000+0000    63.4
...
...

```

然后，获取 Delta 的聚合输出结果。 由于异常检测需要较长的历史记录窗口，我们将使用 Delta 来保留需检测的时间点的历史记录数据。 将“[占位符: 表名]”替换为要创建的限定 Delta 表名（例如“tweets”）。 将“[占位符: 检查点的文件夹名称]”替换为一个字符串值，每次运行此代码时，该字符串值都是独一无二的（例如“etl-from-eventhub-20190605”）。
若要详细了解 Azure Databricks 上的 Delta Lake，请参阅 [Delta Lake Guide](https://docs.azuredatabricks.net/delta/index.html)（Delta Lake 指南）


```scala
//
// Output Aggregation Result to Delta
//

groupStream.writeStream
  .format("delta")
  .outputMode("complete")
  .option("checkpointLocation", "/delta/[Placeholder: table name]/_checkpoints/[Placeholder: folder name for checkpoints]")
  .table("[Placeholder: table name]")

```

将“[占位符: 表名]”替换为在上面选择的 Delta 表名。
```scala
//
// Show Aggregate Result
//

val twitterCount = spark.sql("SELECT COUNT(*) FROM [Placeholder: table name]")
twitterCount.show()

val twitterData = spark.sql("SELECT * FROM [Placeholder: table name] ORDER BY groupTime")
twitterData.show(200, false)

display(twitterData)
```
输出如下： 
```
groupTime                       average
2019-04-08T01:00:00.000+0000    25.6
2019-04-08T02:00:00.000+0000    6857
2019-04-08T03:00:00.000+0000    71
2019-04-08T04:00:00.000+0000    55.111111111111114
2019-04-08T05:00:00.000+0000    2203.8
...
...

```

现在，聚合的时序数据已持续引入到 Delta 中。 然后，我们可以计划一个小时作业，用于检测最新时间点的异常。 将“[占位符: 表名]”替换为在上面选择的 Delta 表名。

```scala
//
// Anomaly Detection
//

import java.time.Instant
import java.time.format.DateTimeFormatter
import java.time.ZoneOffset
import java.time.temporal.ChronoUnit

val detectData = spark.read.format("delta").table("[Placeholder: table name]")

// You could use Databricks to schedule an hourly job and always monitor the latest data point
// Or you could specify a const value here for testing purpose
// For example, val endTime = Instant.parse("2019-04-16T00:00:00Z")
val endTime = Instant.now()

// This is when your input of anomaly detection starts. It is hourly time series in this tutorial, so 72 means 72 hours ago from endTime.
val batchSize = 72
val startTime = endTime.minus(batchSize, ChronoUnit.HOURS)

val DATE_TIME_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss").withZone(ZoneOffset.UTC);

val series = detectData.filter($"groupTime" <= DATE_TIME_FORMATTER.format(endTime))
  .filter($"groupTime" > DATE_TIME_FORMATTER.format(startTime))
  .sort($"groupTime")

series.createOrReplaceTempView("series")

//series.show()

// Register the function to access it
spark.udf.register("anomalydetect", new AnomalyDetectorAggregationFunction)

val adResult = spark.sql("SELECT '" + endTime.toString + "' as datetime, anomalydetect(groupTime, average) as anomaly FROM series")
adResult.show()
```
结果如下： 

```
+--------------------+-------+
|           timestamp|anomaly|
+--------------------+-------+
|2019-04-16T00:00:00Z|  false|
+--------------------+-------+
```

就这么简单！ 现已成功使用 Azure Databricks 以接近实时的速度将数据流式传输到 Azure 事件中心，通过事件中心连接器使用了流数据，然后对流数据运行了异常检测。
虽然在本教程中粒度为小时，但我们始终可以根据自己的需要更改粒度。 

## <a name="clean-up-resources"></a>清理资源

运行完本教程后，可以终止群集。 为此，请在 Azure Databricks 工作区的左窗格中选择“群集”  。 针对想要终止的群集，将光标移到“操作”  列下面的省略号上，选择“终止”  图标，然后选择“确认”。 

![停止 Databricks 群集](../media/tutorials/terminate-databricks-cluster.png "停止 Databricks 群集")

如果不手动终止群集，但在创建群集时选中了“在不活动 \_\_ 分钟后终止”  复选框，则该群集会自动停止。 在这种情况下，如果群集保持非活动状态超过指定的时间，则会自动停止。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Azure Databricks 将数据流式传输到 Azure 事件中心，然后从事件中心实时读取流数据。 继续学习下一教程，了解如何调用异常检测器 API 并使用 Power BI Desktop 将异常可视化。 

> [!div class="nextstepaction"]
>[使用 Power BI Desktop 进行批量异常检测](batch-anomaly-detection-powerbi.md)

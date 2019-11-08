---
title: 教程：Azure 事件中心数据和 Apache Spark - HDInsight
description: 教程 - 将 Azure HDInsight 中的 Apache Spark 连接到 Azure 事件中心并处理流数据。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/17/2019
ms.openlocfilehash: 0b24d1b0215564fb9f6063d4a2d091bb7a9a1c3e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494631"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-apache-spark-in-hdinsight"></a>教程：在 HDInsight 中使用 Azure 事件中心和 Apache Spark 处理推文

本教程介绍如何创建 [Apache Spark](https://spark.apache.org/) 流式处理应用程序，用于将推文发送到 Azure 事件中心；以及如何创建另一个应用程序，用于从事件中心读取推文。 有关 Spark 流式处理的详细说明，请参阅 [Apache Spark 流式处理概述](https://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)。 HDInsight 将相同的流式处理功能引入到 Azure 上的 Spark 群集。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 将消息发送到 Azure 事件中心
> * 从 Azure 事件中心读取消息

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 请参阅[创建 Apache Spark 群集](./apache-spark-jupyter-spark-sql-use-portal.md)。

* 熟悉 Jupyter Notebook 和 Spark on HDInsight 的结合使用。 有关详细信息，请参阅[使用 Apache Spark on HDInsight 加载数据并运行查询](./apache-spark-load-data-run-query.md)。

* [Twitter 帐户](https://twitter.com/i/flow/signup)，并熟悉 Twitter。

## <a name="create-a-twitter-application"></a>创建 Twitter 应用程序

若要接收推文流，请在 Twitter 中创建一个应用程序。 遵照说明创建一个 Twitter 应用程序，并记下稍后需要在本教程中使用的值。

1. 浏览到 [Twitter 应用程序管理](https://apps.twitter.com/)。

1. 选择“创建应用”  。

1. 提供以下所需值：

    |属性 |值 |
    |---|---|
    |应用程序名称|提供应用程序名称。 本教程使用的值为 **HDISparkStreamApp0423**。 此名称必须是唯一的名称。|
    |应用程序说明|提供应用程序的简短说明。 本教程使用的值为“一个简单的 HDInsight Spark 流式处理应用程序”。 |
    |网站 URL|提供应用程序的网站。 不必是有效网站。  本教程使用的值为 `http://www.contoso.com`。|
    |告诉我们如何使用此应用|仅用于测试目的。 创建 Apache Spark 流式处理应用程序，将推文发送到 Azure 事件中心。|

1. 选择“创建”  。

1. 从“查看开发人员术语”弹出窗口中，选择“创建”   。

1. 选择“密钥和令牌”选项卡  。

1. 在“访问令牌和访问令牌机密”下，选择“创建”   。

1. 记下现在出现在页面上的以下四个值，以备后用：

    - **使用者密钥（API 密钥）**
    - **使用者机密（API 密钥）**
    - **访问令牌**
    - **访问令牌机密**

## <a name="create-an-azure-event-hubs-namespace"></a>创建一个 Azure 事件中心命名空间

使用此事件中心来存储推文。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 从左侧菜单，导航至“所有服务” > “物联网” > “事件中心”    。  

    ![为 Spark 流式处理创建事件中心示例](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "为 Spark 流式处理创建事件中心示例")

1. 选择“+ 添加”  。

1. 为新的事件中心命名空间输入以下值：

    |属性 |值 |
    |---|---|
    |Name|输入事件中心的名称。  本教程使用的值为 **myeventhubns20180403**。|
    |定价层|选择“标准”  。|
    |Subscription|选择相应的订阅。|
    |Resource group|从下拉列表中选择现有资源组，或者选择“新建”来创建新的资源组  。|
    |位置|选择与 HDInsight 中 Apache Spark 群集相同的“位置”，以降低延迟和成本。 |
    |启用自动膨胀（可选） |当流量超出了分配给事件中心命名空间的吞吐量单位数时，自动膨胀会自动扩展该数量。  |
    |自动膨胀最大吞吐量单位数（可选）|只有当选中了“启用自动膨胀”时才会显示此滑块。   |

    ![为 Spark 流式处理提供事件中心名称示例](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "为 Spark 流式处理提供事件中心名称示例")

1. 选择“创建”以创建命名空间。   部署将在几分钟内完成。

## <a name="create-an-azure-event-hub"></a>创建 Azure 事件中心

部署事件中心命名空间后，创建一个事件中心。  在门户中：

1. 从左侧菜单，导航至“所有服务” > “物联网” > “事件中心”    。

1. 从列表中选择你的事件中心命名空间。

1. 在“事件中心命名空间”页面中，选择“+ 事件网格”。    

1. 在“创建事件中心”  页面中输入以下值：

    - **名称**：为事件中心提供一个名称。

    - **分区计数**：10.  

    - **消息保留期**：1.

      ![为 Spark 流式处理提供事件中心详细信息示例](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "为 Spark 流式处理提供事件中心详细信息示例")

1. 选择“创建”  。  部署应在几秒内完成，你将返回到“事件中心命名空间”页面。

1. 在“设置”下，选择“共享访问策略”。  

1. 选择“RootManageSharedAccessKey”。 

     ![为 Spark 流式处理设置事件中心策略示例](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "为 Spark 流式处理设置事件中心策略示例")

1. 保存“主密钥”和“连接字符串主密钥”的值，以便稍后在本教程中使用。  

     ![查看 Spark 流式处理的事件中心策略密钥示例](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "查看 Spark 流式处理的事件中心策略密钥示例")

## <a name="send-tweets-to-the-event-hub"></a>将推文发送到事件中心

1. 导航至 `https://CLUSTERNAME.azurehdinsight.net/jupyter`，其中 `CLUSTERNAME` 是 Apache Spark 群集的名称。 创建一个 Jupyter Notebook，并将其命名为 **SendTweetsToEventHub**。

1. 运行以下代码，添加外部 Apache Maven 库：

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

1. 编辑以下代码，方法是将 `<Event hub name>`、`<Event hub namespace connection string>`、`<CONSUMER KEY>`、`<CONSUMER SECRET>`、`<ACCESS TOKEN>`、`<TOKEN SECRET>` 替换为适当的值。 运行编辑的代码，将推文发送到事件中心：

    ```scala
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._

    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)

    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }

    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"

    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)

    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!

    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }

    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

1. 在 Azure 门户中打开事件中心。  在“概述”中应会看到一些图表，其中显示了发送到事件中心的消息。 

## <a name="read-tweets-from-the-event-hub"></a>从事件中心读取推文

创建另一个 Jupyter Notebook，并将其命名为 **ReadTweetsFromEventHub**。

1. 运行以下代码，添加一个外部 Apache Maven 库：

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.13"}}
    ```

2. 编辑以下代码，方法是将 `<Event hub name>` 和 `<Event hub namespace connection string>` 替换为适当的值。 运行编辑的代码，从事件中心读取推文：

    ```scala
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build

    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema

    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")

    messages.printSchema

    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>清理资源

有了 HDInsight，便可以将数据存储在 Azure 存储或 Azure Data Lake Storage 中，以便在群集不用时安全地删除群集。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 如果打算立即开始学习下一教程，可能需要保留该群集，否则请继续并删除该群集。

在 Azure 门户中打开群集，然后选择“删除”  。

![HDInsight Azure 门户删除群集](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "删除 HDInsight 群集")

还可以选择资源组名称来打开“资源组”页，然后选择“删除资源组”  。 通过删除资源组，可以删除 HDInsight Spark 群集和默认存储帐户。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何创建 Apache Spark 流式处理应用程序，以便将推文发送到 Azure 事件中心；以及如何创建另一个应用程序，以便从事件中心读取推文。  请继续学习下一篇文章，试着创建一个机器学习应用程序。

> [!div class="nextstepaction"]
> [创建机器学习应用程序](./apache-spark-ipython-notebook-machine-learning.md)

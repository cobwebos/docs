---
title: 教程：使用 Azure HDInsight 中的 Apache Spark 处理来自 Azure 事件中心的数据 | Microsoft Docs
description: 将 Azure HDInsight 中的 Apache Spark 连接到 Azure 事件中心并处理流数据。
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 9b59f5d58234aaf8f8385f722d6659548e066933
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781406"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>教程：使用 HDInsight 中的 Azure 事件中心和 Spark 处理推文

本教程介绍如何创建一个 Apache Spark 流式处理应用程序用于将推文发送到 Azure 事件中心，并创建另一个应用程序用于从事件中心读取推文。 有关 Spark 流式处理的详细说明，请参阅 [Apache Spark 流式处理概述](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview)。 HDInsight 将相同的流式处理功能引入到 Azure 上的 Spark 群集。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
> * 将消息发送到 Azure 事件中心
> * 从 Azure 事件中心读取消息

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* **完成[教程：在 Azure HDInsight 中的 Apache Spark 群集上加载数据并运行查询](./apache-spark-load-data-run-query.md)一文**。

## <a name="create-a-twitter-application"></a>创建 Twitter 应用程序

若要接收推文流，请在 Twitter 中创建一个应用程序。 遵照说明创建一个 Twitter 应用程序，并记下稍后需要在本教程中使用的值。

1. 浏览到 [Twitter 应用程序管理](https://apps.twitter.com/)。
2. 选择“创建新应用”。
3. 提供以下值：

    - 名称：提供应用程序名称。 本教程使用的值为 **HDISparkStreamApp0423**。 此名称必须是唯一的名称。
    - 说明：提供应用程序的简短说明。 本教程使用的值为“一个简单的 HDInsight Spark 流式处理应用程序”。
    - 网站：提供应用程序的网站。 不必是有效网站。  本教程使用的值为 **http://www.contoso.com**。
    - 回调 URL：可以留空。

4. 选择“是，我已阅读并同意 Twitter 开发人员协议”，然后选择“创建 Twitter 应用程序”。
5. 选择“密钥和访问令牌”选项卡。
6. 选择页面末尾的“创建访问令牌”。
7. 记下页面中的以下值。  本教程稍后需要用到这些值：

    - **使用者密钥（API 密钥）**    
    - **使用者机密（API 机密）**  
    - **访问令牌**
    - **访问令牌机密**   

## <a name="create-an-azure-event-hub"></a>创建 Azure 事件中心

使用此事件中心来存储推文。

1. 登录到 [Azure 门户](https://ms.portal.azure.com)。
2. 选择屏幕左上角的“创建资源”。
3. 依次选择“物联网”、“事件中心”。

    ![为 Spark 流式处理示例创建事件中心](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "为 Spark 流式处理示例创建事件中心")
4. 为新事件中心命名空间输入以下值：

    - **名称**：输入事件中心的名称。  本教程使用的值为 **myeventhubns20180403**。
    - **定价层**：选择“标准”。
    - **资源组**：可以选择新建，或选择 Spark 群集的资源组。 
    - **位置**：选择与 HDInsight 中 Apache Spark 群集相同的“位置”，以降低延迟和成本。

    ![提供 Spark 流式处理示例的事件中心名称](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "提供 Spark 流式处理示例的事件中心名称")
5. 选择“创建”以创建命名空间。

6. 遵照以下说明打开事件中心命名空间：

    1. 在门户中，选择“所有服务”。
    2. 在筛选器框中，输入“事件中心”。
    3. 双击创建的命名空间。
    4. 选择“+ 事件中心”。

6. 在事件中心命名空间列表中，选择新建的命名空间。      
5. 选择“事件中心”，然后选择“+ 事件中心”创建新事件中心。
  

6. 输入以下值：

    - 名称：为事件中心命名。
    - 分区计数：10
    - 消息保留期：1 
   
    ![提供 Spark 流式处理示例的事件中心详细信息](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "提供 Spark 流式处理示例的事件中心详细信息")

7. 选择**创建**。
8. 为命名空间选择“共享的访问策略”（请注意，它不是事件中心共享的访问策略），然后选择“RootManageSharedAccessKey”。
    
     ![设置 Spark 流式处理示例的事件中心策略](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "设置 Spark 流式处理示例的事件中心策略")

9. 保存“主密钥”和“连接字符串主密钥”的值，以便稍后在本教程中使用。

     ![查看 Spark 流式处理示例的事件中心策略密钥](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "查看 Spark 流式处理示例的事件中心策略密钥")


## <a name="send-tweets-to-the-event-hub"></a>将推文发送到事件中心

需要创建一个 Jupyter Notebook，并将其命名为 **SendTweetsToEventHub**。 

1. 运行以下代码，添加外部 Maven 库：

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. 运行以下代码，将推文发送到事件中心：

    ```
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

3. 在 Azure 门户中打开事件中心。  在“概述”中应会看到一些图表，其中显示了发送到事件中心的消息。

## <a name="read-tweets-from-the-event-hub"></a>从事件中心读取推文

需要创建另一个 Jupyter Notebook，并将其命名为 **ReadTweetsFromEventHub**。 

1. 运行以下代码，添加一个外部 Maven 库：

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. 运行以下代码，从事件中心读取推文：

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connectionString).setMaxEventsPerTrigger(5)
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

使用 HDInsight 可将数据存储在 Azure 存储或 Azure Data Lake Store 中，以便可以在群集未用时安全地将其删除。 此外，还需要为 HDInsight 群集付费，即使不用也是如此。 由于群集费用数倍于存储空间费用，因此在群集不用时删除群集可以节省费用。 如果你打算立即学习下一篇教程，可以保留群集。

在 Azure 门户中打开群集，然后选择“删除”。

![删除 HDInsight 群集](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "删除 HDInsight 群集")

还可以选择资源组名称来打开“资源组”页，然后选择“删除资源组”。 删除资源组会删除 HDInsight Spark 群集和默认的存储帐户。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

* 从事件中心读取消息。
请继续学习下一篇文章，试着创建一个机器学习应用程序。 

> [!div class="nextstepaction"]
> [创建机器学习应用程序](./apache-spark-ipython-notebook-machine-learning.md)



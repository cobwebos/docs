---
title: "使用 Apache Storm 从 Azure 事件中心接收事件 | Microsoft 文档"
description: "使用 Apache Storm 从事件中心接收事件入门"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: java
ms.devlang: multiple
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.openlocfilehash: 3e15370c7602276ef323708632b324fe05497f41
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="receive-events-from-event-hubs-using-apache-storm"></a>使用 Apache Storm 从事件中心接收事件

[Apache Storm](https://storm.incubator.apache.org) 是一个分布式实时计算系统，它简化了对未绑定的数据流进行可靠处理的过程。 本节演示如何使用 Azure 事件中心 Storm Spout 从事件中心接收事件。 使用 Apache Storm，可以在承载于不同节点的多个进程间拆分事件。 事件中心与 Storm 集成后，通过使用风暴的 Zookeeper 安装以透明方式对事件使用进度执行检查点操作、管理持久检查点以及从事件中心并行接收，简化了事件使用。

有关事件中心接收模式的详细信息，请参阅[事件中心概述][Event Hubs overview]。

## <a name="create-project-and-add-code"></a>创建项目并添加代码

本教程使用安装的 [HDInsight Storm][HDInsight Storm]，其中随附了现成可用的事件中心 Spout。

1. 请按照 [HDInsight Storm - 入门](../hdinsight/hdinsight-storm-overview.md)过程创建新 HDInsight 群集，并通过远程桌面连接该群集。
2. 将 `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` 文件复制到本地开发环境。 其中包含 events-storm-spout。
3. 使用以下命令将程序包安装到本地 Maven 存储中。 这样，在后面的步骤中，便可以在 Storm 项目中将它添加为引用。

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. 在 Eclipse 中创建一个新的 Maven 项目（依次单击“文件”、“新建”、“项目”）。
   
    ![][12]
5. 选择“使用默认工作区位置”，并单击“下一步”
6. 选择“maven-archetype-quickstart”原型，并单击“下一步”
7. 插入 **GroupId** 和 **ArtifactId**，并单击“完成”
8. 在 **pom.xml** 中的 `<dependency>` 节点内添加以下依赖项。

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. 在 src 文件夹中，创建一个名为 Config.properties 的文件，并复制以下内容，替换值 `receive rule key` 和 `event hub name`：

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    **eventhub.receiver.credits** 的值决定在将事件发布到 Storm 管道之前先进行批处理的事件的数量。 为了简单起见，本示例将此值设置为 10。 在生产中，通常应将它设置为较高的值，例如 1024。
10. 使用以下代码创建名为 **LoggerBolt** 的新类：
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    此 Storm 螺栓记录接收到的事件的内容。 在存储服务中，它可以轻松扩展为存储元组。 [HDInsight 传感器分析教程]同样使用这种方法将数据存储到 HBase 中。
11. 使用以下代码创建一个名为 **LogTopology** 的类：
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    此类创建一个新的事件中心喷管，并使用配置文件中的属性对它进行实例化。 请务必注意，此示例创建 Spout 的数量与事件中心内分区的数量相同，以便使用该事件中心允许的最大并行度。

## <a name="next-steps"></a>后续步骤
访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述][Event Hubs overview]
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight Storm]: ../hdinsight/hdinsight-storm-overview.md
[HDInsight 传感器分析教程]: ../hdinsight/hdinsight-storm-sensor-data-analysis.md

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png

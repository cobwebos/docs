---
title: 使用开源工具可视化 Azure 网络观察程序 NSG 流日志 | Microsoft 文档
description: 本页介绍如何使用开源工具可视化 NSG 流日志。
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f7d51352aa8411e36f4224804c90c2554d4ef9e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394164"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>使用开源工具可视化 Azure 网络观察程序 NSG 流日志

可以通过网络安全组流日志提供的信息了解网络安全组的入口和出口 IP 流量。 这些流日志根据规则显示出站和入站流、流所适用的 NIC、有关流的 5 元组信息（源/目标 IP、源/目标端口、协议），以及是允许还是拒绝流量。

这些流日志可能难以手动分析并获取见解。 不过，可以使用多个开源工具将相关数据可视化。 本文将提供一个使用 Elastic Stack 将这些日志可视化的解决方案，方便用户在 Kibana 仪表板上快速完成流日志的索引和可视化操作。

## <a name="scenario"></a>方案

在本文中，我们将设置一个解决方案，方便用户使用 Elastic Stack 来可视化网络安全组流日志。  将使用 Logstash 输入插件直接从已配置为存储流日志的存储 Blob 获取流日志。 然后使用 Elastic Stack 为流日志编制索引并将这些日志用于创建 Kibana 仪表板，实现信息的可视化。

![方案][scenario]

## <a name="steps"></a>步骤

### <a name="enable-network-security-group-flow-logging"></a>启用网络安全组流日志记录
就本方案来说，必须在帐户的至少一个网络安全组上启用网络安全组流日志记录。 有关启用网络安全组流日志的说明，请参阅以下文章：[网络安全组流日志记录简介](network-watcher-nsg-flow-logging-overview.md)。


### <a name="set-up-the-elastic-stack"></a>设置 Elastic Stack
通过将 NSG 流日志与 Elastic Stack 相连接，可以创建一个 Kibana 仪表板，以便搜索、可视化、分析日志并从中获得见解。

#### <a name="install-elasticsearch"></a>安装 Elasticsearch

1. Elastic Stack 5.0 及更高版本需要 Java 8。 运行命令 `java -version` 可以检查版本。 如果未安装 Java，请参阅 [Oracle 网站](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)上的文档
1. 下载适用于系统的正确二进制程序包：

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)（Elasticsearch 安装）中介绍了其他安装方法

1. 使用以下命令验证 Elasticsearch 是否正在运行：

    ```bash
    curl http://127.0.0.1:9200
    ```

    应会显示如下所示的响应：

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

有关安装 Elasticsearch 的其他说明，请参阅 [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)（安装）页

### <a name="install-logstash"></a>安装 Logstash

1. 若要安装 Logstash，请运行以下命令：

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. 接下来，需要配置 Logstash，以访问和分析流日志。 使用以下命令创建 logstash.conf 文件：

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. 将以下内容添加到文件：

  ```
input {
   azureblob
     {
         storage_account_name => "mystorageaccount"
         storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
         container => "insights-logs-networksecuritygroupflowevent"
         codec => "json"
         # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
         # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
         file_head_bytes => 12
         file_tail_bytes => 2
         # Enable / tweak these settings when event is too big for codec to handle.
         # break_json_down_policy => "with_head_tail"
         # break_json_batch_count => 2
     }
   }

   filter {
     split { field => "[records]" }
     split { field => "[records][properties][flows]"}
     split { field => "[records][properties][flows][flows]"}
     split { field => "[records][properties][flows][flows][flowTuples]"}

  mutate{
   split => { "[records][resourceId]" => "/"}
   add_field => {"Subscription" => "%{[records][resourceId][2]}"
                 "ResourceGroup" => "%{[records][resourceId][4]}"
                 "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
   convert => {"Subscription" => "string"}
   convert => {"ResourceGroup" => "string"}
   convert => {"NetworkSecurityGroup" => "string"}
   split => { "[records][properties][flows][flows][flowTuples]" => ","}
   add_field => {
               "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
               "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
               "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
               "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
               "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
               "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
               "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
               "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                }
   convert => {"unixtimestamp" => "integer"}
   convert => {"srcPort" => "integer"}
   convert => {"destPort" => "integer"}        
  }

  date{
    match => ["unixtimestamp" , "UNIX"]
  }
 }
output {
  stdout { codec => rubydebug }
  elasticsearch {
    hosts => "localhost"
    index => "nsg-flow-logs"
  }
}  
  ```

有关安装 Logstash 的其他说明，请参阅[正式文档](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>安装适用于 Azure Blob 存储的 Logstash 输入插件

可以使用该 Logstash 插件直接从指定的存储帐户访问流日志。 若要安装此插件，请从默认 Logstash 安装目录（在此示例中为 /usr/share/logstash/bin）运行以下命令：

```bash
logstash-plugin install logstash-input-azureblob
```

若要启动 Logstash，请运行以下命令：

```bash
sudo /etc/init.d/logstash start
```

有关该插件的详细信息，请参阅[此处](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)的文档

### <a name="install-kibana"></a>安装 Kibana

1. 运行以下命令以安装 Kibana：

  ```bash
  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
  ```

1. 若要运行 Kibana，请使用以下命令：

  ```bash
  cd kibana-5.2.0-linux-x86_64/
  ./bin/kibana
  ```

1. 若要查看 Kibana Web 界面，请导航到 `http://localhost:5601`
1. 对于本方案，用于流日志的索引模式为“nsg-flow-logs”。 可以更改 logstash.conf 文件“output”节中的索引模式。

1. 如果想要远程查看 Kibana 仪表板，请创建允许访问**端口 5601** 的入站 NSG 规则。

### <a name="create-a-kibana-dashboard"></a>创建 Kibana 仪表板

在本文中，我们提供了一个示例仪表板，用于查看警报中的趋势和详细信息。

![图 1][1]

1. 在[此处](https://aka.ms/networkwatchernsgflowlogdashboard)下载仪表板文件，在[此处](https://aka.ms/networkwatchernsgflowlogvisualizations)下载可视化效果文件，在[此处](https://aka.ms/networkwatchernsgflowlogsearch)下载已保存的搜索文件。

1. 在 Kibana 的“Management”（管理）选项卡下，导航到“Saved Objects”（已保存的对象）并导入所有三个文件。 然后，可从“仪表板”选项卡打开并加载示例仪表板。

还可以创建自己的可视化效果，以及根据感兴趣的指标定制的仪表板。 阅读 Kibana 的[正式文档](https://www.elastic.co/guide/en/kibana/current/visualize.html)，详细了解如何创建 Kibana 可视化效果。

### <a name="visualize-nsg-flow-logs"></a>可视化 NSG 流日志

示例仪表板提供了流日志的多种可视化效果：

1. 按一段时间的决策/方向显示的流 - 时间系列图，显示一段时间内流的数目。 可以编辑这些可视化效果的时间单位和跨度。 “按决策显示的流”显示所做的允许或拒绝决策所占的比例，而“按方向显示的流”则显示入站和出站流量的比例。 用户可以利用这些可视化效果检查一段时间内的流量趋势，查看是否存在峰值或异常模式。

  ![图 2][2]

1. 按目标端口/源端口显示的流 - 饼图，显示流向各自端口的流的明细。 可以通过此视图查看最常用的端口。 如果单击饼图中的特定端口，仪表板的其余部分就会对流进行筛选，仅保留该端口的流。

  ![图 3][3]

1. 流的数目和最早的日志时间 - 此指标显示记录的流的数目，以及捕获的最早日志的日期。

  ![图 4][4]

1. 按 NSG 和规则显示的流 - 条形图，显示每个 NSG 中流的分布情况，以及每个 NSG 中规则的分布情况。 可以通过此图了解产生最多流量的 NSG 和规则。

  ![图 5][5]

1. 排名前 10 的源/目标 IP - 条形图，显示排名前 10 的源 IP 和目标 IP。 可以调整这些图表，增加或减少排名靠前的 IP 的显示数目。 可以通过此图了解最常出现的 IP，以及针对每个 IP 所做的流量决策（允许或拒绝）。

  ![图 6][6]

1. 流元组 - 此表显示包含在每个流元组中的信息及其相应的 NSG 和规则。

  ![图 7][7]

用户可以使用仪表板顶部的查询栏，根据流的任何参数（例如订阅 ID、资源组、规则或者任何其他感兴趣的变量）对仪表板的内容进行筛选。 有关 Kibana 的查询和筛选器的详细信息，请参阅[正式文档](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>结束语

通过组合使用网络安全组流日志和 Elastic Stack，我们提出了一种用于可视化网络流量的方式，该方式功能强大并且可以自定义。 用户可以通过这些仪表板快速获取和共享针对网络流量的见解，并且可以通过筛选来调查任何潜在的异常问题。 使用 Kibana 时，用户可以根据安全、审核和符合性需要对这些仪表板进行定制，打造特定的可视化效果。

## <a name="next-steps"></a>后续步骤

访问[使用 Power BI 可视化 NSG 流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)，了解如何使用 Power BI 可视化 NSG 流日志


<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png

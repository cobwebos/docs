---
title: "使用 Azure 网络观察程序和开源工具执行网络入侵检测 | Microsoft 文档"
description: "本文介绍如何使用 Azure 网络观察程序和开源工具执行网络入侵检测"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: d60b1d44844c449e0f66dc0107a25531569d097b
ms.openlocfilehash: 82d5e525859ebe03b152c63e4debbae469049c12
ms.lasthandoff: 03/31/2017

---

# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>使用网络观察程序和开源工具执行网络入侵检测

数据包捕获是一个重要组件，可以实施网络入侵检测系统 (IDS) 并执行网络安全监视 (NSM)。 可以借助多种开源 IDS 工具来处理数据包捕获，并检查潜在网络入侵和恶意活动的签名。 使用网络观察程序提供的数据包捕获，可以分析网络中是否存在任何有害入侵或漏洞。

Suricata 就是这样的一种开源工具，它是一个 IDS 引擎，可使用规则集来监视网络流量，每当出现可疑事件时，它会触发警报。 Suricata 提供多线程引擎，意味着它能够以更高的速度和效率执行网络流量分析。 有关 Suricata 及其功能的更多详细信息，请访问其网站 https://suricata-ids.org/。

## <a name="scenario"></a>方案

本文介绍如何将环境设置为使用网络观察程序、Suricata 和 Elastic Stack 执行网络入侵检测。 网络观察程序提供用于执行网络入侵检测的数据包捕获。 Suricata 处理数据包捕获，并根据与其给定威胁规则集匹配的数据包触发警报。 这些警报存储在本地计算机上的某个日志文件中。 使用 Elastic Stack 可为 Suricata 生成的日志编制索引，并使用这些日志创建 Kibana 仪表板，提供日志的可视化形式，同时，提供潜在网络漏洞的见解。  

![简单的 Web 应用程序方案][1]

可在 Azure VM 上设置这两个开源工具，以便在自己的 Azure 网络环境内部执行此分析。

## <a name="steps"></a>步骤

### <a name="install-suricata"></a>安装 Suricata

有关其他所有安装方法，请访问 http://suricata.readthedocs.io/en/latest/install.html

1. 在 VM 的命令行终端中运行以下命令：

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. 若要验证安装，请运行命令 `suricata -h` 查看命令的完整列表。

### <a name="download-the-emerging-threats-ruleset"></a>下载 Emerging Threats 规则集

目前，我们尚未创建运行 Suricata 所需的任何规则。 如果想要检测特定的网络威胁，可以创建自己的规则；或者，也可以使用许多提供商开发的规则集，例如 Emerging Threats，或 Snort 开发的 VRT 规则。 本文使用可免费访问的 Emerging Threats 规则集：

下载该规则集，并将其复制到目录：

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>使用 Suricata 处理数据包捕获

若要使用 Suricata 处理数据包捕获，请运行以下命令：

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
若要检查生成的警报，请阅读 fast.log 文件：
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>设置 Elastic Stack

尽管 Suricata 生成的日志包含有关网络情况的重要信息，但这些日志文件并不是很容易阅读和理解。 通过将 Suricata 与 Elastic Stack 相连接，可以创建一个 Kibana 仪表板，从而可以搜索、可视化、分析日志并从中获得见解。

#### <a name="install-elasticsearch"></a>安装 Elasticsearch

1. Elastic Stack 5.0 和更高版本需要 Java 8。 运行命令 `java -version` 可以检查版本。 如果未安装 Java，请参阅 [Oracle 网站](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)上的文档
1. 下载适用于系统的正确二进制程序包：

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)（Elasticsearch 安装）中介绍了其他安装方法

1. 使用以下命令验证 Elasticsearch 是否正在运行：

    ```
    curl http://127.0.0.1:9200
    ```

    应会显示如下所示的响应：

    ```
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

有关安装 Elastic Search 的其他说明，请参阅 [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)（安装）页

### <a name="install-logstash"></a>安装 Logstash

1. 若要安装 Logstash，请运行以下命令：

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. 接下来，需要将 Logstash 配置为读取 eve.json 文件的输出。 使用以下命令创建 logstash.conf 文件：

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. 将以下内容添加到该文件（确保 eve.json 文件的路径正确）：

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. 确保提供对 eve.json 文件的适当权限，使 Logstash 能够引入该文件。
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. 若要启动 Logstash，请运行以下命令：

    ```
    sudo /etc/init.d/logstash start
    ```

有关安装 Logstash 的其他说明，请参阅[正式文档](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>安装 Kibana

1. 运行以下命令安装 Kibana：

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. 若要运行 Kibana，请使用以下命令：

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. 若要查看 Kibana Web 界面，请导航到 `http://localhost:5601`
1. 对于本方案，用于 Suricata 日志的索引模式为“logstash-*”

1. 如果想要远程查看 Kibana 仪表板，请创建允许访问**端口 5601** 的入站 NSG 规则。

### <a name="create-a-kibana-dashboard"></a>创建 Kibana 仪表板

在本文中，我们提供了一个示例仪表板用于查看警报中的趋势和详细信息。

1. 在[此处](https://aka.ms/networkwatchersuricatadashboard)下载仪表板文件，在[此处](https://aka.ms/networkwatchersuricatavisualization)下载可视化效果文件，在[此处](https://aka.ms/networkwatchersuricatasavedsearch)下载已保存的搜索文件。

1. 在 Kibana 的“Management”（管理）选项卡下，导航到“Saved Objects”（已保存的对象）并导入所有三个文件。 然后，可从“Dashboard”（仪表板）选项卡打开并加载示例仪表板。

还可以创建自己的可视化效果，以及根据感兴趣的指标定制的仪表板。 阅读 Kibana 的[正式文档](https://www.elastic.co/guide/en/kibana/current/visualize.html)，详细了解如何创建 Kibana 可视化效果。

![Kibana 仪表板][2]

### <a name="visualize-ids-alert-logs"></a>可视化 IDS 警报日志

示例仪表板提供了 Suricata 警报日志的多种可视化效果：

1. 按 GeoIP 列出警报 – 基于地理位置，按来源国家/地区（由 IP 确定）显示警报分布的地图

    ![地理 IP][3]

1. 排名靠前的 10 条警报 – 最常触发的 10 条警报的摘要及其说明。 单击单个警报可以进一步筛选仪表板中的内容，以便只显示与该特定警报相关的信息。

    ![图 4][4]

1. 警报数 – 规则集触发的警报总数

    ![图 5][5]

1. 排名靠前的 20 个源/目标 IP/端口 – 显示触发警报次数最多的前 20 个 IP 和端口的饼图。 可以进一步筛选特定的 IP/端口，以查看触发了多少以及哪些类型的警报。

    ![图 6][6]

1. 警报摘要 – 汇总每个警报的具体详细信息的表格。 可以自定义此表，以显示每条警报的其他想要了解的参数。

    ![图 7][7]

有关创建自定义可视化效果和仪表板的更多文档，请参阅 [Kibana 的正式文档](https://www.elastic.co/guide/en/kibana/current/introduction.html)。

## <a name="conclusion"></a>结束语

通过将网络观察程序提供的数据包捕获与 Suricata 等开源 IDS 工具相结合，可以针对各种威胁执行网络入侵检测。 使用这些仪表板可以快速探查网络中的趋势和异常，以及挖掘数据来发现恶意用户代理或有漏洞的端口触发警报的根本原因。 使用这些提取的数据，可以在如何抵御网络中的任何有害入侵企图方面做出明智的决策，并创建规则来防范网络中将来发生入侵。

## <a name="next-steps"></a>后续步骤

访问 [Use packet capture to do proactive network monitoring with Azure Functions](network-watcher-alert-triggered-packet-capture.md)（在 Azure Functions 中使用数据包捕获执行主动网络监视），了解如何根据警报触发数据包捕获

访问 [Visualize NSG flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)（使用 Power BI 可视化 NSG 流日志），了解如何使用 Power BI 可视化 NSG 流日志



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png


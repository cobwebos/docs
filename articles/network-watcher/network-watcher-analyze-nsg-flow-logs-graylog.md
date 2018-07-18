---
title: 分析 Azure 网络安全组流日志 - Graylog | Microsoft Docs
description: 了解如何在 Azure 中使用网络观察程序与 Graylog 来管理和分析网络安全组流日志。
services: network-watcher
documentationcenter: na
author: mareat
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: 8d82ffa84c3d75ec3acd102a2de2bdce3718a995
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
ms.locfileid: "26639280"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>在 Azure 中使用网络观察程序与 Graylog 来管理和分析网络安全组流日志

[网络安全组流日志](network-watcher-nsg-flow-logging-overview.md)提供可用来了解 Azure 网络接口入口和出口 IP 流量的信息。 流日志根据网络安全组规则显示出站和入站流、流应用到的网络接口、有关流的 5 元组信息（源/目标 IP、源/目标端口、协议），以及是允许还是拒绝了流量。

可以在启用了流日志记录的网络中使用许多的网络安全组。 使用多个已启用流日志记录的网络安全组，可能会导致从日志中分析和获取见解变得非常麻烦。 本文提供一种解决方法，即使用 Graylog（开源日志管理和分析工具）和 Logstash（开源服务器端数据处理管道）来集中管理这些网络安全组流日志。

## <a name="scenario"></a>方案

已使用网络观察程序启用网络安全组流日志。 流日志流入 Azure Blob 存储。 Logstash 插件用于连接和处理 Blob 存储中的流日志并将其发送到 Graylog。 将流日志存储到 Graylog 中之后，可对其进行分析，并在自定义的仪表板中将其可视化。

![Graylog 工作流]](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>安装步骤

### <a name="enable-network-security-group-flow-logging"></a>启用网络安全组流日志记录

对于本方案，必须在帐户的至少一个网络安全组上启用网络安全组流日志记录。 有关启用网络安全组流日志的说明，请参阅以下文章：[网络安全组流日志记录简介](network-watcher-nsg-flow-logging-overview.md)。

### <a name="setting-up-graylog"></a>安装 Graylog

对于本示例，需在 Azure 中部署的 Ubuntu 14.04 服务器上配置 Graylog 和 Logstash。

- 请参阅 Graylog 提供的[文档](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html)，获取有关在 Ubuntu 上进行安装的分步说明。
- 另请确保遵循[文档](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)配置 Graylog Web 界面。

本示例使用 Graylog 最低安装要求（即 单个 Graylog 实例），但可将 Graylog 构建为根据系统和生产需求跨资源进行缩放。 有关体系结构注意事项的详细信息或体系结构深入指南，请参阅 Graylog 的[文档](http://docs.graylog.org/en/2.2/pages/architecture.html)和[体系结构指南](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)。

可根据平台和偏好，以多种方式安装 Graylog。 有关可能的安装方法的完整列表，请参阅 Graylog 的官方[文档](http://docs.graylog.org/en/2.2/pages/installation.html)。 Graylog 服务器应用程序在 Linux 分发版上运行，附带以下先决条件：

-   Oracle Java SE 8 或更高版本 – [Oracle 安装文档](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-   Elastic Search 2.x（2.1.0 或更高版本）– [Elasticsearch 安装文档](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-   MongoDB 2.4 或更高版本 – [MongoDB 安装文档](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>安装 Logstash

Logstash 用于将 JSON 格式的流日志平展到流元组级别。 平展流日志可使 Graylog 中的日志组织和搜索变得更轻松。

1.  若要安装 Logstash，请运行以下命令：

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2.  配置 Logstash，以分析流日志并将其发送到 Graylog。 创建 Logstash.conf 文件：

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3.  将以下内容添加到该文件。 更改突出显示的值，以反映存储帐户详细信息：

    ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        add_field => {
                    "time" => "%{[records][time]}"
                    "systemId" => "%{[records][systemId]}"
                    "category" => "%{[records][category]}"
                    "resourceId" => "%{[records][resourceId]}"
                    "operationName" => "%{[records][operationName}}"
                    "Version" => "%{[records][properties][Version}}"
                    "rule" => "%{[records][properties][flows][rule]}"
                    "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }
    }
        date {
            match => ["unixtimestamp" , "UNIX"]
        }
    }
    output {
        stdout { codec => rubydebug }
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
提供的 Logstash 配置文件由三个部分组成：输入、筛选器和输出。 输入部分指定 Logstash 要处理的日志的输入源 – 在本例中，我们将使用 Azure 博客输入插件（在后续步骤中安装），以便可以访问 Blob 存储中存储的网络安全组流日志 JSON 文件。

然后，filter 部分将平展每个流日志文件，以便使每个单独的流元组及其关联属性成为单独的 Logstash 事件。

最后，output 节将每个 Logstash 事件转发到 Graylog 服务器。 若要满足特定需要，可根据需要修改 Logstash 配置文件。

   > [!NOTE]
   > 前面的配置文件假定已在本地主机环回 IP 地址 127.0.0.1 上配置 Graylog 服务器。 如果没有，请确保将输出部分中的主机参数更改为正确的 IP 地址。

有关安装 Logstash 的其他说明，请参阅 Logstash [文档](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)。

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>安装适用于 Azure Blob 存储的 Logstash 输入插件

使用该 Logstash 插件可以直接从指定的 Blob 存储帐户访问流日志。 若要安装该插件，请从默认的 Logstash 安装目录（在本例中为 /usr/share/logstash/bin）运行以下命令：

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

有关该插件的详细信息，请参阅[文档](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)。

### <a name="set-up-connection-from-logstash-to-graylog"></a>设置从 Logstash 到 Graylog 的连接

使用 Logstash 建立与流日志的连接并设置 Graylog 服务器之后，需要将 Graylog 配置为接受传入的日志文件。

1.  使用针对 Graylog 服务器 Web 界面配置的 URL 导航到该界面。 可以通过将浏览器定向到 `http://<graylog-server-ip>:9000/` 来访问该界面

2.  若要导航到配置页，请在顶部导航栏的右侧选择“系统”下拉菜单，并单击“输入”。
    或者导航到 `http://<graylog-server-ip>:9000/system/inputs`

    ![入门](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3.  若要启动新输入，请在“选择输入”下拉列表中选择“GELF UDP”，并填写表单。 GELF 是“Graylog 扩展日志格式”(Graylog Extended Log Format) 的缩写。 GELF 格式由 Graylog 开发。 若要详细了解此格式的优点，请参阅 Graylog [文档](http://docs.graylog.org/en/2.2/pages/gelf.html)。

    确保将输入绑定到配置 Graylog 服务器的 IP。 IP 地址应与 Logstash 配置文件 UDP 输出中的 **host** 字段匹配。 默认端口应是 *12201*。 确保端口与 Logstash 配置文件中指定的 UDP 输出中的 **port** 字段匹配。

    ![输入](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

    启动输入后，应会看到它显示在“本地输入”部分中，如下图所示：

    ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

    若要详细了解 Graylog 消息输入，请参阅[文档](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs)。

4.  完成这些配置后，可使用以下命令启动 Logstash，开始读入流日志：

    `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>搜索整个 Graylog 消息

等待一段时间让 Graylog 服务器收集消息后，可以搜索整个消息。 若要检查正在发送到 Graylog 服务器的消息，请在“输入”配置页中，单击所创建的 GELF UDP 输入的“显示收到的消息”按钮。 随后会定向到如下图所示的屏幕： 

![直方图](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

单击蓝色的“%{Message}”链接会展开每条消息，显示每个流元组的参数，如下图所示：

![消息](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

默认情况下，如果未选择要搜索的特定消息字段，会将所有消息字段包含在搜索中。 如果想要搜索特定的消息（例如， 来自特定源 IP 的流元组），可以根据[文档](http://docs.graylog.org/en/2.2/pages/queries.html)中所述使用 Graylog 搜索查询语言


## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>使用 Graylog 分析网络安全组流日志

设置并运行 Graylog 之后，可以使用它的某些功能来更好地了解流日志数据。 操作方法之一是使用仪表板创建数据的特定视图。

### <a name="create-a-dashboard"></a>创建仪表板

1.  在顶部导航栏中，选择“仪表板”或导航到 `http://<graylog-server-ip>:9000/dashboards/`

2.  在此处，请单击绿色的“创建仪表板”按钮，并在简短表单中填写仪表板的标题和说明。 单击“保存”按钮创建新仪表板。 随后会出现如下图所示的仪表板：

    ![仪表板](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>添加小组件

可以单击仪表板的标题来查看该仪表板，但目前它是空的，因为尚未添加任何小组件。 可添加到仪表板中的简易且有用的小组件类型是“快速值”图表，其中显示所选字段的值列表及其分布。

1.  在顶部导航栏中选择“搜索”，导航回到接收流日志的 UDP 输入的搜索结果。

2.  在屏幕左侧的“搜索结果”面板下，找到“字段”选项卡，其中列出了每个传入流元组消息的各个字段。

3.  选择要可视化的任何所需参数（本示例选择了源 IP）。 若要显示可能的小组件列表，请单击字段左侧的蓝色下拉箭头，并选择“快速值”生成小组件。 应会看到下图所示的内容：

    ![Source IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4.  在此处，可以选择小组件右上角的“添加到仪表板”按钮，并选择要添加的相应仪表板。

5.  导航回到仪表板可以看到刚刚添加的小组件。

    可将其他各种小组件（例如直方图和计数）添加到仪表板，以跟踪重要指标，如下图中所示的示例仪表板：

    ![流日志仪表板](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    有关仪表板和其他类型的小组件的更多说明，请参阅 Graylog 的[文档](http://docs.graylog.org/en/2.2/pages/dashboards.html)。

通过将网络观察程序与 Graylog 集成，现在能够以方便、集中的方式管理和可视化网络安全组流日志。 Graylog 提供其他许多强大功能，例如流和警报，使用这些功能也能进一步管理流日志，以及更好地了解网络流量。 设置 Graylog 并将其连接到 Azure 之后，可以继续尽情了解它所提供的其他功能。

## <a name="next-steps"></a>后续步骤

访问[使用 Power BI 可视化网络安全组流日志](network-watcher-visualize-nsg-flow-logs-power-bi.md)，了解如何使用 Power BI 可视化网络安全组流日志。

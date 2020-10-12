---
title: 通过 Logstash 将数据源连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Logstash 将日志从外部数据源转发到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 7fe47289dcc6b6d6af4d13b36b5c3b1dae3baaf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89663750"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>使用 Logstash 将数据源连接到 Azure Sentinel

> [!IMPORTANT]
> 使用 Logstash 输出插件的数据引入当前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

对 **Logstash 数据收集引擎**使用 Azure Sentinel 的新输出插件后，现在可以通过 Logstash 将所需的任何类型的日志直接发送到 Azure Sentinel 中的 Log Analytics 工作区。 日志将发送到将使用输出插件定义的自定义表。

若要了解有关使用 Logstash 数据收集引擎的详细信息，请参阅 [Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)入门。

## <a name="overview"></a>概述

### <a name="architecture-and-background"></a>体系结构和背景

![日志存储体系结构的关系图。](./media/connect-logstash/logstash-architecture.png)

Logstash 引擎由三个组件组成：

- 输入插件：来自各种源的数据的自定义集合。
- 筛选插件：根据指定的条件对数据进行操作和规范化。
- 输出插件：将收集和处理的数据的自定义发送到不同的目标。

> [!NOTE]
> Azure Sentinel 仅支持其自己提供的输出插件。 它不支持 Azure Sentinel 的第三方输出插件或任何类型的任何其他 Logstash 插件。

使用 Log Analytics HTTP 数据收集器 REST API，适用于 Logstash 的 Azure Sentinel 输出插件向 Log Analytics 工作区发送 JSON 格式的数据。 数据将引入到自定义日志中。

- 了解 [Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics/create-request)的详细信息。
- 详细了解 [自定义日志](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs)。

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>在 Logstash 中部署 Azure Sentinel 输出插件

### <a name="step-1-installation"></a>步骤1：安装

Logstash 集合中提供了 Azure Sentinel 输出插件。

- 请按照 Logstash 使用 [插件](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) 文档中的说明安装 ***Logstash-loganalytics*** 插件。
   
- 如果你的 Logstash 系统无法访问 Internet，请按照 Logstash [脱机插件管理](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) 文档中的说明准备并使用脱机插件包。  (这将需要你构建具有 Internet 访问权限的另一个 Logstash 系统。 ) 

### <a name="step-2-configuration"></a>步骤2：配置

使用 [配置文件](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) 文档的 Logstash 结构中的信息，并将 Azure Sentinel 输出插件添加到具有以下键和值的配置中。  (在表后显示适当的配置文件语法。 ) 

| 字段名称 | 数据类型 | 说明 |
|----------------|---------------|-----------------|
| `workspace_id` | 字符串 | 输入你的工作区 ID GUID。 * |
| `workspace_key` | 字符串 | 输入工作区主键 GUID。 * |
| `custom_log_table_name` | 字符串 | 设置要将日志引入到其中的表的名称。 只能为每个输出插件配置一个表名称。 日志表将显示在 "**日志**" 下的 "日志" 下的 "**自定义日志**" 类别中的**表**中，带有 `_CL` 后缀。 |
| `endpoint` | 字符串 | 可选字段。 默认情况下，这是 Log Analytics 终结点。 使用此字段可设置备用终结点。 |
| `time_generated_field` | 字符串 | 可选字段。 此属性将覆盖 Log Analytics 中的默认 **TimeGenerated** 字段。 在数据源中输入时间戳字段的名称。 该字段中的数据必须符合 ISO 8601 格式 (`YYYY-MM-DDThh:mm:ssZ`)  |
| `key_names` | array | 输入 Log Analytics 输出架构字段的列表。 每个列表项都应括在单引号中，并以逗号分隔项，并将整个列表括在方括号内。 请参阅以下示例。 |
| `plugin_flush_interval` | 数字 | 可选字段。 设置此项可定义消息传输 Log Analytics 之间的最大时间间隔 (秒) 。 默认值为 5。 |
    | `amount_resizing` | boolean | True 或 False。 启用或禁用自动缩放机制，这会根据收到的日志数据量调整消息缓冲区大小。 |
| `max_items` | 数字 | 可选字段。 仅当 `amount_resizing` 设置为 "false" 时才适用。 用于在记录)  (中设置消息缓冲区大小的上限。 默认为 2000。  |

\* 你可以在 " **代理管理**" 下的 "工作区资源" 中找到工作区 ID 和主密钥。

#### <a name="sample-configurations"></a>示例配置

下面是一些使用几个不同选项的示例配置。

- 使用 filebeat 输入管道的基本配置：

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- 使用 tcp 输入管道的基本配置：

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- 高级配置：

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > 访问输出插件 [GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) ，了解有关其内部工作原理、配置和性能设置的详细信息。

### <a name="step-3-restart-logstash"></a>步骤3：重新启动 Logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>步骤4：在 Azure Sentinel 中查看传入日志

1. 验证是否正在将消息发送到输出插件。

1. 在 Azure Sentinel 导航菜单中，单击 " **日志**"。 在 " **表** " 标题下，展开 " **自定义日志** " 类别。 找到并单击指定的表的名称， (，并 `_CL` 在配置中) 后缀。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="日志存储自定义日志的屏幕截图。":::

1. 若要查看表中的记录，请使用表名作为架构来查询该表。

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="日志存储自定义日志的屏幕截图。":::

## <a name="monitor-output-plugin-audit-logs"></a>监视输出插件审核日志

若要监视 Azure Sentinel 输出插件的连接和活动，请启用适当的 Logstash 日志文件。 有关日志文件位置，请参阅 [Logstash 目录布局](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) 文档。

如果在此日志文件中看不到任何数据，则通过输入和筛选器插件在本地生成并发送一些事件 () 确保输出插件接收数据。 Azure Sentinel 仅支持与输出插件相关的问题。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 Logstash 将外部数据源连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用 [内置](tutorial-detect-threats-built-in.md) 或 [自定义](tutorial-detect-threats-custom.md) 规则开始使用 Azure Sentinel 检测威胁。

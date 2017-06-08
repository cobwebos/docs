---
title: "在 OMS Log Analytics 中收集自定义 JSON 数据 | Microsoft Docs"
description: "使用 OMS Agent for Linux 将自定义 JSON 数据源收集到 Log Analytics 中。  这些自定义数据源可以是返回 JSON 的简单脚本，例如 curl 或 FluentD 的 300 多个插件之一。 本文介绍此数据收集所需的配置。"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 800ee1269556e7c2d56fbbf2b497c10509b5c78c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>在 Log Analytics 中使用 OMS Agent for Linux 收集自定义 JSON 数据源
使用 OMS Agent for Linux 将自定义 JSON 数据源收集到 Log Analytics 中。  这些自定义数据源可以是返回 JSON 的简单脚本，例如 [curl](https://curl.haxx.se/) 或 [FluentD 的 300 多个插件](http://www.fluentd.org/plugins/all)之一。 本文介绍此数据收集所需的配置。

> [!NOTE]
> OMS Agent for Linux v1.1.0-217+ 是自定义 JSON 数据所必需的

## <a name="configuration"></a>配置

### <a name="configure-input-plugin"></a>配置输入插件

若要在 Log Analytics 中收集 JSON 数据，请将 `oms.api.` 添加到输入插件中 FluentD 标记的起始位置。

例如，下面是 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 中一个单独的配置文件 `exec-json.conf`。  此配置文件使用 FluentD 插件 `exec` 每隔 30 秒运行一个 curl 命令。  此命令的输出由 JSON 输出插件收集。

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 下添加的配置文件需要使用以下命令更改其所有权。

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>配置输出插件 
将下面的输出插件配置添加到 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 中的主配置，或者作为单独的配置文件放置在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 中

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-oms-agent-for-linux"></a>重新启动 OMS Agent for Linux
使用以下命令重启 OMS Agent for Linux 服务。

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>输出
将在 Log Analytics 中收集具有 `<FLUENTD_TAG>_CL` 记录类型的数据。

例如，Log Analytics 中具有 `tomcat_CL` 记录类型的自定义标记 `tag oms.api.tomcat`。  可以使用以下日志搜索检索此类型的所有记录。

    Type=tomcat_CL

支持嵌套 JSON 数据源，但基于父字段编制索引。 例如，下面的 JSON 数据从 Log Analytics 搜索返回为 `tag_s : "[{ "a":"1", "b":"2" }]`。

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>后续步骤
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。 
 

---
title: 在 Azure Monitor 中通过 CollectD 收集数据 | Microsoft Docs
description: CollectD 是一个开源 Linux 守护程序，它定期从应用程序级和系统级信息中收集数据。  本文介绍了如何在 Azure Monitor 中通过 CollectD 收集数据。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 2118f137f2c0d32f891a170c3509bceee7ba13ed
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794432"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Azure Monitor 中的 Linux 代理上通过 CollectD 收集数据
[CollectD](https://collectd.org/) 是一个开源 Linux 守护程序，它定期从应用程序级和系统级信息中收集性能指标。 示例应用程序包括 Java 虚拟机 (JVM)、MySQL Server 和 Nginx。 本文介绍了如何在 Azure Monitor 中通过 CollectD 收集性能数据。

可以在[插件表](https://collectd.org/wiki/index.php/Table_of_Plugins)中找到可用插件的完整列表。

![CollectD 概述](media/data-sources-collectd/overview.png)

Log Analytics Linux 代理中包括了以下 CollectD 配置，用以将 CollectD 数据路由到 Log Analytics Linux 代理。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

此外，如果使用的是 5.5 之前的某个 collectD 版本，请改用以下配置。

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

CollectD 配置使用默认的 `write_http` 插件通过端口 26000 将性能指标数据发送到 Log Analytics Linux 代理。 

> [!NOTE]
> 如果需要，可以将此端口配置为一个自定义的端口。

适用于 Linux 的 Log Analytics 代理也在端口 26000 上侦听 CollectD 指标，然后将其转换为 Azure Monitor 架构指标。 下面是 Log Analytics Linux 代理配置 `collectd.conf`。

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>支持的版本
- Azure Monitor 当前支持 CollectD 4.8 版及更高版本。
- 要收集 CollectD 指标，需要 Log Analytics Linux 代理 v1.1.0-217 或更高版本。


## <a name="configuration"></a>配置
下面是在 Azure Monitor 中配置 CollectD 数据收集的基本步骤。

1. 将 CollectD 配置为使用 write_http 插件将数据发送到 Log Analytics Linux 代理。  
2. 将 Log Analytics Linux 代理配置为在相应的端口上侦听 CollectD 数据。
3. 重新启动 CollectD 和 Log Analytics Linux 代理。

### <a name="configure-collectd-to-forward-data"></a>配置 CollectD 来转发数据 

1. 要将 CollectD 数据路由到 Log Analytics Linux 代理，需要将 `oms.conf` 添加到 CollectD 的配置目录中。 此文件的目的地取决于计算机的 Linux 发行版。

    如果 CollectD 配置目录位于 /etc/collectd.d/ 中：

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    如果 CollectD 配置目录位于 /etc/collectd/collectd.conf.d/ 中：

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >对于 5.5 之前的 CollectD 版本，必须如上所示修改 `oms.conf` 中的标记。
    >

2. 将 collectd.conf 复制到所需工作区的 omsagent 配置目录中。

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. 使用以下命令重新启动 CollectD 和 Log Analytics Linux 代理。

    sudo service collectd restart  sudo /opt/microsoft/omsagent/bin/service_control restart

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>CollectD 指标到 Azure Monitor 架构的转换
为了在 Log Analytics Linux 代理已收集的基础结构指标与 CollectD 收集的新指标之间维护一个熟悉的模型，将使用以下架构映射：

| CollectD 指标字段 | Azure Monitor 字段 |
|:--|:--|
| `host` | Computer |
| `plugin` | 无 |
| `plugin_instance` | Instance Name<br>如果 **plugin_instance** 为 null，则 InstanceName="_Total" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>如果 **type_instance** 为 null，则 CounterName=**空白** |
| `dsnames[]` | CounterName |
| `dstypes` | 无 |
| `values[]` | CounterValue |

## <a name="next-steps"></a>后续步骤
* 了解[日志查询](../log-query/log-query-overview.md)以便分析从数据源和解决方案中收集的数据。 
* 使用[自定义字段](custom-fields.md)将来自 syslog 记录的数据解析为单个字段。

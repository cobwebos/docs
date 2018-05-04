---
title: 在 OMS Log Analytics 中收集 Nagios 和 Zabbix 警报 | Microsoft Docs
description: Nagios 和 Zabbix 是开源监视工具。 可以将来自这些工具的警报收集到 Log Analytics 中，以便将其与来自其他来源的警报一起进行分析。  本文介绍了如何配置用于 Linux 的 OMS 代理，以便收集来自这些系统的警报。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/13/2018
ms.author: magoedte
ms.openlocfilehash: 04c56b7b7726d9ca603f2ff38acfabc887ecaf34
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-log-analytics-from-oms-agent-for-linux"></a>通过用于 Linux 的 OMS 代理将来自 Nagios 和 Zabbix 的警报收集到 Log Analytics 中 
[Nagios](https://www.nagios.org/) 和 [Zabbix](http://www.zabbix.com/) 是开源监视工具。 可以将来自这些工具的警报收集到 Log Analytics 中，以便将其与[来自其他来源的警报](log-analytics-alerts.md)一起进行分析。  本文介绍了如何配置用于 Linux 的 OMS 代理，以便收集来自这些系统的警报。
 
## <a name="prerequisites"></a>先决条件
适用于 Linux 的 OMS 代理支持从 Nagios（最高版本 4.2.x）和 Zabbix（最高版本 2.x）收集警报。

## <a name="configure-alert-collection"></a>配置警报收集

### <a name="configuring-nagios-alert-collection"></a>配置 Nagios 警报收集
若要收集警报，请在 Nagios 服务器上执行以下步骤。

1. 向用户 **omsagent** 授予对 Nagios 日志文件 `/var/log/nagios/nagios.log` 的读取访问权限。 假定 nagios.log 文件归组 `nagios` 所有，可以将用户 **omsagent** 添加到 **nagios** 组。 

    sudo usermod -a -G nagios omsagent

2.  修改位于 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 处的配置文件。 确保以下条目存在且未注释掉︰  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. 重新启动 omsagent 守护程序

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>配置 Zabbix 警报收集
若要收集来自 Zabbix 服务器的警报，需要指定一个用户和明文密码。  虽然不理想，但建议你创建用户并仅向其授予用于监视的权限。

若要在 Nagios 服务器上收集警报，请执行以下步骤。

1. 修改位于 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 处的配置文件。 确保以下条目存在且未注释掉。将用户名和密码更改为适合 Zabbix 环境的值。

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. 重新启动 omsagent 守护程序

    sudo sh /opt/microsoft/omsagent/bin/service_control restart


## <a name="alert-records"></a>警报记录
可以使用 Log Analytics 中的[日志搜索](log-analytics-log-searches.md)来检索来自 Nagios 和 Zabbix 的警报记录。

### <a name="nagios-alert-records"></a>Nagios 警报记录

Nagios 收集的警报记录的 **Type** 为 **Alert**，且 **SourceSystem** 为 **Nagios**。  它们具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*Nagios* |
| AlertName |警报的名称。 |
| AlertDescription | 警报的说明。 |
| AlertState | 服务或主机的状态。<br><br>OK<br>WARNING<br>UP<br>DOWN |
| HostName | 创建了警报的主机的名称。 |
| PriorityNumber | 警报的优先级。 |
| StateType | 警报状态的类型。<br><br>SOFT - 尚未重新检查的问题。<br>HARD - 已重新检查了指定次数的问题。  |
| TimeGenerated |警报的创建日期和时间。 |


### <a name="zabbix-alert-records"></a>Zabbix 警报记录
Zabbix 收集的警报记录的 **Type** 为 **Alert**，且 **SourceSystem** 为 **Zabbix**。  它们具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| Type |*Alert* |
| SourceSystem |*Zabbix* |
| AlertName | 警报的名称。 |
| AlertPriority | 警报的严重性。<br><br>未分类<br>信息<br>警告<br>平均值<br>高<br>灾难  |
| AlertState | 警报的状态。<br><br>0 - 状态为最新。<br>1 - 状态为未知。  |
| AlertTypeNumber | 指定警报是否可以生成多个问题事件。<br><br>0 - 状态为最新。<br>1 - 状态为未知。    |
| 注释 | 警报的附加备注。 |
| HostName | 创建了警报的主机的名称。 |
| PriorityNumber | 指示警报严重性的值。<br><br>0 - 未分类<br>1 - 信息<br>2 - 警告<br>3 - 平均值<br>4 - 高<br>5 - 灾难 |
| TimeGenerated |警报的创建日期和时间。 |
| TimeLastModified |上次更改警报状态的日期和时间。 |


## <a name="next-steps"></a>后续步骤
* 在 Log Analytics 中了解[警报](log-analytics-alerts.md)。
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。 

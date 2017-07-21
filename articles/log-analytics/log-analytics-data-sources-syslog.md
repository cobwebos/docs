---
title: "收集和分析 OMS Log Analytics 中的 Syslog 消息 | Microsoft Docs"
description: "Syslog 是普遍适用于 Linux 的事件日志记录协议。 本文介绍如何在 Log Analytics 中配置 Syslog 消息集合以及它们在 OMS 存储库中创建的记录的详细信息。"
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
ms.date: 06/12/2017
ms.author: magoedte;bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 783b9b48251c5f092121288af8834e2caf31f5d7
ms.contentlocale: zh-cn
ms.lasthandoff: 06/13/2017


---
# <a name="syslog-data-sources-in-log-analytics"></a>Log Analytics 中的 Syslog 数据源
Syslog 是普遍适用于 Linux 的事件日志记录协议。  应用程序将发送可能存储在本地计算机或传递到 Syslog 收集器的消息。  安装适用于 Linux 的 OMS 代理后，它将配置本地 Syslog 后台程序，以将消息转发到此代理。  然后，此代理将消息发送到 Log Analytics，其中相应的记录将在 OMS 存储库中创建。  

> [!NOTE]
> 当 rsyslog 为默认守护程序时，Log Analytics 支持 rsyslog 或 syslog-ng 发送的消息集合。 不支持将 Red Hat Enterprise Linux 版本 5、CentOS 和 Oracle Linux 版本 (sysklog) 上的默认 syslog 守护程序用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 [rsyslog 守护程序](http://rsyslog.com)以替换 sysklog。
> 
> 

![Syslog 收集](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>配置 Syslog
针对 Linux 的 OMS 代理将仅收集在其配置中指定设施和严重级别的事件。  通过 OMS 门户或通过管理 Linux 代理的配置文件来配置 Syslog。

### <a name="configure-syslog-in-the-oms-portal"></a>配置 OMS 门户中的 Syslog
可以从 [Log Analytics 的“设置”中的“数据”](log-analytics-data-sources.md#configuring-data-sources)菜单配置 Syslog。  此配置将传递到每个 Linux 代理上的配置文件。

通过键入设施名称并单击 **+** 可添加新设施。  对于每个设施，将仅收集具有所选严重级别的消息。  检查要收集的特定设施的严重级别。  不能向筛选消息提供任何其他条件。

![配置 Syslog](media/log-analytics-data-sources-syslog/configure.png)

默认情况下，所有配置更改均会自动推送到所有代理。  如果想在每个 Linux 代理上手动配置Syslog，则取消选中“*将下面的配置应用到我的 Linux 计算机*”框即可。

### <a name="configure-syslog-on-linux-agent"></a>在 Linux 代理上配置 Syslog
[OMS 代理安装在 Linux 客户端上](log-analytics-linux-agents.md)后，它将安装可定义收集的消息的设施和严重级别的默认 syslog 配置文件。  你可以修改此文件以更改配置。  此配置文件视客户端已安装的 Syslog 守护程序而异。

> [!NOTE]
> 如果编辑 syslog 配置，必须重新启动 syslog 守护程序才能使更改生效。
> 
> 

#### <a name="rsyslog"></a>rsyslog
rsyslog 的配置文件位于 **/etc/rsyslog.d/95-omsagent.conf**。  其默认内容如下所示。  这将收集针对警告或更高级别的全部设施从本地代理发送的 syslog 消息。

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

通过删除设施的配置文件部分可删除某个设施。  通过修改设施条目，可以针对某个特定设施限制收集的严重级别。  例如，要将用户设施限制为错误或更高严重级别的消息，则需要将配置文件的相应行修改为如下所示：

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
syslog-ng 的配置文件位于 **/etc/syslog-ng/syslog-ng.conf**。  其默认内容如下所示。  这将收集针对全部设施和所有严重级别的从本地代理发送的 syslog 消息。   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

通过删除设施的配置文件部分可删除某个设施。  通过将严重级别从其列表中删除，可以针对某个特定设施限制收集的严重级别。  例如，要将用户设施限制为仅警告和关键消息，则需要将配置文件的相应部分修改为如下所示：

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>从其他 Syslog 端口收集数据
OMS 代理在端口 25224 侦听本地客户端上的 Syslog 消息。  安装代理时，会应用默认的 syslog 配置，此配置位于以下位置： 

* Rsyslog：`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng：`/etc/syslog-ng/syslog-ng.conf`

可通过创建两个配置文件来更改端口号：FluentD 配置文件和 rsyslog-or-syslog-ng（取决于已安装的 Syslog 守护程序）。  

* FluentD 配置文件应为新文件（位于 `/etc/opt/microsoft/omsagent/conf/omsagent.d`），同时用自定义端口号替换“端口”条目中的值。

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* 对于 rsyslog，应创建新的配置文件（位于 `/etc/rsyslog.d/`），同时用自定义端口号替换值 %SYSLOG_PORT%。  

    > [!NOTE]
    > 如果修改了配置文件 `95-omsagent.conf` 中的此值，代理应用默认配置时将覆盖此值。
    > 

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* 若要修改 syslog-ng 配置，应复制下面显示的示例配置，然后将自定义修改设置添加到 syslog-ng.conf 配置文件（位于 `/etc/syslog-ng/`）的末尾。  不要使用默认标签 %WORKSPACE_ID%_oms 或 %WORKSPACE_ID_OMS，请定义自定义标签，以帮助区分你的更改。  

    > [!NOTE]
    > 如果修改了配置文件中的默认值，代理应用默认配置时将覆盖这些值。
    > 

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

完成更改后，需重启 Syslog 和 OMS 代理服务，确保配置更改生效。   

## <a name="syslog-record-properties"></a>Syslog 记录属性
record 记录的类型为 **Syslog**，并且具有下表中的属性。

| 属性 | 说明 |
|:--- |:--- |
| 计算机 |从中收集事件的计算机。 |
| 设施 |定义生成消息的系统部分。 |
| HostIP |发送消息的系统的 IP 地址。 |
| HostName |发送消息的系统的名称。 |
| SeverityLevel |事件的严重级别。 |
| SyslogMessage |消息的文本。 |
| ProcessID |生成消息的进程 ID。 |
| EventTime |生成事件的日期和时间。 |

## <a name="log-queries-with-syslog-records"></a>具有 Syslog 记录的日志查询
下表提供了检索 Syslog 记录的不同日志查询示例。

| 查询 | 说明 |
|:--- |:--- |
| Type=Syslog |所有 Syslog。 |
| Type=Syslog SeverityLevel=error |具有错误严重级别的所有 Syslog 记录。 |
| Type=Syslog &#124; measure count() by Computer |按计算机计算的 Syslog 记录数目。 |
| Type=Syslog &#124; measure count() by Facility |按设施计算的 Syslog 记录数目。 |

## <a name="next-steps"></a>后续步骤
* 了解[日志搜索](log-analytics-log-searches.md)以便分析从数据源和解决方案中收集的数据。 
* 使用[自定义字段](log-analytics-custom-fields.md)将来自 syslog 记录的数据解析为单个字段。
* [配置 Linux 代理](log-analytics-linux-agents.md)以收集其他类型的数据。 



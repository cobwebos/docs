---
title: 验证与 Azure 哨兵的连接 |微软文档
description: 验证安全解决方案的连接性，以确保 CEF 消息被转发到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731816"
---
# <a name="step-3-validate-connectivity"></a>第 3 步：验证连接

部署日志转发器（在步骤 1 中）并配置安全解决方案以向其发送 CEF 消息（在步骤 2 中），请按照这些说明验证安全解决方案和 Azure Sentinel 之间的连接。 

## <a name="prerequisites"></a>先决条件

- 日志转发器计算机上必须具有提升的权限 （sudo）。

- 必须在日志转发器计算机上安装了 Python。<br>
使用`python –version`命令进行检查。

## <a name="how-to-validate-connectivity"></a>如何验证连接

1. 从 Azure Sentinel 导航菜单中，打开**日志**。 使用**通用安全日志**架构运行查询，以查看是否从安全解决方案接收日志。<br>
请注意，可能需要大约 20 分钟，直到日志开始出现在**日志分析**中。 

1. 如果看不到查询的任何结果，请验证事件是否从安全解决方案生成，或者尝试生成一些事件，并验证它们正转发到您指定的 Syslog 转发器计算机。 

1. 在日志转发器上运行以下脚本，以检查安全解决方案、日志转发器和 Azure Sentinel 之间的连接。 此脚本检查守护进程是否侦听正确的端口，转发是否配置正确，并且没有任何内容阻止守护进程和日志分析代理之间的通信。 它还发送模拟消息"TestCommonEventFormat"以检查端到端连接。 <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>验证脚本解释

验证脚本执行以下检查：

# <a name="rsyslog-daemon"></a>[rsyslog 守护进程](#tab/rsyslog)

1. 检查文件<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 检查该文件中的文件中包含以下文本：

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. 检查计算机上是否有任何可能阻止网络流量（如主机防火墙）的安全增强功能。

1. 检查 syslog 守护进程 （rsyslog） 是否正确配置为向 TCP 端口 25226 上的日志分析代理发送其标识为 CEF（使用正则表达式）的消息：

    - 配置文件：`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. 检查 syslog 守护进程是否接收端口 514 上的数据

1. 检查是否建立了必要的连接：用于接收数据的 tcp 514，用于 syslog 守护进程和日志分析代理之间的内部通信的 tcp 25226

1. 将 MOCK 数据发送到本地主机上的端口 514。 此数据应在 Azure Sentinel 工作区中通过运行以下查询来观察：

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng 守护进程](#tab/syslogng)

1. 检查文件<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    存在且有效。

1. 检查该文件中的文件中包含以下文本：

        <source>
            type syslog
            port 25226
            bind 127.0.0.1
            protocol_type tcp
            tag oms.security
            format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
            <parse>
                message_format auto
            </parse>
        </source>

        <filter oms.security.**>
            type filter_syslog_security
        </filter>

1. 检查计算机上是否有任何可能阻止网络流量（如主机防火墙）的安全增强功能。

1. 检查 syslog 守护进程 （syslog-ng） 是否正确配置为向 TCP 端口 25226 上的日志分析代理发送其标识为 CEF（使用正则表达式）的消息：

    - 配置文件：`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. 检查 syslog 守护进程是否接收端口 514 上的数据

1. 检查是否建立了必要的连接：用于接收数据的 tcp 514，用于 syslog 守护进程和日志分析代理之间的内部通信的 tcp 25226

1. 将 MOCK 数据发送到本地主机上的端口 514。 此数据应在 Azure Sentinel 工作区中通过运行以下查询来观察：

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。


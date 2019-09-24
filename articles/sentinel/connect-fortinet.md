---
title: 将 Fortinet 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Fortinet 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 20079fd0c95da3e3aec9518f194ea39561a5e662
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240704"
---
# <a name="connect-your-fortinet-appliance"></a>连接 Fortinet 设备



可以通过将日志文件另存为 Syslog 通用事件格式（CEF）来将 Azure Sentinel 连接到任何 Fortinet 设备。 通过与 Azure Sentinel 集成，你可以从 Fortinet 轻松地跨日志文件数据运行分析和查询。 有关 Azure Sentinel 引入 CEF 数据的详细信息，请参阅[连接 CEF 设备](connect-common-event-format.md)。

> [!NOTE]
> 数据存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>步骤 1：使用代理连接 Fortinet 设备

若要将 Fortinet 设备连接到 Azure Sentinel，请在专用 VM 或本地计算机上部署代理，以支持设备与 Azure Sentinel 之间的通信。 

你还可以在现有的 Azure VM、其他云中的 VM 或本地计算机上手动部署代理。

> [!NOTE]
> 请确保根据组织的安全策略配置计算机的安全性。 例如，你可以将网络配置为与你的企业网络安全策略一致，并更改守护程序中的端口和协议以符合你的要求。 

若要查看这两个选项的网络图，请参阅[连接数据源](connect-data-sources.md#agent-options)。

### <a name="deploy-the-agent"></a>部署代理

1. 在 Azure Sentinel 门户中，单击 "**数据连接器**"，然后选择 " **Fortinet** "，然后单击 "**连接器" 页**。 

1. 在 "**下载并安装 Syslog 代理**" 下，选择你的计算机类型（Azure 或本地）。 
1. 在打开的 "**虚拟机**" 屏幕中，选择要使用的计算机，然后单击 "**连接**"。
1. 如果**为 Azure Linux 虚拟机选择 "下载并安装代理**"，请选择该计算机，然后单击 "**连接**"。 如果为**非 Azure Linux 虚拟机选择了 "下载并安装代理**"，请在 "**直接代理**" 屏幕中，运行**下载和板载 agent for Linux**中的脚本。
1. 在连接器屏幕上的 "**配置和转发 syslog**" 下，设置 syslog 守护程序是**rsyslog**还是**syslog-ng**。 
1. 复制以下命令并在设备上运行它们：
   - 如果选择了 rsyslog：
            
     1. 告诉 Syslog 守护程序侦听设备 local_4，并使用端口25226将 Syslog 消息发送到 Azure Sentinel 代理。 使用此命令：`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. 下载并安装[security_events 配置文件](https://aka.ms/asi-syslog-config-file-linux)，该文件将 Syslog 代理配置为在端口25226上侦听。 使用此命令： `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`其中{0}应替换为工作区 GUID。
     1. 使用以下命令重新启动 syslog 守护程序：`sudo service rsyslog restart`
            
   - 如果选择了 syslog-ng：

      1. 告诉 Syslog 守护程序侦听设备 local_4，并使用端口25226将 Syslog 消息发送到 Azure Sentinel 代理。 使用此命令：`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. 下载并安装[security_events 配置文件](https://aka.ms/asi-syslog-config-file-linux)，该文件将 Syslog 代理配置为在端口25226上侦听。 使用此命令： `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`其中{0}应替换为工作区 GUID。
      1. 使用以下命令重新启动 syslog 守护程序：`sudo service syslog-ng restart`
1. 使用以下命令重新启动 Syslog 代理：`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. 通过运行以下命令确认代理日志中没有任何错误：`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>步骤 2：将 Fortinet 日志转发到 Syslog 代理

配置 Fortinet 以通过 Syslog 代理将 CEF 格式的 Syslog 消息转发到 Azure 工作区。

1. 在 Fortinet 设备上打开 CLI，并运行以下命令：

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - 将服务器**ip 地址**替换为代理的 ip 地址。
    - 将**facility_name**设置为使用你在代理中配置的设备。 默认情况下，代理将此设置为 local4。
    - 将**syslog 端口**设置为**514**或在代理上设置的端口。
    - 若要在早期 FortiOS 版本中启用 CEF 格式，你可能需要运行命令 set **csv disable**。
 
   > [!NOTE] 
   > 有关详细信息，请参阅[Fortinet 文档库](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary)。 选择版本，并使用**手册**和**日志消息参考**。

 若要使用 Fortinet 事件的 Azure Monitor Log Analytics 中的相关架构，请搜索`CommonSecurityLog`。


## <a name="step-3-validate-connectivity"></a>步骤 3：验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。 

1. 请确保使用正确的工具。 设备在设备和 Azure Sentinel 中必须相同。 可以在 Azure Sentinel 中检查要使用的工具文件，并在文件`security-config-omsagent.conf`中对其进行修改。 

2. 确保日志在 Syslog 代理中获得正确的端口。 在 Syslog 代理计算机上运行以下命令： `tcpdump -A -ni any  port 514 -vv`。 此命令显示从设备传输到 Syslog 计算机的日志。 请确保在正确的端口和适当的设备上从源设备接收日志。

3. 请确保发送的日志符合[RFC 3164](https://tools.ietf.org/html/rfc3164)。

4. 在运行 Syslog 代理的计算机上，确保已打开端口514和25226，并使用命令`netstat -a -n:`进行侦听。 有关使用此命令的详细信息，请参阅[netstat （8）-Linux 手册页](https://linux.die.net/man/8/netstat)。 如果侦听正确，将看到：

   ![Azure Sentinel 端口](./media/connect-cef/ports.png) 

5. 确保在要向其发送日志的端口514上设置守护程序侦听。
    - 对于 rsyslog：<br>确保该文件`/etc/rsyslog.conf`包括以下配置：

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      有关详细信息，请[参阅 imudp：UDP Syslog 输入模块](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module)和[imtcp：TCP Syslog 输入模块](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)。

   - 对于 syslog-ng：<br>确保该文件`/etc/syslog-ng/syslog-ng.conf`包括以下配置：

           # source s_network {
            network( transport(UDP) port(514));
             };
     有关详细信息，请[参阅 imudp：UDP Syslog 输入模块](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html)和[syslog-ng 开源 edition 3.16-管理指南](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455)。

1. 检查 Syslog 守护程序和代理之间是否存在通信。 在 Syslog 代理计算机上运行以下命令： `tcpdump -A -ni any  port 25226 -vv`。 此命令显示从设备传输到 Syslog 计算机的日志。 确保还会在代理上接收日志。

6. 如果这两个命令均提供了成功的结果，请查看 Log Analytics 查看日志是否到达。 从这些装置流式传输的所有事件都以原始形式出现`CommonSecurityLog`在 "类型" 下的 "Log Analytics 中。

7. 若要检查是否存在错误，或日志是否未到达，请查看`tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`。 如果显示日志格式不匹配错误，请参阅`/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`并查看文件。 `security_events.conf` 确保日志与您在此文件中看到的 regex 格式匹配。

8. 请确保 Syslog 消息默认大小限制为2048字节（2 KB）。 如果日志太长，请使用以下命令更新 security_events：`message_length_limit 4096`

10. 如果代理未接收到 Fortinet 日志，请运行此命令，具体取决于所使用的 Syslog 守护程序的类型，设置该设备，并将日志设置为搜索日志中的单词 Fortinet：
       - rsyslog：`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     使用以下命令重新启动 Syslog 守护程序：`sudo service rsyslog restart`
       - syslog-ng：`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     使用以下命令重新启动 Syslog 守护程序：`sudo service syslog-ng restart`

## <a name="next-steps"></a>后续步骤
本文介绍了如何将 Fortinet 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。


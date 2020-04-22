---
title: 部署日志转发器将 CEF 数据连接到 Azure 哨兵 |微软文档
description: 了解如何部署代理以将 CEF 数据连接到 Azure Sentinel。
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
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731648"
---
# <a name="step-1-deploy-the-log-forwarder"></a>第 1 步：部署日志转发器


在此步骤中，您将指定和配置 Linux 计算机，它将日志从安全解决方案转发到 Azure Sentinel 工作区。 此计算机可以是本地环境中的物理计算机或虚拟机、Azure VM 或其他云中的 VM。 使用提供的链接，您将在指定的计算机上运行执行以下任务的脚本：
- 安装 Linux 的日志分析代理（也称为 OMS 代理），并将其配置为以下目的：
    - 从 TCP 端口 25226 上的内置 Linux Syslog 守护进程侦听 CEF 消息
    - 通过 TLS 安全地将邮件发送到 Azure Sentinel 工作区，在那里对其进行解析和丰富

- 为以下目的配置内置 Linux Syslog 守护进程 （rsyslog.d/syslog-ng）：
    - 在 TCP 端口 514 上从安全解决方案中侦听 Syslog 消息
    - 使用 TCP 端口 25226 将标识为 CEF 的邮件仅转发到本地主机上的日志分析代理
 
## <a name="prerequisites"></a>先决条件

- 您必须在指定的 Linux 计算机上具有提升的权限 （sudo）。
- 必须在 Linux 计算机上安装 python。<br>使用`python -version`命令进行检查。
- 在安装日志分析代理之前，不能将 Linux 计算机连接到任何 Azure 工作区。

## <a name="run-the-deployment-script"></a>运行部署脚本
 
1. 在 Azure Sentinel 导航菜单中，单击 **"数据连接器**"。 从连接器列表中，单击 **"常见事件格式 （CEF）"** 磁贴，然后单击右下角的 **"打开连接器"页面**按钮。 

1. 在**1.2 下，在 Linux 计算机上安装 CEF 收集器**，复制**运行以下脚本下提供的链接以安装和应用 CEF 收集器**，或从以下文本中复制：

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. 脚本运行时，请检查以确保不会收到任何错误或警告消息。

继续[执行步骤 2：配置安全解决方案以转发 CEF 消息](connect-cef-solution-config.md)。

## <a name="deployment-script-explained"></a>已解释的部署脚本

以下是部署脚本操作的逐命令说明。

选择系统守护进程以查看相应的说明。

# <a name="rsyslog-daemon"></a>[rsyslog 守护进程](#tab/rsyslog)

1. **下载并安装日志分析代理：**

    - 下载日志分析 （OMS） Linux 代理的安装脚本<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 安装日志分析代理<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **配置 Syslog 守护进程：**

    1. 使用 syslog 配置文件`/etc/rsyslog.conf`打开端口 514 以进行 TCP 通信。

    1. 通过将特殊配置文件`security-config-omsagent.conf`插入到 syslog 守护进程目录`/etc/rsyslog.d/`中，将守护进程配置为将 CEF 消息转发到 TCP 端口 25226 上的日志分析代理。

        `security-config-omsagent.conf`文件的内容：

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **重新启动 Syslog 守护进程**

    `service rsyslog restart`

1. **将日志分析代理配置设置为侦听端口 25226 并将 CEF 消息转发到 Azure Sentinel**

    1. 从日志分析代理 GitHub 存储库下载配置<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 重新启动日志分析代理<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng 守护进程](#tab/syslogng)

1. **下载并安装日志分析代理：**

    - 下载日志分析 （OMS） Linux 代理的安装脚本<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - 安装日志分析代理<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **配置 Syslog 守护进程：**

    1. 使用 syslog 配置文件`/etc/syslog-ng/syslog-ng.conf`打开端口 514 以进行 TCP 通信。

    1. 通过将特殊配置文件`security-config-omsagent.conf`插入到 syslog 守护进程目录`/etc/syslog-ng/conf.d/`中，将守护进程配置为将 CEF 消息转发到 TCP 端口 25226 上的日志分析代理。

        `security-config-omsagent.conf`文件的内容：

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **重新启动 Syslog 守护进程**

    `service syslog-ng restart`

1. **将日志分析代理配置设置为侦听端口 25226 并将 CEF 消息转发到 Azure Sentinel**

    1. 从日志分析代理 GitHub 存储库下载配置<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. 重新启动日志分析代理<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何部署日志分析代理，将 CEF 设备连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。


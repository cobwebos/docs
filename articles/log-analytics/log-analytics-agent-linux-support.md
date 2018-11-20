---
title: Azure Log Analytics Linux 代理故障排除 | Microsoft Docs
description: 描述 Log Analytics Linux 代理最常见问题的症状、原因和解决方法。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 55a3f3d7ac08554273464ed7c6f665e27661c6f8
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625515"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>如何排查 Log Analytics Linux 代理的问题 

本文介绍如何解决可能遇到的 Log Analytics Linux 代理的相关错误，并提供可能的解决方案建议。

如果这些步骤对你均无效，我们还提供了以下支持渠道：

* 具有顶级支持权益的客户可以通过[顶级](https://premier.microsoft.com/)提出支持请求。
* 具有 Azure 支持协议的客户可以在 [Azure 门户](https://manage.windowsazure.com/?getsupport=true)中提出支持请求。
* 借助 [OMI 故障排除指南](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md)诊断 OMI 问题。
* 提出 [GitHub 问题](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)。
* 请访问 Log Analytics 反馈页面，查看已提交的想法和 bug [http://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback)或提出新的想法。  

## <a name="important-log-locations-and-log-collector-tool"></a>重要的日志位置和日志收集器工具

 文件 | 路径
 ---- | -----
 Log Analytics Linux 代理日志文件 | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log `
 Log Analytics 代理配置日志文件 | `/var/opt/microsoft/omsconfig/omsconfig.log`

 我们建议你使用我们的日志收集器工具来检索重要日志进行故障排除或在提交 GitHub 问题之前使用它们来检索重要日志。 你可以点击[此处](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)了解有关该工具的详细信息以及如何运行该工具。

## <a name="important-configuration-files"></a>重要的配置文件

 类别 | 文件位置
 ----- | -----
 Syslog | `/etc/syslog-ng/syslog-ng.conf` 或 `/etc/rsyslog.conf` 或 `/etc/rsyslog.d/95-omsagent.conf`
 性能、Nagios、Zabbix、Log Analytics 输出和常规代理 | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 其他配置 | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >如果从 Azure 门户中针对你的工作区的[数据菜单 Log Analytics 高级设置](log-analytics-data-sources.md#configuring-data-sources)中配置收集，则性能计数器的编辑配置文件和 Syslog 将会被覆盖。 要禁用所有代理的配置，则禁用从 Log Analytics“高级设置”收集，若禁用单个代理，则运行以下命令：  
> `sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable`

## <a name="installation-error-codes"></a>安装错误代码

| 错误代码 | 含义 |
| --- | --- |
| NOT_DEFINED | 由于未安装必需的依赖项，将不会安装 auoms auditd 插件 | Auoms 安装失败，请安装程序包 auditd。 |
| 2 | 提供给 shell 捆绑包的选项无效。 运行 `sudo sh ./omsagent-*.universal*.sh --help` 获取使用情况 |
| 3 | 未向 shell 捆绑包提供任何选项。 运行 `sudo sh ./omsagent-*.universal*.sh --help` 获取使用情况。 |
| 4 | 无效的程序包类型或者无效的代理服务器设置；omsagent-*rpm*.sh 程序包只能安装在基于 RPM 的系统上，而 msagent-*deb*.sh 程序包只能安装在基于 Debian 的系统上。 建议从[最新版本](log-analytics-quick-collect-linux-computer.md#install-the-agent-for-linux)使用通用安装程序。 也进行[查看](#issue:-unable-to-connect-through-proxy-to-log-analytics)以验证你的代理服务器设置。 |
| 5 | 必须以 root 身份执行 shell 捆绑包或在载入期间返回 403 错误。 使用 `sudo` 运行你的命令。 |
| 6 | 无效的程序包体系结构或者载入期间返回 200 错误；omsagent-*x64.sh 程序包只能安装在 64 位系统上，而 omsagent-* x86.sh 程序包只能安装在 32 位系统上。 从[最新版本](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest)为你的体系结构下载正确的程序包。 |
| 17 | OMS 程序包安装失败。 仔细查看命令输出查找根源故障。 |
| 19 | OMI 程序包安装失败。 仔细查看命令输出查找根源故障。 |
| 20 | SCX 程序包安装失败。 仔细查看命令输出查找根源故障。 |
| 21 | Provider 工具包安装失败。 仔细查看命令输出查找根源故障。 |
| 22 | 捆绑的程序包安装失败。 仔细查看命令输出查找根源故障 |
| 23 | SCX 或 OMI 程序包已安装。 使用 `--upgrade` 而不是 `--install` 安装 shell 捆绑包。 |
| 30 | 内部捆绑包错误。 提出 [GitHub 问题](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)，附带输出中的详细信息。 |
| 55 | 不受支持的 openssl 版本或无法连接到 Log Analytics 服务或 dpkg 已锁定或缺少 curl 程序。 |
| 61 | 缺少 Python ctypes 库。 安装 Python ctypes 库或程序包 (python-ctypes)。 |
| 62 | 缺少 tar 程序，请安装 tar。 |
| 63 | 缺少 sed 程序，请安装 sed。 |
| 64 | 缺少 curl 程序，请安装 curl。 |
| 65 | 缺少 gpg 程序，请安装 gpg。 |

## <a name="onboarding-error-codes"></a>载入错误代码

| 错误代码 | 含义 |
| --- | --- |
| 2 | 提供给 omsadmin 脚本的选项无效。 运行 `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` 获取使用情况。 |
| 3 | 提供给 omsadmin 脚本的配置无效。 运行 `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` 获取使用情况。 |
| 4 | 提供给 omsadmin 脚本的代理无效。 验证代理，并参阅我们的[有关使用 HTTP 代理服务器的文档](log-analytics-agent-overview.md#network-firewall-requirements)。 |
| 5 | 从 Log Analytics 服务收到 403 HTTP 错误。 请参阅完整的 omsadmin 脚本输出了解详细信息。 |
| 6 | 未从 Log Analytics 服务收到 200 HTTP 错误。 请参阅完整的 omsadmin 脚本输出了解详细信息。 |
| 7 | 无法连接到 Log Analytics 服务。 请参阅完整的 omsadmin 脚本输出了解详细信息。 |
| 8 | 载入 Log Analytics 工作区时出错。 请参阅完整的 omsadmin 脚本输出了解详细信息。 |
| 30 | 内部脚本错误。 提出 [GitHub 问题](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)，附带输出中的详细信息。 |
| 31 | 生成代理 ID 时出错。 提出 [GitHub 问题](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)，附带输出中的详细信息。 |
| 32 | 生成证书时出错。 请参阅完整的 omsadmin 脚本输出了解详细信息。 |
| 33 | 生成 omsconfig 的元配置时出错。 提出 [GitHub 问题](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)，附带输出中的详细信息。 |
| 34 | 不存在元配置生成脚本。 重新尝试使用 `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>` 载入。 |

## <a name="enable-debug-logging"></a>启用调试日志记录
### <a name="oms-output-plugin-debug"></a>OMS 输出插件调试
 FluentD 允许插件特定日志记录级别，从而允许针对输入和输出指定不同的日志级别。 要为 OMS 输出指定不同的日志级别，请在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 编辑常规代理配置。  

 在 OMS 输出插件的配置文件末尾之前，将 `log_level` 属性从 `info` 更改为 `debug`：

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

调试日志记录允许按类型、数据项数量和发送所用时间查看批量上传至 Log Analytics 服务的信息：

*启用调试日志的示例︰*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>详细输出
如果不使用 OMS 输出插件，还可以将数据项直接输出到 Log Analytics for Linux 代理日志文件中可见的 `stdout`。

在 Log Analytics 常规代理配置文件中的 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 处，通过在每一行的前面添加 `#` 注释掉 OMS 输出插件：

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

在输出插件下面，通过在每一行的前面删除 `#` 注释掉以下部分：

```
<match **>
  type stdout
</match>
```

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>问题：无法通过代理连接到 Log Analytics

### <a name="probable-causes"></a>可能的原因
* 在载入期间指定的代理不正确
* Log Analytics 和 Azure 自动化服务终结点不在数据中心的允许列表中 

### <a name="resolution"></a>解决方法
1. 使用以下命令（启用了 `-v` 选项）通过 Log Analytics Linux 代理重新载入到 Log Analytics 服务中。 它允许通过代理服务器连接到 Log Analytics 服务的代理能够进行详细输出。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. 请查看[更新代理设置](log-analytics-agent-manage.md#update-proxy-settings)部分，验证是否已将代理正确配置为通过代理服务器进行通信。    
* 仔细检查下列 Log Analytics 终结点是否在允许列表中：

    |代理资源| 端口 | 方向 |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | 端口 443| 入站和出站 |  
    |*.oms.opinsights.azure.com | 端口 443| 入站和出站 |  
    |* .blob.core.windows.net | 端口 443| 入站和出站 |  
    |* .azure-automation.net | 端口 443| 入站和出站 | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>问题：尝试载入时收到 403 错误

### <a name="probable-causes"></a>可能的原因
* Linux 服务器上的日期和时间不正确 
* 使用的工作区 ID 和工作区密钥不正确

### <a name="resolution"></a>解决方法

1. 使用 date 命令检查 Linux 服务器上的时间。 如果时间比当前时间快/慢 15 分钟，则载入失败。 若要纠正此问题，请更新 Linux 服务器的日期和/或时区。 
2. 验证你是否安装了最新版本的 Log Analytics Linux 代理。  如果时间偏差导致了载入故障，最新版本现在会发出通知。
3. 请按照本文前面所述的安装说明使用正确的工作区 ID 和工作区密钥重新载入。

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>问题：载入后，日志文件中立即显示 500 和 404 错误
这是第一次将 Linux 数据上传到 Log Analytics 工作区时发生的已知问题。 这不会影响发送的数据或服务体验。

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>问题：Azure 门户中未显示任何数据

### <a name="probable-causes"></a>可能的原因

- 载入到 Log Analytics 服务失败
- 与 Log Analytics 服务的连接受阻
- Log Analytics Linux 代理数据已备份

### <a name="resolution"></a>解决方法
1. 通过检查是否存在以下文件，来检查是否已成功载入 Log Analytics 服务：`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. 使用 `omsadmin.sh` 命令行指令重新载入
3. 如果使用代理，请参阅之前提供的代理解决方法步骤。
4. 在某些情况下，当 Log Analytics Linux 代理无法与此服务通信时，代理上的数据会在整个缓冲区（大小 50 MB）中排队。 该代理应通过运行以下命令重新启动：`/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`。 

    >[!NOTE]
    >此问题已在代理版本 1.1.0-28 及更高版本中解决。


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>问题：看不到转发的 Syslog 消息 

### <a name="probable-causes"></a>可能的原因
* 应用于 Linux 服务器的配置不允许收集已发送的设施和/或日志级别
* Syslog 未正确转发到 Linux 服务器
* 每秒转发的消息数太大，Log Analytics Linux 代理基本配置无法处理

### <a name="resolution"></a>解决方法
* 验证 Syslog 的 Log Analytics 工作区中的配置是否具有所有设施和正确的日志级别。 查看[在 Azure 门户中配置 Syslog 收集](log-analytics-data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* 验证本机 Syslog 消息守护程序（`rsyslog`、`syslog-ng`）是否能够接收转发的消息
* 检查 Syslog 服务器的防火墙设置，以确保未阻止消息
* 使用 `logger` 命令模拟向 Log Analytics 发送的 Syslog 消息
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>问题：收到的 Errno 地址已在 omsagent 日志文件中使用
如果你在 omsagent.log 中看到 `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>`。

### <a name="probable-causes"></a>可能的原因
此错误指出 Linux 诊断扩展 (LAD) 与 Log Analytics Linux VM 扩展并行安装，并且它使用 syslog 数据收集所用的端口作为 omsagent。

### <a name="resolution"></a>解决方法
1. 以 root 身份执行以下命令（请注意，25224 只是举例，你可能在自己的环境中看到 LAD 使用不同的端口号）：

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    然后，你需要编辑正确的 `rsyslogd` 或 `syslog_ng` 配置文件，并更改要写入到端口 25229 的 LAD 相关配置。

2. 如果 VM 正在运行 `rsyslogd`，要修改的文件是：`/etc/rsyslog.d/95-omsagent.conf`（如果不存在，则修改 `/etc/rsyslog`）。 如果 VM 正在运行 `syslog_ng`，要修改的文件是：`/etc/syslog-ng/syslog-ng.conf`。
3. 重新启动 omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`。
4. 重新启动 syslog 服务。

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>问题：无法使用清除选项卸载 omsagent

### <a name="probable-causes"></a>可能的原因

* Linux 诊断扩展已安装
* Linux 诊断扩展已安装和卸载，但你仍会看到以下相关错误：omsagent 已被 mdsd 使用，无法删除。

### <a name="resolution"></a>解决方法
1. 卸载 Linux 诊断扩展 (LAD)。
2. 如果 Linux 诊断扩展文件出现在以下位置，请从计算机中删除：`/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` 和 `/var/opt/microsoft/omsagent/LAD/`。

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>问题：你无法看到任何 Nagios 数据 

### <a name="probable-causes"></a>可能的原因
* Omsagent 用户没有权限从 Nagios 日志文件中读取
* Nagios 源和筛选器未从 omsagent.conf 文件中注释掉

### <a name="resolution"></a>解决方法
1. 遵循以下这些[说明](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)添加 omsagent 用户以从 Nagios 文件读取。
2. 在 Log Analytics Linux 代理常规配置文件的 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 处，确保 Nagios 源和筛选器**均已**被注释掉。

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>问题：看不到任何 Linux 数据 

### <a name="probable-causes"></a>可能的原因
* 载入到 Log Analytics 服务失败
* 与 Log Analytics 服务的连接受阻
* 虚拟机已重新启动
* 相比 Log Analytics Linux 代理程序包安装的版本，OMI 程序包已手动升级到较新版本
* DSC 资源在 `omsconfig.log` 日志文件中记录“找不到类”错误
* Log Analytics 代理数据已备份
* DSC 记录“当前配置不存在。执行 Start-DscConfiguration 命令及 -Path 参数来指定配置文件并先创建当前的配置。” （在 `omsconfig.log` 日志文件中），但不存在关于 `PerformRequiredConfigurationChecks` 操作的日志消息。

### <a name="resolution"></a>解决方法
1. 安装 auditd 程序包等所有依赖项。
2. 通过检查是否存在以下文件，来检查是否已成功载入 Log Analytics 服务：`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`。  如果它不存在，使用 omsadmin.sh 命令行[指令](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)重新载入。
4. 如果使用代理服务器，请检查上述代理服务器故障排除步骤。
5. 在某些 Azure 分发系统中，omid OMI 服务器后台程序在重新启动虚拟机后未启动。 这将导致看不到 Audit、ChangeTracking 或 UpdateManagement 解决方案相关的数据。 解决方法是通过运行 `sudo /opt/omi/bin/service_control restart` 来手动启动 omi 服务器。
6. OMI 程序包手动升级到较新版本后，必须手动重新启动，Log Analytics 代理才能继续运行。 对于其中 OMI 服务器在升级之后无法自动启动的分发，此为必需步骤。 运行 `sudo /opt/omi/bin/service_control restart` 重新启动 OMI。
7. 如果在 omsconfig.log 中看到 DSC 资源“找不到类”错误，请运行 `sudo /opt/omi/bin/service_control restart`。
8. 在某些情况下，当 Log Analytics Linux 代理无法与 Log Analytics 服务通信时，代理上的数据会备份到整个缓冲区（大小 50 MB）。 该代理应通过运行以下命令重新启动：`/opt/microsoft/omsagent/bin/service_control restart`。

    >[!NOTE]
    >此问题已在代理版本 1.1.0-28 或更高版本中解决
    >

* 如果 `omsconfig.log` 日志文件未指出 `PerformRequiredConfigurationChecks` 操作定期在系统上运行，cron 作业/服务可能会有问题。 请确保 `/etc/cron.d/OMSConsistencyInvoker` 下存存在 cron 作业。 如果需要，请运行以下命令创建 cron 作业：

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    此外，请确保 cron 服务正在运行。 可以将 `service cron status` 与 Debian、Ubuntu、SUSE 结合使用，或者将 `service crond status` 与 RHEL、CentOS、Oracle Linux 结合使用，来检查此服务的状态。 如果不存在该服务，可以安装二进制文件并使用以下命令启动该服务：

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>问题：在从 Syslog 或 Linux 性能计数器的门户配置收集时，未应用这些设置

### <a name="probable-causes"></a>可能的原因
* Log Analytics Linux 代理未获取最新配置
* 未应用门户中的已更改设置

### <a name="resolution"></a>解决方法
背景：`omsconfig` 是每隔五分钟便会查找新门户端配置的 Log Analytics Linux 配置代理。 然后，此配置会应用到位于以下位置的 Log Analytics Linux 代理配置文件中：/etc/opt/microsoft/omsagent/conf/omsagent.conf。

* 在某些情况下，Log Analytics Linux 配置代理可能无法与导致未应用最新配置的门户配置服务通信。
  1. 通过运行 `dpkg --list omsconfig` 或 `rpm -qi omsconfig` 检查是否已安装 `omsconfig` 代理。  如果未安装，请重新安装最新版本的 Log Analytics Linux 代理。

  2. 通过运行以下命令检查 `omsconfig` 是否可以与 Log Analytics 服务进行通信：`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`。 此命令返回代理从该服务中收到的配置（包括 Syslog 设置、Linux 性能计数器和自定义日志）。 如果此命令失败，请运行以下命令：`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`。 此命令会强制 omsconfig 代理与 Log Analytics 服务进行通信并检索最新的配置。

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>问题：看不到任何自定义日志数据 

### <a name="probable-causes"></a>可能的原因
* 载入到 Log Analytics 服务失败。
* 未选择“将下列配置应用于我的 Linux 服务器”设置。
* omsconfig 尚未从该服务获取最新的自定义日志配置。
* Log Analytics Linux 代理用户 `omsagent` 无法访问自定义日志，原因是没有权限或者找不到该日志。  可能会看到如下错误：
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* 已知的争用条件问题在 Log Analytics Linux 代理版本 1.1.0-217 中已修复

### <a name="resolution"></a>解决方法
1. 通过检查是否存在以下文件，来验证是否已成功载入 Log Analytics：`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`。 如果不存在，则可以：  

  1. 使用 omsadmin.sh 命令行[指令](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)重新载入。
  2. 在 Azure 门户的“高级设置”下，确保已启用“将以下配置应用于我的 Linux 服务器”设置。  

2. 通过运行以下命令检查 `omsconfig` 是否可以与 Log Analytics 服务进行通信：`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`。  此命令返回代理从该服务中收到的配置（包括 Syslog 设置、Linux 性能计数器和自定义日志）。 如果此命令失败，请运行以下命令：`sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`。 此命令会强制 omsconfig 代理与 Log Analytics 服务进行通信并检索最新的配置。

背景：Log Analytics Linux 代理不是以具有特权的用户 `root` 身份运行，而是以 `omsagent` 用户身份运行。 在大多数情况下，必须为此用户授予显式权限以便读取某些文件。 要为 `omsagent` 用户授予权限，请运行以下命令︰

1. 将 `omsagent` 用户添加到特定组 `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. 授予对所需文件 `sudo chmod -R ugo+rx <FILE DIRECTORY>` 的通用读取权限

这是 1.1.0-217 之前的 Log Analytics Linux 代理版本中已知的争用条件问题。 更新到最新的代理后，运行以下命令以获取最新版本的输出插件：`sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`。

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>问题：你正尝试重新载入到新的工作区
当你尝试将代理重新载入新的工作区时，载入之前需要清理 Log Analytics 代理配置。 若要清理代理中的旧配置，请使用 `--purge` 运行shell 捆绑包

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
或

```
sudo sh ./onboard_agent.sh --purge
```

你可以在使用 `--purge` 选项后继续重新载入

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>在 Azure 门户中，log Analytics 代理扩展标记为失败状态：预配失败

### <a name="probable-causes"></a>可能的原因
* 已从操作系统中删除 log Analytics 代理
* Log Analytics 代理服务已关闭、已禁用或未配置

### <a name="resolution"></a>解决方法 
执行以下步骤来更正问题。
1. 从 Azure 门户中删除扩展。
2. 按照[说明](log-analytics-quick-collect-linux-computer.md)安装代理。
3. 运行以下命令重启代理：`sudo /opt/microsoft/omsagent/bin/service_control restart`。
* 等待几分钟，并将预配状态更改为“预配成功”。


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>问题：Log Analytics 代理升级按需进行

### <a name="probable-causes"></a>可能的原因

主机上的 Log Analytics 代理程序包已过期。

### <a name="resolution"></a>解决方法 
执行以下步骤来更正问题。

1. 查看[页面](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/)上的最新版本。
2. 下载安装脚本（1.4.2-124 作为示例版本）：

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. 通过执行以下命令升级程序包：`sudo sh ./omsagent-*.universal.x64.sh --upgrade`。

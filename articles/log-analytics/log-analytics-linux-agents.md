---
title: "将 Linux 计算机连接到 Azure Log Analytics | Microsoft 文档"
description: "使用 Log Analytics，可以帮助收集和分析处理从 Linux 计算机生成的数据。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 10da54ebce28dfdcd0f793b1c717e738150b72ae
ms.contentlocale: zh-cn
ms.lasthandoff: 05/25/2017


---
# <a name="connect-your-linux-computers-to-log-analytics"></a>将 Linux 计算机连接到 Log Analytics
使用 Log Analytics，可以帮助收集和分析处理从 Linux 计算机生成的数据。 通过将从 Linux 收集的数据添加到 OMS，你几乎可以在任何地方管理 Linux 系统和容器解决方案（如 Docker），而无需考虑计算机所在的物理位置。 数据源可以作为物理服务器驻留在本地数据中心中、云托管服务（如 Amazon Web Services (AWS) 或 Microsoft Azure）中的虚拟计算机中，甚至办公桌上的笔记本电脑中。 此外，OMS 同样还可以从 Windows 计算机收集数据，因此它支持真正的混合 IT 环境。

你可以使用单一管理门户，在 OMS 中借助 Log Analytics 查看和管理所有这些数据源。 这样，无需使用多个不同的系统便可监视数据源，使得数据源的使用更加容易，并且你可以将喜欢的任何数据导出到已有的任何业务分析解决方案或系统。

本文是快速入门指南，可帮助你使用 OMS Agent for Linux 收集和管理 Linux 计算机的数据。 有关更多技术详细信息（如代理服务器配置、CollectD 指标和自定义 JSON 数据源的相关信息），请参阅 GitHub 上的 [OMS Agent for Linux 概述](https://github.com/Microsoft/OMS-Agent-for-Linux)和 [OMS Agent for Linux 完整文档](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)。

目前，可以从 Linux 计算机收集以下类型的数据︰

* 性能指标
* Syslog 事件
* 来自 Nagios 和 Zabbix 的警报
* Docker 容器性能指标、库存和日志

## <a name="supported-linux-versions"></a>支持的 Linux 版本
很多 Linux 发行版对 X86 和 x64 版本均提供官方支持。 不过，OMS Agent for Linux 在未列出的其他发行版上可能也可以运行。

* Amazon Linux 2015.09 - 2012.09
* CentOS Linux 5、6 和 7
* Oracle Linux 5、6 和 7
* Red Hat Enterprise Linux Server 5、6 和 7
* Debian GNU/Linux 6、7 和 8
* Ubuntu 12.04 LTS、14.04 LTS、15.04 和 15.10
* SUSE Linux Enterprise Server 11 和 12

## <a name="oms-agent-for-linux"></a>OMS Agent for Linux
Operations Management Suite Agent for Linux 包含多个程序包。 发布文件包含以下程序包，可通过使用 `--extract` 运行 shell 捆绑包获取这些程序包。

| **程序包** | **版本** | **说明** |
| --- | --- | --- |
| omsagent |1.1.0 |Operations Management Suite Agent for Linux |
| omsconfig |1.1.1 |配置 OMS Agent 的代理 |
| omi |1.0.8.3 |Open Management Infrastructure (OMI) - 一款轻量级 CIM 服务器 |
| scx |1.6.2 |操作系统性能指标的 OMI CIM 提供程序 |
| apache-cimprov |1.0.0 |OMI 的 Apache HTTP 服务器性能监视提供程序。 仅当检测到 Apache HTTP 服务器时才安装。 |
| mysql-cimprov |1.0.0 |OMI 的 MySQL 服务器性能监视提供程序。 仅当检测到 MySQL/MariaDB 服务器时才安装。 |
| docker-cimprov |0.1.0 |OMI 的 Docker 提供程序。 仅当检测到 Docker 时才安装。 |

### <a name="additional-installation-artifacts"></a>其他安装项目
在安装 OMS Agent for Linux 程序包后，将应用下列其他系统范围的配置更改。 卸载 omsagent 程序包时会删除这些项目。

* 创建一个名为 `omsagent` 的非特权用户。 这是 omsagent 守护程序运行时使用的帐户
* 在 /etc/sudoers.d/omsagent 目录下创建一个 sudoers “include” 文件。这将授权 omsagent 重新启动 syslog 和 omsagent 守护程序。 如果安装的 sudo 版本不支持 sudo “include” 指令，则会将这些条目写入 /etc/sudoers。
* 修改 syslog 配置，以将事件子集转发到代理。 有关详细信息，请参阅下面的**配置数据收集**一节

### <a name="linux-data-collection-details"></a>Linux 数据收集详细信息
下表显示数据收集方法以及有关如何收集数据的其他详细信息。

| 源 | 直接代理 | SCOM 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 分钟 |
| Nagios |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |到达时 |
| syslog |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |来自 Azure 存储：10 分钟；来自代理：到达时 |
| Linux 性能计数器 |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |根据计划，最小值为 10 秒 |
| 更改跟踪 |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |每小时 |

### <a name="package-requirements"></a>程序包要求
| **必需的程序包** | **说明** | **最低版本** |
| --- | --- | --- |
| Glibc |GNU C 库 |2.5-12 |
| Openssl |OpenSSL 库 |0.9.8e 或 1.0 |
| Curl |cURL Web 客户端 |7.15.5 |
| Python-ctype |函数库 |不适用 |
| PAM |可插拔身份验证模块 |不适用 |

> [!NOTE]
> 收集 syslog 消息时需要 rsyslog 或 syslog ng。 不支持将 Red Hat Enterprise Linux 版本 5、CentOS 和 Oracle Linux 版本 (sysklog) 上的默认 syslog 守护程序用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。
>
>

## <a name="quick-install"></a>快速安装
运行以下命令以下载 omsagent，验证校验和，然后安装和载入代理。 这些命令适用于 64 位。 在 OMS 门户中，在“**连接的源**”选项卡上的“**设置**”下，找到工作区 ID 和主键。

![工作区详细信息](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

可以使用多种其他方法来安装和升级代理。 有关这些方法的详细信息，请参阅[安装 OMS Agent for Linux 的步骤](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux)。

你还可以观看 [Azure 视频演练](https://www.youtube.com/watch?v=mF1wtHPEzT0)。

## <a name="choose-your-linux-data-collection-method"></a>选择 Linux 数据收集方法
如何选择想要收集的数据类型取决于是要使用 OMS 门户，还是要直接在 Linux 客户端上编辑各种配置文件。 如果选择使用门户，则会自动将配置发送到你的所有 Linux 客户端。 如果不同的 Linux 客户端需要不同的配置，则需要单独编辑客户端文件，或使用 PowerShell DSC、Chef 或 Puppet 等替代方法。

你可以指定要使用 Linux 计算机上的配置文件收集的 syslog 事件和性能计数器。 *如果选择通过编辑代理配置文件来配置数据收集，则应禁用集中式配置。*  下面内容说明了如何在代理的配置文件中配置数据收集，以及如何禁用所有 OMS Agents for Linux 或单个计算机的集中配置。

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>禁用单个 Linux 计算机的 OMS 管理
使用 OMS_MetaConfigHelper.py 脚本对单个 Linux 计算机禁用配置数据的集中式数据收集。 如果有一部分计算机需要专门配置，这种方法很有用。

禁用集中式配置︰

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

重新启用集中式配置︰

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Linux 性能计数器
Linux 性能计数器类似于 Windows 性能计数器 — 运行方式类似。 可以使用以下过程添加和配置性能计数器。 性能计数器添加到 OMS 后，每隔 30 秒收集一次数据。

### <a name="to-add-a-linux-performance-counter-in-oms"></a>在 OMS 中添加 Linux 性能计数器
1. 要使用 OMS 门户配置 OMS Agents for Linux，你可以在“设置”页中添加 Linux 性能计数器，然后单击“**数据**”。  
2. 在“**设置**”页中的“**数据**”下，单击“**Linux 性能计数器**”，然后选择或键入要添加的计数器名称。  
    ![数据](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. 如果不知道计数器的全名，你可以开始键入部分名称，系统会显示可用的计数器列表。 当找到想要添加的计数器时，请单击列表中的名称，然后单击加号图标以添加计数器。
4. 添加计数器后，它将在计数器列表中以彩色条突出显示。
5. 默认情况下，已选择“**将下列配置用于我的计算机**”选项。 如果想要禁止发送配置数据，请清除该选项。
6. 完成性能计数器修改后，在页面底部单击“**保存**”以完成所做的更改。 然后，所做的配置更改通常会在 5 分钟内发送到所有使用 OMS 注册的 OMS Agents for Linux。

### <a name="configure-linux-performance-counters-in-oms"></a>在 OMS 中配置 Linux 性能计数器
对于 Windows 性能计数器，可以为每个性能计数器选择一个特定实例。 但是，对于 Linux 性能计数器，选择的计数器实例会应用于父计数器的所有子计数器。 下表显示 Linux 和 Windows 性能计数器的可用通用实例。

| **实例名称** | **含义** |
| --- | --- |
| \_Total |所有实例的总计 |
| \* |所有实例 |
| (/&#124;/var) |匹配命名的实例：/ 或 /var |

同样，为父计数器选择的采样间隔会应用于其所有子计数器。 换言之，所有子计数器的采样间隔和实例都绑定在一起。

### <a name="add-and-configure-performance-metrics-with-linux"></a>使用 Linux 添加和配置性能指标
要收集的性能指标由 /etc/opt/microsoft/omsagent/&lt;工作区 id&gt;/conf/omsagent.conf 中的配置控制。 有关 OMS Agent for Linux 的可用类和指标，请参阅[可用的性能指标](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics)。

要收集的性能指标的每个对象或类别应在配置文件中作为单个 `<source>` 元素进行定义。 语法遵循下面的模式。

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

此元素的可配置参数包括︰

* **Object\_name**︰收集的对象名称。
* **Instance\_regex**：用于定义要收集的实例的*正则表达式*。 值 `.*` 指定所有实例。 要仅收集 \_Total 实例的处理器指标，可以指定 `_Total`。 要仅收集 crond 或 sshd 实例的进程指标，可以指定 `(crond|sshd)`。
* **Counter\_name\_regex**：用于定义要收集的对象计数器的*正则表达式*。 要收集对象的所有计数器，请指定：`.*`。 要仅收集内存对象的交换空间计数器，可以指定 `.+Swap.+`
* **Interval**︰收集对象计数器的频率。

性能指标的默认配置如下︰

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>使用 Linux 命令启用 MySQL 性能计数器
如果当安装 omsagent 捆绑包时在计算机上检测到 MySQL 服务器或 MariaDB 服务器，则会自动安装 MySQL 服务器的性能监视提供程序。 此提供程序连接到本地 MySQL/MariaDB 服务器以公开性能统计信息。 你需要配置 MySQL 用户凭据，以便提供程序可以访问 MySQL 服务器。

要在 localhost 上定义 MySQL 服务器的默认用户帐户，请使用以下命令示例。

> [!NOTE]
> 凭据文件必须可供 omsagent 帐户读取。 建议以 omsgent 身份运行 mycimprovauth 命令。
>
>

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>

sudo /opt/omi/bin/service_control restart
```


或者，可以通过创建 /var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth 文件，在文件中指定所需的 MySQL 凭据。 有关通过 mysql-auth 文件管理用于监视的 MySQL 凭据的详细信息，请参阅[在身份验证文件中管理 MySQL 监视凭据](#manage-mysql-monitoring-credentials-in-the-authentication-file)。

有关 MySQL 用户收集 MySQL 服务器性能数据时所需的对象权限的详细信息，请参阅 [MySQL 性能计数器所需的数据库权限](#database-permissions-required-for-mysql-performance-counters)。

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>使用 Linux 命令启用 Apache HTTP服务器性能计数器
如果安装 omsagent 捆绑包时在计算机上检测到 Apache HTTP 服务器，则会自动安装 Apache HTTP 服务器的性能监视提供程序。 此提供程序依赖于必须加载到 Apache HTTP 服务器才能访问性能数据的 Apache“模块”。

可以使用以下命令加载该模块︰

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

要卸载 Apache 监视模块，请运行以下命令︰

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>使用 Log Analytics 查看性能数据
1. 在 Operations Management Suite 门户中，单击“日志搜索”磁贴。
2. 在搜索栏中键入 `* (Type=Perf)` 以查看所有性能计数器。

由于 OMS 还会收集 Windows 性能计数器数据，你应将搜索范围缩小到特定于 Linux 的数据。 因此，下面的示例将会显示特定于名为 Chorizo21 的示例 Linux 服务器的性能数据。

```
Type=Perf Computer=chorizo*
```

![在搜索结果中显示的示例服务器](./media/log-analytics-linux-agents/oms-perfsearch01.png)

在结果中，可以单击“**指标**”以查看收集的数据的计数器。 对于每个计数器，以图形形式显示实时数据。

![指标](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>Syslog
Syslog 是类似于 Windows 事件日志的事件日志记录协议 — 它们在 OMS 中显示时运行方式类似。

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>在 OMS 中添加新的 Linux syslog 设施
1. 在“**设置**”页上的“**数据**”下，单击“**Syslog**”，然后在加号图标左侧，键入要添加的 syslog 设施的名称。
    ![Linux syslog](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. 如果不知道设施的全名，你可以开始键入部分名称，系统将显示可用 syslog 设施的列表。 当找到要添加的 syslog 设施时，请单击列表中的名称，然后单击加号图标以添加 syslog 设施。
3. 添加设施后，它将在计数器列表中以彩色条突出显示。 接下来，选择要收集的（syslog 设施信息的）严重性类别。
4. 在页面底部，单击“**保存**”完成更改。 然后，所做的配置更改通常会在 5 分钟内发送到所有使用 OMS 注册的 OMS Agents for Linux。

### <a name="configure-linux-syslog-facilities-in-linux"></a>在 Linux 中配置 Linux syslog 设施
Syslog 事件从 syslog 守护程序（例如 rsyslog 或 syslog-ng）发送到代理正在侦听的本地端口。 默认情况下，端口为 25224。 安装代理时，会应用默认的 syslog 配置。 可以在以下位置中找到默认配置︰

Rsyslog：/etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng：/etc/syslog-ng/syslog-ng.conf

默认的 OMS 代理 syslog 配置从所有设施上传带有警告或更高严重性的 syslog 事件。

> [!NOTE]
> 如果编辑 syslog 配置，必须重新启动 syslog 守护程序才能使更改生效。
>
>

OMS 的 OMS Agent for Linux 默认 syslog 配置如下︰

#### <a name="rsyslog"></a>Rsyslog
```
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
```

#### <a name="syslog-ng"></a>Syslog-ng
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>使用 Log Analytics 查看所有 syslog 事件
1. 在 Operations Management Suite 门户中，单击“**日志搜索**”磁贴。
2. 在“**日志管理**”组中，选择预定义的 syslog 搜索，然后选择一个来运行。

此示例显示所有 Syslog 事件。

![日志搜索中显示的 Syslog 事件](./media/log-analytics-linux-agents/oms-linux-syslog.png)

现在，你可以钻取到搜索结果。

## <a name="linux-alerts"></a>Linux 警报
如果你使用 Nagios 或 Zabbix 管理 Linux 计算机，则 OMS 可以接收从这些工具生成的警报。 但是，目前尚没有方法使用 OMS 门户来配置传入的警报数据。 相反，你需要编辑配置文件以开始向 OMS 发送警报。

### <a name="collect-alerts-from-nagios"></a>收集来自 Nagios 的警报
要收集来自 Nagios 服务器的警报，需要进行以下配置更改。

1. 向用户 **omsagent** 授予对 Nagios 日志文件（即 /var/log/nagios/nagios.log）的读访问权限。 假定 nagios.log 文件归组 **nagios** 所有，你可以将用户 **omsagent** 添加到 **nagios** 组。

    ```
    sudo usermod –a -G nagios omsagent
    ```
2. 修改 omsagent.conf 配置文件 (/etc/opt/microsoft/omsagent/&lt;工作区 id&gt;/conf/omsagent.conf)。 确保以下条目存在且未注释掉︰

    ```
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
    ```
3. 重新启动 omsagent 守护程序︰

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="collect-alerts-from-zabbix"></a>收集来自 Zabbix 的警报
要收集来自 Zabbix 服务器的警报，你可以执行类似于上述 Nagios 的步骤，但需要以*明文*指定用户和密码。 这不是理想的做法，但可能会很快更改。 要解决此问题，建议你创建用户并仅为其授予监视权限。

Zabbix 的 omsagent.conf 配置文件 (/etc/opt/microsoft/omsagent/&lt;工作区 id&gt;/conf/omsagent.conf) 的示例部分应类似如下所示：

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### <a name="view-alerts-in-log-analytics-search"></a>在 Log Analytics 搜索中查看警报
配置 Linux 计算机以将警报发送到 OMS 后，可以使用几个简单的日志搜索查询来查看警报。 下面的搜索查询示例返回生成的所有已记录警报。 例如，如果 IT 基础结构中发生某种类型的问题，则下面的示例查询结果可能指示问题的起源。 你可以按源系统轻松钻取到警报，以缩小调查范围。 优点是不必从头开始访问各种管理系统 — 前提是警报发送到 OMS，你可以从那里开始查看。

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>使用 Log Analytics 查看所有 Nagios 警报
1. 在 Operations Management Suite 门户中，单击“**日志搜索**”磁贴。
2. 在查询栏中，键入下面的搜索查询

    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![Log Search 中显示的 Nagios 警报](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

看到搜索结果后，你可以钻取到更多详细信息，例如 *AlertState*。

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>使用 Log Analytics 查看所有 Zabbix 警报
1. 在 Operations Management Suite 门户中，单击“**日志搜索**”磁贴。
2. 在查询栏中，键入下面的搜索查询

    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![Log Search 中显示的 Zabbix 警报](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

看到搜索结果后，你可以钻取到更多详细信息，例如 *AlertName*。

## <a name="compatibility-with-system-center-operations-manager"></a>与 System Center Operations Manager 的兼容性
OMS Agent for Linux 与 System Center Operations Manager 代理共享代理二进制文件。 在当前由 Operations Manager 管理的系统上安装 OMS Agent for Linux 会将计算机上的 OMI 和 SCX 程序包升级到较新版本。 OMS Agent for Linux 与 System Center 2012 R2 兼容。 但是，**System Center 2012 SP1 及更早版本目前不与 OMS Agent for Linux 兼容或不受它支持。**

> [!NOTE]
> 如果 OMS Agent for Linux 安装到当前未由 Operations Manager 管理的计算机，而你稍后想要使用 Operations Manager 管理该计算机时，必须修改 OMI 配置才能发现该计算机。 **如果在安装 OMS Agent for Linux 之前安装了 Operations Manager 代理，则不需要此步骤。**
>
>

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>允许 OMS Agent for Linux 与 Operations Manager 进行通信
1. 编辑文件 /etc/opt/omi/conf/omiserver.conf
2. 确保以 **httpsport=** 开头的行定义了端口 1270。 例如，`httpsport=1270`
3. 重新启动 OMI 服务器︰

    ```
    sudo /opt/omi/bin/service_control restart
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL 性能计数器所需的数据库权限
要为 MySQL 监视用户授予权限，授予用户必须具有 'GRANT option' 特权以及要授予的特权。

要让 MySQL 用户返回性能数据，该用户需要访问以下查询︰

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

除了这些查询，MySQL 用户还需要对以下默认值表具有 SELECT 访问权限︰

* information_schema
* mysql

可以通过运行以下授予命令来授予这些特权。

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>在身份验证文件中管理 MySQL 监视凭据
以下部分帮助你管理 MySQL 凭据。

### <a name="configure-the-mysql-omi-provider"></a>配置 MySQL OMI 提供程序
MySQL OMI 提供程序需要预先配置 MySQL 用户并安装 MySQL 客户端库，才能从 MySQL 实例中查询性能/运行状况信息。

### <a name="mysql-omi-authentication-file"></a>MySQL OMI 身份验证文件
MySQL OMI 提供程序使用身份验证文件确定 MySQL 实例正在侦听的绑定地址和端口以及用于收集指标的凭据。 在安装期间，MySQL OMI 提供程序将扫描绑定地址和端口的 MySQL my.cnf 配置文件（默认位置）并部分设置 MySQL OMI 身份验证文件。

要完成对 MySQL 服务器实例的监视，请将预先生成的 MySQL OMI 身份验证文件添加到正确的目录。

### <a name="authentication-file-format"></a>身份验证文件格式
MySQL OMI 身份验证文件是文本文件，包含以下信息︰

* 端口
* 绑定地址
* MySQL 用户名
* Base64 编码的密码

MySQL OMI 身份验证文件仅向生成密码的 Linux 用户授予读/写权限。

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

默认 MySQL OMI 身份验证文件包含默认实例和端口号，具体取决于从找到的 MySQL 配置文件中获取并解析了哪些信息。

默认实例是一种可使在一台 Linux 主机上管理多个 MySQL 实例更加容易的方法，由带有端口 0 的实例表示。 所有添加的实例将继承默认实例中设置的属性。 例如，如果添加了在端口 "3308" 上侦听的 MySQL 实例，则默认实例的绑定地址、用户名和 Base64 编码的密码将用于尝试并监视 3308 上侦听的实例。 如果 3308 上的实例绑定到另一个地址，且使用相同的 MySQL 用户名和密码对，则只需要重新指定绑定地址，其他属性将被继承。

身份验证文件的示例如下所示。

默认实例和带有端口 3308 的实例︰

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

默认实例和带有端口 3308 + 不同 Base 64 编码密码的实例︰

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **属性** | **说明** |
| --- | --- |
| 端口 |端口表示 MySQL 实例正在侦听的当前端口。  端口 0 意味着对于默认实例使用以下属性。 |
| 绑定地址 |绑定地址是当前的 MySQL 绑定地址 |
| username |这是你希望用于监视的 MySQL 服务器实例的 MySQL 用户的用户名。 |
| Base64 编码的密码 |这是以 Base64 编码的 MySQL 监视用户的密码。 |
| AutoUpdate |当升级 MySQL OMI 提供程序时，提供程序将重新扫描 my.cnf 文件中的更改，并覆盖 MySQL OMI 身份验证文件。 根据 MySQL OMI 身份验证文件是否需要更新，将此标志设置为 true 或 false。 |

#### <a name="authentication-file-location"></a>身份验证文件位置
MySQL OMI 身份验证文件应位于以下位置并命名为 "mysql-auth"：

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

该文件（和 auth/omsagent 目录）应由 omsagent 用户所有。

## <a name="agent-logs"></a>代理日志
OMS Agent for Linux 的日志位于以下位置︰

/var/opt/microsoft/omsagent/&lt;工作区 id&gt;/log/

omsconfig（代理配置）程序的 OMS Agent for Linux 日志位于以下位置︰

/var/opt/microsoft/omsconfig/log/

OMI 和 SCX 组件（这些组件提供性能指标数据）的日志位于以下位置︰

/var/opt/omi/log/ and /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>OMS Agent for Linux 疑难解答
使用以下信息来诊断和解决常见问题。

如果本部分中的疑难解答信息没有帮助，还可以使用以下资源来帮助解决问题。

* 具有 Premier 支持的客户可以通过 [Premier](https://premier.microsoft.com/) 来记录支持案例
* 具有 Azure 支持协议的客户可以在 [Azure 门户](https://manage.windowsazure.com/?getsupport=true) 中记录支持案例
* 提出 [GitHub 问题](https://github.com/Microsoft/OMS-Agent-for-Linux/issues)
* 从论坛反馈获得想法，并创建缺陷报告 [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>重要的日志位置
| 文件 | 路径 |
| --- | --- |
| OMS Agent for Linux 日志文件 |`/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log ` |
| OMS Agent 配置日志文件 |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>重要的配置文件
| 类别 | 文件位置 |
| --- | --- |
| Syslog |`/etc/syslog-ng/syslog-ng.conf` 或 `/etc/rsyslog.conf` 或 `/etc/rsyslog.d/95-omsagent.conf` |
| 性能、Nagios、Zabbix、OMS 输出和常规代理 |`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` |
| 其他配置 |`/etc/opt/microsoft/omsagent/<workspace id>/omsagent.d/*.conf` |

> [!NOTE]
> 如果 OMS 门户配置已启用，则将覆盖性能计数器和 syslog 的编辑配置文件。 可以在 OMS 门户中针对所有节点禁用配置，也可以通过运行以下命令针对单个节点禁用配置︰
>
>

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>启用调试日志记录
要启用调试日志记录，可以使用 OMS 输出插件和详细输出。

#### <a name="oms-output-plugin"></a>OMS 输出插件
FluentD 允许插件针对输入和输出指定不同的日志级别。 要为 OMS 输出指定不同的日志级别，请在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 文件中编辑常规代理配置。

在靠近配置文件底部，将 `log_level` 属性从 `info` 更改为 `debug`。

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

调试日志记录允许按类型、数据项数量和发送所用时间查看批量上传至 OMS 服务的信息。

*启用调试日志的示例︰*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>详细输出
如果不使用 OMS 输出插件，还可以将数据项直接输出到 OMS Agent for Linux 日志文件中可见的 `stdout`。

在 OMS 常规代理配置文件中的 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 处，通过在每一行的前面添加 `#` 注释掉 OMS 输出插件。

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

在输出插件下面，通过删除每一行开头的 `#` 符号，删除以下部分中的注释。

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>转发的 Syslog 消息不出现在日志中
#### <a name="probable-causes"></a>可能的原因
* 应用于 Linux 服务器的配置不允许收集已发送的设施和/或日志级别
* Syslog 未正确转发到 Linux 服务器
* 每秒转发的消息数太大，OMS Agent for Linux 的基本配置无法处理

#### <a name="resolutions"></a>解决方法
* 验证 Syslog 的 OMS 门户中的配置是否具有所有设施和正确的日志级别
  * **OMS 门户 > 设置 > 数据 > Syslog**
* 验证本机 Syslog 消息守护程序（`rsyslog`、`syslog-ng`）是否能够接收转发的消息
* 检查 Syslog 服务器的防火墙设置，以确保未阻止消息
* 使用 `logger` 命令模拟 Syslog 消息发送给 OMS，例如︰
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>使用代理服务器连接到 OMS 时的问题
#### <a name="probable-causes"></a>可能的原因
* 安装和配置代理时指定的代理服务器不正确
* OMS 服务终结点不在数据中心的白名单中

#### <a name="resolutions"></a>解决方法
* 使用以下命令（启用了 `-v` 选项）重新安装 OMS Agent for Linux。 这允许通过代理服务器连接到 OMS 服务的代理能够进行详细输出。
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * 查看[将代理配置为使用 HTTP 代理服务器](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server)中的 OMS 代理服务器文档
* 验证下列 OMS 服务终结点是否在白名单中

| 代理资源 | 端口 |
| --- | --- |
| &#42;.ods.opinsights.azure.com |端口 443 |
| &#42;.oms.opinsights.azure.com |端口 443 |
| ods.systemcenteradvisor.com |端口 443 |
| &#42;.blob.core.windows.net/ |端口 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>载入时显示 403 错误
#### <a name="probable-causes"></a>可能的原因
* Linux 服务器上的日期和时间不正确
* 使用的工作区 ID 和工作区密钥不正确

#### <a name="resolution"></a>解决方法
* 使用 `date` 命令验证 Linux 服务器上的时间。 如果数据与当前时间的差距大于 15 分钟，则载入失败。 要纠正此问题，请更新 Linux 服务器的日期和/或时区。
* 如果时间差异导致载入失败，最新版本的 OMS Agent for Linux 会通知你。
* 使用正确的工作区 ID 和工作区密钥重新载入。 有关详细信息，请参阅[使用命令行载入](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)。

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>载入后在日志文件中显示 500 错误或 404 错误
这是第一次将 Linux 数据上传到 OMS 工作区的过程中发生的已知问题。 这不会影响发送的数据或产生其他问题。 可以忽略最初载入时的错误。

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Nagios 数据未显示在 OMS 门户中
#### <a name="probable-causes"></a>可能的原因
* omsagent 用户没有权限从 Nagios 日志文件中读取
* Nagios 源和筛选器部分仍在 omsagent.conf 文件中加有注释

#### <a name="resolutions"></a>解决方法
* 添加 omsagent 用户以从 Nagios 文件中读取。 有关详细信息，请参阅 [Nagios 警报](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)。
* 在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 处的 OMS Agent for Linux 常规配置文件中，确保 Nagios 源和筛选器部分**都**已删除注释，类似于下面的示例。

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


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Linux 数据不显示在 OMS 门户中
#### <a name="probable-causes"></a>可能的原因
* 载入到 OMS 服务失败
* 与 OMS 服务的连接受阻
* 备份了 OMS Agent for Linux 数据

#### <a name="resolutions"></a>解决方法
* 通过验证 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 是否存在，确认到 OMS 服务的载入已成功。
* 使用 omsadmin.sh 命令行重新载入。 有关详细信息，请参阅[使用命令行载入](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)。
* 如果使用代理服务器，请使用上述代理服务器疑难解答步骤
* 在某些情况下，当 OMS Agent for Linux 无法与 OMS 服务通信时，Agent 上的数据会备份到整个缓冲区（大小 50 MB）。 通过运行 `/opt/microsoft/omsagent/bin/service_control restart` 命令重新启动 OMS Agent for Linux。
  >[AZURE.NOTE] 此问题已在 Agent 1.1.0-28 及更高版本中解决。

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>Syslog Linux 性能计数器配置未在 OMS 门户中应用
#### <a name="probable-causes"></a>可能的原因
* OMS Agent for Linux 中的配置代理尚未从 OMS 门户检索最新的配置。
* 未应用门户中的已修改设置

#### <a name="resolutions"></a>解决方法
`omsconfig` 是 OMS Agent for Linux 中的配置代理，会每隔 5 分钟检索 OMS 门户配置更改。 此配置然后应用于 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 处的 OMS Agent for Linux 配置文件。

* 在某些情况下，OMS Agent for Linux 配置代理可能无法与门户配置服务通信，导致未应用最新配置。
* 使用以下命令验证是否安装了 `omsconfig` 代理：

  * `dpkg --list omsconfig` 或 `rpm -qi omsconfig`
  * 如果未安装，请重新安装最新版本的 OMS Agent for Linux
* 验证 `omsconfig` 代理是否可与 OMS 服务通信

  * 运行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` 命令
    * 上面的命令返回代理从门户中检索的配置（包括 Syslog 设置、Linux 性能计数器和自定义日志）
    * 如果上面的命令失败，则运行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` 命令。 此命令会强制 omsconfig 代理与 OMS 服务进行通信以检索最新的配置。

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>自定义 Linux 日志数据未显示在 OMS 门户中
#### <a name="probable-causes"></a>可能的原因
* 载入到 OMS 服务失败
* 未选择“**将下列配置应用于我的 Linux 服务器**”设置
* omsconfig 尚未从门户获取最新的自定义日志
* 由于权限问题或找不到 `omsagent`，`omsagent` 使用无法访问自定义日志。 在这种情况下，你将看到以下输出：
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* 这是一个已知的争用条件问题，已在 OMS Agent for Linux 版本 1.1.0-217 中修复

#### <a name="resolutions"></a>解决方法
* 通过确定 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 文件是否存在，确认已经成功载入。
  * 如果需要，使用 omsadmin.sh 命令行重新载入。 有关详细信息，请参阅[使用命令行载入](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)。
* 在 OMS 门户中，在“**数据**”选项卡的“**设置**”下，确保选择“**将下列配置应用于我的 Linux 服务器**”设置  
  ![应用配置](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* 验证 `omsconfig` 代理是否可与 OMS 服务通信

  * 运行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` 命令
  * 上面的命令返回代理从门户中检索的配置（包括 Syslog 设置、Linux 性能计数器和自定义日志）
  * 如果上面的命令失败，则运行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` 命令。 此命令会强制 omsconfig 代理与 OMS 服务进行通信并检索最新的配置。

OMS Agent for Linux 不是以具有特权的用户 `root` 身份运行，而是以 `omsagent` 用户身份运行。 在大多数情况下，必须为用户授予显式权限以便读取某些文件。

要为 `omsagent` 用户授予权限，请运行以下命令︰

1. 使用 `sudo usermod -a -G <GROUPNAME> <USERNAME>` 将 `omsagent` 用户添加到特定组
2. 使用 `sudo chmod -R ugo+rw <FILE DIRECTORY>` 授予对所需文件的通用读取权限

这是一个已知的争用条件问题，已在 OMS Agent for Linux 版本 1.1.0-217 中解决。 更新到最新的代理后，运行以下命令以获取最新版本的输出插件︰

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf
```

## <a name="known-limitations"></a>已知限制
查看以下各节以了解有关 OMS Agent for Linux 的当前限制。

### <a name="azure-diagnostics"></a>Azure 诊断
对于在 Azure 中运行的 Linux 虚拟机，可能需要附加步骤以允许由 Azure 诊断和 Operations Management Suite 收集数据。 为了与 OMS Agent for Linux 兼容，需要安装**版本 2.2** 的 Linux 诊断扩展。

有关安装和配置 Linux 诊断扩展的详细信息，请参阅[使用 Azure CLI 命令启用 Linux 诊断扩展](../virtual-machines/linux/classic/diagnostic-extension-v2.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension)。

**将 Linux 诊断扩展从 2.0 升级到 2.2 Azure CLI ASM：**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

这些命令示例引用名为 PrivateConfig.json 的文件。 该文件的格式应类似于下面的示例。

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>不支持 Sysklog
收集 Syslog 消息时需要 rsyslog 或 syslog ng。 不支持将 Red Hat Enterprise Linux 版本 5、CentOS 和 Oracle Linux 版本 (sysklog) 上的默认 syslog 守护程序用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。 有关使用 rsyslog 替换 sysklog 的详细信息，请参阅[安装新生成的 rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)。

## <a name="next-steps"></a>后续步骤
* [从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)，以添加功能和收集数据。
* 熟悉[日志搜索](log-analytics-log-searches.md)以查看解决方案收集的详细信息。
* 使用[仪表板](log-analytics-dashboards.md)保存并显示你自己的自定义搜索。


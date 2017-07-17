---
title: "将 Linux 计算机连接到 Operations Management Suite (OMS) | Microsoft Docs"
description: "本文介绍了如何使用 OMS Agent for Linux 将在 Azure 中、其他云中或本地托管的 Linux 计算机连接到 OMS。"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 79bbb4dfe03a6c1ae782abc1404e22343bde22a0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017

---

# 将 Linux 计算机连接到 Operations Management Suite (OMS)
<a id="connect-your-linux-computers-to-operations-management-suite-oms" class="xliff"></a> 

对于作为物理服务器或虚拟机驻留在本地数据中心的 Linux 计算机和容器解决方案（例如 Docker）以及云托管服务（例如 Amazon Web Services (AWS) 或 Microsoft Azure）中的虚拟机，可以使用 OMS 收集和处理从其中生成的数据。 还可以使用 OMS 中可用的管理解决方案来主动管理 Linux VM 的生命周期，比如使用更改跟踪来标识配置更改，使用更新管理来管理软件更新。 

OMS Agent for Linux 通过 TCP 端口 443 与 OMS 服务进行出站通信；如果计算机连接到防火墙或代理服务器以通过 Internet 进行通信，则请查看[对代理进行配置以便与 HTTP 代理服务器或 OMS 网关一起使用](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway)，了解需要应用哪些配置更改。  如果使用 System Center 2016 - Operations Manager 或 Operations Manager 2012 R2 监视计算机，该计算机可以与 OMS 服务进行多宿主连接，以便收集数据并将数据转发到该服务，且仍受 Operations Manager 监视。  受 Operations Manager 管理组监视并与 OMS 集成的 Linux 计算机不通过管理组接收数据源配置和转发收集的数据。  

如果 IT 安全策略不允许网络上的计算机连接到 Internet，可将代理配置为连接到 OMS 网关，以根据启用的解决方案接收配置信息并发送收集的数据。 有关如何将 OMS Linux Agent 配置为通过 OMS 网关与 OMS 服务进行通信的详细信息和步骤，请参阅[使用 OMS 网关将计算机连接到 OMS](log-analytics-oms-gateway.md)。  

下图描绘了代理管理的 Linux 计算机与 OMS 之间的连接（包括方向和端口）。

![直接代理与 OMS 通信的示意图](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## 系统要求
<a id="system-requirements" class="xliff"></a>
开始之前，请查看以下详细信息来验证是否满足先决条件。

### 支持的 Linux 操作系统
<a id="supported-linux-operating-systems" class="xliff"></a>
以下 Linux 分发版受官方支持。  不过，OMS Agent for Linux 在未列出的其他发行版上可能也可以运行。

* Amazon Linux 2012.09 --> 2015.09 (x86/x64)
* CentOS Linux 5、6 和 7 (x86/x64)
* Oracle Linux 5、6 和 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6 和 7 (x86/x64)
* Debian GNU/Linux 6、7 和 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS、15.04、15.10、16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 和 12 (x86/x64)

### 网络
<a id="network" class="xliff"></a>
下面的信息列出了 Linux 代理与 OMS 进行通信时必需的代理和防火墙配置信息。 流量从网络传出到 OMS 服务。 

|代理资源| 端口 |  
|------|---------|  
|*.ods.opinsights.azure.com | 端口 443|   
|*.oms.opinsights.azure.com | 端口 443|   
|ods.systemcenteradvisor.com | 端口 443|   
|*.blob.core.windows.net/ | 端口 443|   

### 程序包要求
<a id="package-requirements" class="xliff"></a>

 **必需的程序包**   | **说明**   | **最低版本**
--------------------- | --------------------- | -------------------
Glibc | GNU C 库   | 2.5-12 
Openssl | OpenSSL 库 | 0.9.8e 或 1.0
Curl | cURL Web 客户端 | 7.15.5
Python-ctype | | 
PAM | 可插拔身份验证模块   | 

> [!NOTE]
>  收集 Syslog 消息时需要 rsyslog 或 syslog ng。 不支持将 Red Hat Enterprise Linux 版本 5、CentOS 和 Oracle Linux 版本 (sysklog) 上的默认 syslog 守护程序用于 syslog 事件收集。 若要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。 

代理由多个程序包组成。 发布文件包含以下程序包，可通过使用 `--extract` 运行 shell 捆绑包获取这些程序包：

**程序包** | **版本** | **说明**
----------- | ----------- | --------------
omsagent | 1.3.4 | Operations Management Suite Agent for Linux
omsconfig | 1.1.1 | 配置 OMS Agent 的代理
omi | 1.2.0 | Open Management Infrastructure (OMI)：一款轻型 CIM 服务器
scx | 1.6.3 | 操作系统性能指标的 OMI CIM 提供程序
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 服务器性能监视提供程序。 在检测到 Apache HTTP 服务器时安装。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 服务器性能监视提供程序。 在检测到 MySQL/MariaDB 服务器时安装。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供程序。 在检测到 Docker 时安装。

### 与 System Center Operations Manager 的兼容性
<a id="compatibility-with-system-center-operations-manager" class="xliff"></a>
OMS Agent for Linux 与 System Center Operations Manager 代理共享代理二进制文件。 在当前由 Operations Manager 管理的系统上安装 OMS Agent for Linux 会将计算机上的 OMI 和 SCX 程序包升级到较新版本。 在此版本中，OMS Agent for Linux 与 System Center 2016 - Operations Manager/Operations Manager 2012 R2 Agent for Linux 相兼容。 

> [!NOTE]
> System Center 2012 SP1 及更早版本目前与 OMS Agent for Linux 不兼容或不受它支持。<br>
> 如果将 OMS Agent for Linux 安装到当前不受 Operations Manager 监视的计算机，之后又想使用 Operations Manager 监视该计算机，则必须修改 [OMI 配置](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager)才能发现该计算机。 **如果在安装 OMS Agent for Linux 之前安装了 Operations Manager 代理，则*不*需要执行此步骤。**

### 系统配置更改
<a id="system-configuration-changes" class="xliff"></a>
在安装 OMS Agent for Linux 程序包后，将应用下列其他系统范围的配置更改。 卸载 omsagent 程序包时会删除这些项目。

* 创建一个名为 `omsagent` 的非特权用户。 这是 omsagent 守护程序运行时使用的帐户。
* 在 /etc/sudoers.d/omsagent 中创建一个 sudoers “include” 文件。 这将授权 omsagent 重新启动 syslog 和 omsagent 守护程序。 如果安装的 sudo 版本不支持 sudo “include” 指令，则会将这些条目写入 /etc/sudoers。
* 修改 syslog 配置，以将事件子集转发到代理。 有关详细信息，请参阅下面的**配置数据收集**一节

### 从以前的版本升级
<a id="upgrade-from-a-previous-release" class="xliff"></a>
此版本支持从早于 1.0.0-47 的版本升级。 使用 `--upgrade` 命令执行安装时，会将代理的所有组件都升级到最新版本。

## 安装 OMS Agent for Linux
<a id="install-the-oms-agent-for-linux" class="xliff"></a>
OMS Agent for Linux 以自提取和可安装的 shell 脚本捆绑包形式提供。 此捆绑包包含用于各个代理组件的 Debian 和 RPM 程序包，可以直接安装该捆绑包，也可以通过提取它来检索单个程序包。 分别针对 x64 体系结构和 x86 体系结构提供一个捆绑包。 

### 安装代理
<a id="installing-the-agent" class="xliff"></a>

1. 使用 scp/sftp 将相应的捆绑包（x86 或 x64）传输到 Linux 计算机。
2. 使用 `--install` 或 `--upgrade` 参数安装捆绑包。 

    > [!NOTE]
    > 如果安装任何现有的程序包（例如在已安装 System Center Operations Manager Agent for Linux 时），请使用 `--upgrade` 参数。 若要在安装过程中连接到 Operations Management Suite，请提供 `-w <WorkspaceID>` 和 `-s <Shared Key>` 参数。

### 捆绑包命令行参数
<a id="bundle-command-line-arguments" class="xliff"></a>
```
Options:
  --extract              Extract contents and exit.
  --force                Force upgrade (override version checks).
  --install              Install the package from the system.
  --purge                Uninstall the package and remove all related data.
  --remove               Uninstall the package from the system.
  --restart-deps         Reconfigure and restart dependent service
  --source-references    Show source code reference hashes.
  --upgrade              Upgrade the package in the system.
  --version              Version of this shell bundle.
  --version-check        Check versions already installed to see if upgradable.
  --debug                use shell debug mode.
  
  -w id, --id id         Use workspace ID <id> for automatic onboarding.
  -s key, --shared key   Use <key> as the shared key for automatic onboarding.
  -d dmn, --domain dmn   Use <dmn> as the OMS domain for onboarding. Optional.
                         default: opinsights.azure.com
                         ex: opinsights.azure.us (for FairFax)
  -p conf, --proxy conf  Use <conf> as the proxy configuration.
                         ex: -p [protocol://][user:password@]proxyhost[:port]
  -a id, --azure-resource id Use Azure Resource ID <id>.
  -m marker, --multi-homing-marker marker
                         Onboard as a multi-homing(Non-Primary) workspace.

  -? | --help            shows this usage text.
```

#### 直接安装和载入
<a id="to-install-and-onboard-directly" class="xliff"></a>
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### 安装并载入到美国政府云中的工作区
<a id="to-install-and-onboard-to-a-workspace-in-us-government-cloud" class="xliff"></a>
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

#### 安装代理包并在以后载入
<a id="to-install-the-agent-packages-and-onboard-at-a-later-time" class="xliff"></a>
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade
```

#### 从捆绑包中提取代理包，但不安装
<a id="to-extract-the-agent-packages-from-the-bundle-without-installing" class="xliff"></a>
```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --extract
```

## 将代理配置为使用 HTTP 代理服务器或 OMS 网关
<a id="configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway" class="xliff"></a>
OMS Agent for Linux 支持通过 HTTP 或 HTTPS 代理服务器或 OMS 网关与 OMS 服务进行通信。  支持匿名身份验证和基本身份验证（用户名/密码）。  

### 代理配置
<a id="proxy-configuration" class="xliff"></a>
代理配置值具有以下语法：

`[protocol://][user:password@]proxyhost[:port]`

属性|说明
-|-
协议|http 或 https
user|用于代理身份验证的可选用户名
password|用于代理身份验证的可选密码
proxyhost|代理服务器/OMS 网关的地址或 FQDN
端口|代理服务器/OMS 网关的可选端口号

例如： `http://user01:password@proxy01.contoso.com:8080`

可在安装期间指定代理服务器，也可在安装后通过修改 proxy.conf 配置文件来指定。   

### 在安装期间指定代理配置
<a id="specify-proxy-configuration-during-installation" class="xliff"></a>
omsagent 安装捆绑包的 `-p` 或 `--proxy` 参数指定要使用的代理配置。 

```
sudo sh ./omsagent-1.3.0-1.universal.x64.sh --upgrade -p http://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### 在文件中定义代理配置
<a id="define-the-proxy-configuration-in-a-file" class="xliff"></a>
可以在 `/etc/opt/microsoft/omsagent/proxy.conf` 文件中设置代理配置。可以直接创建或编辑该文件，但必须对其权限进行更新以授予 omiuser 组对该文件的读取权限。 例如：
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:8080"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### 删除代理配置
<a id="removing-the-proxy-configuration" class="xliff"></a>
若要删除以前定义的代理配置并还原为直接连接，请删除 proxy.conf 文件：
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```
## 允许 OMS Agent for Linux 向 System Center Operations Manager 报告
<a id="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager" class="xliff"></a>
执行以下步骤可将 OMS Agent for Linux 配置为向 System Center Operations Manager 管理组报告。  

1. 编辑 `/etc/opt/omi/conf/omiserver.conf` 文件
2. 确保以 **httpsport=** 开头的行定义了端口 1270。 例如：`httpsport=1270`
3. 重新启动 OMI 服务器：`sudo /opt/omi/bin/service_control restart`

## 使用 Operations Management Suite 载入
<a id="onboarding-with-operations-management-suite" class="xliff"></a>
如果在安装捆绑包时未提供工作区 ID 和密钥，代理随后必须向 Operations Management Suite 注册。

### 使用命令行载入
<a id="onboarding-using-the-command-line" class="xliff"></a>
运行 omsadmin.sh 命令，并为工作区提供工作区 ID 和密钥。 必须以根用户身份（使用 sudo 提升）运行此命令：
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key> [-p <proxy>] [-v]
```
可选的 -v 开关将在载入过程中启用详细日志记录。 所有信息都将显示在执行 shell 脚本的屏幕上。

### 使用文件载入
<a id="onboarding-using-a-file" class="xliff"></a>
1.  创建文件 `/etc/omsagent-onboard.conf`。 该文件必须可供根用户读写。
`sudo vi /etc/omsagent-onboard.conf`
2.  在该文件中插入以下行（包含工作区 ID 和共享密钥）：

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  运行以下命令以载入到 OMS 中：`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  成功载入后将删除该文件

## 管理 omsagent 守护程序
<a id="manage-omsagent-daemon" class="xliff"></a>
从版本 1.3.0-1 开始，我们为每个载入工作区注册 omsagent 守护程序。 守护程序的名称为 *omsagent-\<workspace-id>*。  可以使用 `/opt/microsoft/omsagent/bin/service_control` 命令运行该守护程序。

```
sudo sh /opt/microsoft/omsagent/bin/service_control start|stop|restart|enable|disable [<workspace id>]
```

工作区 ID 是一个可选参数。 如果指定该参数，它将仅在特定于工作区的守护程序上运行。  否则，它将在所有守护程序上运行。


## 代理日志
<a id="agent-logs" class="xliff"></a>
OMS Agent for Linux 的日志位于此处：`/var/opt/microsoft/omsagent/<workspace id>/log/`omsconfig 程序的日志（代理配置）位于此处：`/var/opt/microsoft/omsconfig/log/`OMI 和 SCX 组件的日志（提供性能指标数据）位于此处：`/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### 日志轮换配置##
<a id="log-rotation-configuration" class="xliff"></a>
omsagent 的日志轮换配置位于此处：`/etc/logrotate.d/omsagent-<workspace id>`

默认设置为： 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## 卸载 OMS Agent for Linux
<a id="uninstalling-the-oms-agent-for-linux" class="xliff"></a>
可以使用 dpkg 或 rpm 或者通过使用 `--remove` 参数运行捆绑包 .sh 文件，来卸载代理包。  此外，如果想要完全删除 OMS Agent for Linux 的所有元素，可以使用 `--purge` 参数运行捆绑包 .sh 文件。 

### Debian 和 Ubuntu
<a id="debian--ubuntu" class="xliff"></a>
```
> sudo dpkg -P omsconfig
> sudo dpkg -P omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

### CentOS、Oracle Linux、RHEL 和 SLES
<a id="centos-oracle-linux-rhel-and-sles" class="xliff"></a>
```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```
## 故障排除
<a id="troubleshooting" class="xliff"></a>

### 问题：无法通过代理连接到 OMS
<a id="issue-unable-to-connect-through-proxy-to-oms" class="xliff"></a>

#### 可能的原因
<a id="probable-causes" class="xliff"></a>
* 在载入期间指定的代理不正确
* OMS 服务终结点不在数据中心的白名单中 

#### 解决方法
<a id="resolutions" class="xliff"></a>
1. 使用以下命令（启用了 `-v` 选项）通过 OMS Agent for Linux 重新载入到 OMS 服务中。 这允许通过代理服务器连接到 OMS 服务的代理能够进行详细输出。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. 请查看[将代理配置为使用 HTTP 代理服务器(#configuring the-agent-for-use-with-a-http-proxy-server)部分，验证是否已将代理正确配置为通过代理服务器进行通信。    
* 仔细检查下列 OMS 服务终结点是否在白名单中：

    |代理资源| 端口 |  
    |------|---------|  
    |*.ods.opinsights.azure.com | 端口 443|   
    |*.oms.opinsights.azure.com | 端口 443|   
    |ods.systemcenteradvisor.com | 端口 443|   
    |*.blob.core.windows.net/ | 端口 443|   

### 问题：尝试载入时收到 403 错误
<a id="issue-you-receive-a-403-error-when-trying-to-onboard" class="xliff"></a>

#### 可能的原因
<a id="probable-causes" class="xliff"></a>
* Linux 服务器上的日期和时间不正确 
* 使用的工作区 ID 和工作区密钥不正确

#### 解决方法
<a id="resolution" class="xliff"></a>

1. 使用 date 命令检查 Linux 服务器上的时间。 如果时间比当前时间快/慢 15 分钟，则载入失败。 若要纠正此问题，请更新 Linux 服务器的日期和/或时区。 
2. 验证你已安装了最新版本的 OMS Agent for Linux。  如果时间偏差导致了载入故障，最新版本现在会发出通知。
3. 请按照本主题上文中的安装说明使用正确的工作区 ID 和工作区密钥重新载入。

### 问题：载入后，日志文件中立即显示 500 和 404 错误
<a id="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding" class="xliff"></a>
这是第一次将 Linux 数据上传到 OMS 工作区时发生的已知问题。 这不会影响发送的数据或服务体验。

### 问题：OMS 门户中未显示任何数据
<a id="issue--you-are-not-seeing-any-data-in-the-oms-portal" class="xliff"></a>

#### 可能的原因
<a id="probable-causes" class="xliff"></a>

- 载入到 OMS 服务失败
- 与 OMS 服务的连接受阻
- 备份了 OMS Agent for Linux 数据

#### 解决方法
<a id="resolutions" class="xliff"></a>
1. 通过检查是否存在以下文件，来检查是否已成功载入 OMS 服务：`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. 使用 `omsadmin.sh` 命令行指令重新载入
3. 如果使用代理，请参阅之前提供的代理解决方法步骤。
4. 在某些情况下，当 OMS Agent for Linux 无法与 OMS 服务通信时，代理上的数据会在整个缓冲区（大小 50 MB）中排队。 OMS Agent for Linux 应通过运行以下命令重新启动：`/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`。 
> [!NOTE]
> 此问题已在代理版本 1.1.0-28 及更高版本中解决。


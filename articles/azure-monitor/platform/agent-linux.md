---
title: 在 Linux 计算机上安装 Log Analytics 代理
description: 本文介绍如何使用适用于 Linux 的 Log Analytics 代理将在其他云中或本地托管的 Linux 计算机连接到 Azure Monitor。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 4414dc86ff318cfff5d224ce7aa064c31f3df460
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91294522"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>在 Linux 计算机上安装 Log Analytics 代理
本文详细介绍如何使用以下方法在 Linux 计算机上安装 Log Analytics 代理：

* [使用 GitHub 上托管的包装器脚本安装 Linux 代理](#install-the-agent-using-wrapper-script)。 如果计算机已直接或通过代理服务器连接到 Internet，则建议使用此方法安装和升级代理。
* [手动下载并安装](#install-the-agent-manually)代理。 如果 Linux 计算机无法访问 Internet，并通过 [Log Analytics 网关](gateway.md)与 Azure Monitor 或 Azure 自动化进行通信，则需要执行此步骤。 

>[!IMPORTANT]
> 本文中所述的安装方法通常用于本地或其他云中的虚拟机。 有关可用于 Azure 虚拟机的更高效的选项，请参阅 [安装选项](log-analytics-agent.md#installation-options) 。



## <a name="supported-operating-systems"></a>支持的操作系统

有关 Log Analytics 代理支持的 Linux 发行版列表，请参阅 [Azure Monitor 代理概述](agents-overview.md#supported-operating-systems) 。

>[!NOTE]
>仅 x86_x64 平台（64 位）支持 OpenSSL 1.1.0，任何平台均不支持早于 1.x 版本的 OpenSSL。
>
从 2018 年 8 月之后发布的版本开始，我们对支持模型进行了以下更改：  

* 仅支持服务器版本，不支持客户端版本。  
* 将支持重点放在任何 [Azure Linux 认可的发行版](../../virtual-machines/linux/endorsed-distros.md)。 请注意，新的发行版/版本被 Azure Linux 认可和其受 Log Analytics Linux 代理支持，这两者之间可能存在一些延迟。
* 列出的每个主版本支持所有的次版本。
* 超出制造商终止支持日期的版本不受支持。  
* 不支持新版本的 AMI。  
* 默认仅支持运行 SSL 1.x 的版本。

>[!NOTE]
>如果使用的是当前不受支持且与我们的支持模型不一致的发行版或版本，我们建议对此存储库创建分支，并接受 Microsoft 支持不会为已分支的代理版本提供帮助。

### <a name="python-2-requirement"></a>Python 2 要求

 Log Analytics 代理需要 Python 2。 如果虚拟机使用的发行版默认情况下不包括 Python 2，则必须进行安装。 以下示例命令将在不同的发行版上安装 Python 2。

 - Red Hat、CentOS、Oracle：`yum install -y python2`
 - Ubuntu、Debian：`apt-get install -y python2`
 - SUSE: `zypper install -y python2`

Python2 可执行文件必须化名为 *python*。 下面是可以用来设置此别名的一种方法：

1. 运行以下命令以删除所有现有别名。
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 运行以下命令以创建别名。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>支持的 Linux 强化
OMS 代理对 Linux 提供了有限的自定义支持。 

当前支持以下内容： 
- FIPS

以下内容已计划，但尚不受支持：
- CIS
- SELINUX

OMS 代理不支持且未计划使用其他强化和自定义方法。  

## <a name="agent-prerequisites"></a>代理必备组件

下表突出显示了将在其中安装代理的 [受支持的 Linux 发行版](#supported-operating-systems) 所需的包。

|所需程序包 |说明 |最低版本 |
|-----------------|------------|----------------|
|Glibc |    GNU C 库 | 2.5-12 
|Openssl    | OpenSSL 库 | 1.0.x 或 1.1.x |
|Curl | cURL Web 客户端 | 7.15.5 |
|Python | | 2.6+ 或 3.3+
|Python-ctype | | 
|PAM | 可插入验证模块 | | 

>[!NOTE]
>收集 Syslog 消息时需要 rsyslog 或 syslog ng。 不支持将 Red Hat Enterprise Linux 版本 5、CentOS 和 Oracle Linux 版本 (sysklog) 上的默认 syslog 守护程序用于 syslog 事件收集。 要从这些发行版的此版本中收集 syslog 数据，应安装并配置 rsyslog 守护程序以替换 sysklog。

## <a name="network-requirements"></a>网络要求
请参阅 [Log Analytics 代理概述](log-analytics-agent.md#network-requirements) ，了解 Linux 代理的网络要求。

## <a name="agent-install-package"></a>代理安装包

适用于 Linux 的 Log Analytics 代理由多个包组成。 发行文件包含以下包，可通过结合 `--extract` 参数运行 shell 捆绑包来获取这些包：

**包** | **版本** | **说明**
----------- | ----------- | --------------
omsagent | 1.13.9 | 适用于 Linux 的 Log Analytics 代理
omsconfig | 1.1.1 | Log Analytics 代理的配置代理
omi | 1.6.4 | Open Management Infrastructure (OMI) - 一款轻型 CIM 服务器。 请注意，OMI 要求拥有 root 访问权限，以运行所需的 cron 作业来使服务正常工作
scx | 1.6.4 | 操作系统性能指标的 OMI CIM 提供程序
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 服务器性能监视提供程序。 仅当检测到 Apache HTTP 服务器时才安装。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 服务器性能监视提供程序。 仅当检测到 MySQL/MariaDB 服务器时才安装。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供程序。 仅当检测到 Docker 时才安装。

### <a name="agent-installation-details"></a>代理安装详细信息

安装适用于 Linux 的 Log Analytics 代理包后，将应用下述额外的系统范围的配置更改。 卸载 omsagent 程序包时会删除这些项目。

* 创建一个名为 `omsagent` 的非特权用户。 守护程序通过此凭据运行。 
* 将在 `/etc/sudoers.d/omsagent` 中创建一个 sudoers *include* 文件。 这会授权 `omsagent` 重启 syslog 和 omsagent 守护程序。 如果安装的 sudo 版本不支持 sudo *include* 指令，则会将这些条目写入 `/etc/sudoers`。
* 修改 syslog 配置，以将事件子集转发到代理。 有关详细信息，请参阅[配置 Syslog 数据收集](data-sources-syslog.md)。

在受监视的 Linux 计算机上，代理列为 `omsagent`。 `omsconfig` 是每隔 5 分钟便会查找一次新门户端配置的 Log Analytics Linux 代理的配置代理。 新的和已更新的配置应用到 `/etc/opt/microsoft/omsagent/conf/omsagent.conf` 中的代理配置文件。

## <a name="install-the-agent-using-wrapper-script"></a>使用包装器脚本来安装代理

以下步骤使用适用于 Linux 计算机（可直接或通过代理服务器进行通信以下载在 GitHub 上托管的代理并安装代理）的包装脚本，为 Azure 和 Azure 政府云中的 Log Analytics 配置代理的安装程序。  

如果 Linux 计算机需要通过代理服务器与 Log Analytics 通信，可以通过包含 `-p [protocol://][user:password@]proxyhost[:port]` 在命令行中指定此配置。 *protocol* 属性接受 `http` 或 `https`，*proxyhost* 属性接受代理服务器的完全限定域名或 IP 地址。 

例如： `https://proxy01.contoso.com:30443`

如果在任一情况下需要身份验证，需要指定用户名和密码。 例如： `https://user01:password@proxy01.contoso.com:30443`

1. 若要配置 Linux 计算机以连接至 Log Analytics 工作区，请运行以下命令，并提供工作区 ID 和主密钥。 以下命令将下载代理、验证其校验和并将其安装好。
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    代理服务器要求进行身份验证时，以下命令包括 `-p` 代理参数和示例语法：

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. 若要配置 Linux 计算机以连接至 Azure 政府云中的 Log Analytics 工作区，请运行以下命令，并提供先前所复制的工作区 ID 和主密钥。 以下命令将下载代理、验证其校验和并将其安装好。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    代理服务器要求进行身份验证时，以下命令包括 `-p` 代理参数和示例语法：

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 运行以下命令重启代理： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>手动安装代理

适用于 Linux 的 Log Analytics 代理以自提取和可安装的 shell 脚本捆绑包形式提供。 此捆绑包包含用于各个代理组件的 Debian 和 RPM 程序包，可以直接安装该捆绑包，也可以通过提取它来检索单个程序包。 分别针对 x64 体系结构和 x86 体系结构提供一个捆绑包。 

> [!NOTE]
> 对于 Azure VM，建议你使用适用于 Linux 的 [Azure Log Analytics VM 扩展](../../virtual-machines/extensions/oms-linux.md)在这些 VM 上安装代理。 

1. [下载](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide)相应的捆绑包（x64 或 x86）并使用 SCP/SFTP 将其传输到 Linux VM 或物理计算机。

2. 使用 `--install` 参数安装捆绑包。 若要在安装过程中加入到 Log Analytics 工作区，请提供前面复制的 `-w <WorkspaceID>` 和 `-s <workspaceKey>` 参数。

    >[!NOTE]
    >如果安装 omi、scx、omsconfig 等依赖包或其旧版本，则需要使用 `--upgrade` 参数（已安装适用于 Linux 的 System Center Operations Manager 代理时存在这种情况）。 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. 若要将 Linux 代理配置为通过 Log Analytics 网关安装并连接到 Log Analytics 工作区，请运行以下命令并提供代理、工作区 ID 和工作区密钥参数。 可以通过包含 `-p [protocol://][user:password@]proxyhost[:port]` 在命令行中指定此配置。 *proxyhost* 属性接受 Log Analytics 网关服务器的完全限定域名或 IP 地址。  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    如果需要身份验证，则需要指定用户名和密码。 例如： 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. 若要将 Linux 计算机配置为连接到 Azure 政府云中的 Log Analytics 工作区，请运行以下命令，以提供前面复制的工作区 ID 和主密钥。

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

若要在以后安装代理包并将其配置为向特定的 Log Analytics 工作区报告，请运行以下命令：

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

若要从捆绑中提取代理包而不安装代理，请运行以下命令：

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>从以前的版本升级

从版本 1.0.0-47 开始，每个版本都支持从旧版升级。 使用 `--upgrade` 参数执行安装可将代理的所有组件升级到最新版本。

## <a name="cache-information"></a>缓存信息
在本地计算机上，适用于 Linux 的 Log Analytics 代理中的数据缓存在 *% STATE_DIR_WS/out_oms_common*缓冲 * 发送到 Azure Monitor 之前。 自定义日志数据以 *% STATE_DIR_WS/out_oms_blob*缓冲 * 缓冲。 某些 [解决方案和数据类型](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)的路径可能不同。

代理每隔20秒尝试上传一次。 如果该操作失败，它将等待呈指数级增加的时间，直到成功为止。 它将在第二次尝试之前等待30秒，在下120一次重试之前的60秒，在两次重试之间等待大约9分钟，直到再次成功连接。 代理在放弃并移到下一条数据块之前，只会重试10次。 此过程将一直继续，直到代理成功上传。 表示数据在被丢弃之前最多可以缓冲8.5 小时。

默认缓存大小为 10 MB，但可在 [omsagent 文件](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)中修改。


## <a name="next-steps"></a>后续步骤

- 查看[管理和维护 Windows 和 Linux 的 Log Analytics 代理](agent-manage.md)以了解如何重新配置、升级代理或从虚拟机中删除代理。

- 如果在安装或管理代理时遇到问题，请查看 [Linux 代理疑难解答](agent-linux-troubleshoot.md)。

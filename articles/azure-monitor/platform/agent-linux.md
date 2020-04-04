---
title: 将 Linux 计算机连接到 Azure 监视器 |微软文档
description: 本文介绍如何使用 Linux 的日志分析代理将托管在其他云中或本地的 Linux 计算机连接到 Azure 监视器。
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 9807d6eeb07b953ab75b328ce64c5166ca52dd2a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637523"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>将 Linux 计算机连接到 Azure 监视器

为了使用 Azure 监视器监视和管理本地数据中心或其他云环境中的虚拟机或其他云环境中的物理计算机，您需要部署日志分析代理并将其配置为向日志分析工作区报告。 该代理还支持用于 Azure 自动化的混合 Runbook 辅助角色。

可以使用以下方法之一安装 Linux 的日志分析代理。 有关使用每种方法的详细信息在本文中后面提供。

* [手动下载并安装](#install-the-agent-manually)代理。 当 Linux 计算机无法访问 Internet 并通过[日志分析网关](gateway.md)与 Azure 监视器或 Azure 自动化通信时，这是必需的。 
* 使用 GitHub 上托管[的包装脚本安装 Linux 的代理](#install-the-agent-using-wrapper-script)。 当计算机直接或通过代理服务器与 Internet 连接时，这是安装和升级代理的推荐方法。

若要了解支持的配置，请查看[支持的 Linux 操作系统](log-analytics-agent.md#supported-linux-operating-systems)和[网络防火墙配置](log-analytics-agent.md#network-requirements)。

>[!NOTE]
>无法将适用于 Linux 的 Log Analytics 代理配置为向多个 Log Analytics 工作区报告。 只能将其配置为同时向系统中心操作管理器管理组和日志分析工作区报告，也可以单独报告。

## <a name="agent-install-package"></a>代理安装包

Linux 的日志分析代理由多个包组成。 发布文件包含以下包，这些包可通过使用`--extract`参数运行 shell 捆绑包来获取：

**包** | **版本** | **说明**
----------- | ----------- | --------------
omsagent | 1.12.15 | Linux 的日志分析代理
omsconfig | 1.1.1 | 日志分析代理的配置代理
omi | 1.6.3 | 开放式管理基础架构 （OMI） -- 轻量级 CIM 服务器。 *请注意，OMI 需要根访问才能运行服务正常运行所需的 cron 作业*
scx | 1.6.3 | 操作系统性能指标的 OMI CIM 提供程序
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 服务器性能监视提供程序。 仅当检测到 Apache HTTP 服务器时才安装。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 服务器性能监视提供程序。 仅当检测到 MySQL/MariaDB 服务器时才安装。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供程序。 仅当检测到 Docker 时才安装。

### <a name="agent-installation-details"></a>代理安装详细信息

安装 Linux 包的日志分析代理后，将应用以下额外的系统范围配置更改。 卸载 omsagent 程序包时会删除这些项目。

* 创建一个名为 `omsagent` 的非特权用户。 守护进程在此凭据下运行。 
* 在 中`/etc/sudoers.d/omsagent`创建*包含文件的*sudoers 这将`omsagent`授权重新启动系统日志和 omsagent 守护进程。 如果 sudo*包含*指令在 sudo 的已安装版本中不受支持，则这些条目`/etc/sudoers`将写入 。
* 修改 syslog 配置，以将事件子集转发到代理。 有关详细信息，请参阅配置[Syslog 数据收集](data-sources-syslog.md)。

在受监视的 Linux 计算机上，代理被列为`omsagent`。 `omsconfig`是 Linux 配置代理的日志分析代理，每 5 分钟查找一次新的门户端配置。 新的和更新的配置应用于位于 的`/etc/opt/microsoft/omsagent/conf/omsagent.conf`代理配置文件。

## <a name="obtain-workspace-id-and-key"></a>获取工作区 ID 和密钥

在安装适用于 Linux 的 Log Analytics 代理前，需要先获得 Log Analytics 工作区的工作区 ID 和秘钥。 在代理设置期间需要此信息来正确配置它并确保它可以与 Azure 监视器成功通信。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. 在 Azure 门户左上角选择“所有服务”****。 在搜索框中输入 **Log Analytics**。 键入时，列表会根据输入的内容进行筛选。 选择“Log Analytics 工作区”****。

2. 在 Log Analytics 工作区列表中，选择前面创建的工作区。 （可能已将其命名为 **DefaultLAWorkspace**。）

3. 选择**高级设置**：

    ![Azure 门户中 Log Analytics 的“高级设置”菜单](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. 选择“已连接的源”，然后选择“Linux 服务器”********。

5. “工作区 ID”和“主密钥”右侧的值********。 将它们复制并粘贴到喜爱的编辑器中。

## <a name="install-the-agent-manually"></a>手动安装代理

Linux 的日志分析代理在自提取和可安装的 shell 脚本捆绑包中提供。 此捆绑包包含用于各个代理组件的 Debian 和 RPM 程序包，可以直接安装该捆绑包，也可以通过提取它来检索单个程序包。 为 x64 提供了一个捆绑包，为 x86 体系结构提供了一个捆绑包。 

> [!NOTE]
> 对于 Azure VM，我们建议您使用 Linux 的 Azure[日志分析 VM 扩展](../../virtual-machines/extensions/oms-linux.md)在它们上安装代理。 

1. 使用 scp/sftp 将适当的捆绑包 （x64 或 x86）[下载](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide)并传输到 Linux VM 或物理计算机。

2. 使用`--install`参数安装捆绑包。 要在安装期间将日志分析工作区装上板，请`-w <WorkspaceID>`提供`-s <workspaceKey>`前面复制的 和 参数。

    >[!NOTE]
    >如果安装了 omi、scx、omsconfig 或其旧版本等从属包，则需要使用`--upgrade`参数，如果已安装 Linux 的系统中心操作管理器代理，则无需使用参数。 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. 要将 Linux 代理配置为通过日志分析网关安装和连接到日志分析工作区，请运行以下命令，提供代理、工作区 ID 和工作区密钥参数。 可以通过 包括`-p [protocol://][user:password@]proxyhost[:port]`在命令行上指定此配置。 *代理主机*属性接受日志分析网关服务器的完全限定的域名或 IP 地址。  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    如果需要身份验证，则需要指定用户名和密码。 例如： 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. 要将 Linux 计算机配置为连接到 Azure 政府云中的日志分析工作区，请运行以下命令，提供前面复制的工作区 ID 和主密钥。

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

如果要安装代理包并将其配置为稍后向特定日志分析工作区报告，请运行以下命令：

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

如果要在不安装代理的情况下从捆绑包中提取代理包，则运行以下命令：

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>使用包装脚本安装代理

以下步骤使用 Linux 计算机的包装脚本配置 Azure 和 Azure 政府云中日志分析代理的设置，这些脚本可以直接或通过代理服务器进行通信，以下载在 GitHub 上托管的代理并安装代理。  

如果 Linux 计算机需要通过代理服务器与日志分析进行通信，则可以通过 包括`-p [protocol://][user:password@]proxyhost[:port]`在命令行上指定此配置。 *协议*属性接受`http`或`https`，*代理主机*属性接受代理服务器完全限定的域名或 IP 地址。 

例如： `https://proxy01.contoso.com:30443`

如果在这两种情况下都需要身份验证，则需要指定用户名和密码。 例如： `https://user01:password@proxy01.contoso.com:30443`

1. 要将 Linux 计算机配置为连接到日志分析工作区，请运行以下命令，提供工作区 ID 和主键。 以下命令将下载代理、验证其校验和并将其安装好。
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    当代理服务器要求进行身份验证时，以下命令包括 `-p` 代理参数和示例语法：

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. 若要配置 Linux 计算机以连接至 Azure 政府云中的 Log Analytics 工作区，请运行以下命令，并提供先前所复制的工作区 ID 和主密钥。 以下命令将下载代理、验证其校验和并将其安装好。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    当代理服务器要求进行身份验证时，以下命令包括 `-p` 代理参数和示例语法：

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 运行以下命令重启代理： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>从以前的版本升级

每个版本都支持从以前的版本（从版本 1.0.0-47 开始）进行升级。 使用`--upgrade`参数执行安装，将代理的所有组件升级到最新版本。

## <a name="next-steps"></a>后续步骤

- 查看[管理和维护 Windows 和 Linux 的 Log Analytics 代理](agent-manage.md)以了解如何重新配置、升级代理或从虚拟机中删除代理。

- 如果在安装或管理代理时遇到问题，请查看 [Linux 代理疑难解答](agent-linux-troubleshoot.md)。

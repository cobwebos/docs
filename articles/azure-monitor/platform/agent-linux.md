---
title: 将 Linux 计算机连接到 Azure Monitor | Microsoft Docs
description: 本文介绍如何使用适用于 Linux 的 Log Analytics 代理将在其他云中或本地托管的 Linux 计算机连接到 Azure Monitor。
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 965d5dd558d0da7a758db77330c9129ea0e8247c
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543854"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>将 Linux 计算机连接到 Azure Monitor

若要使用 Azure Monitor 在本地数据中心或其他云环境中监视和管理虚拟机或物理计算机，需部署 Log Analytics 代理，并将其配置为向 Log Analytics 工作区报告。 该代理还支持用于 Azure 自动化的混合 Runbook 辅助角色。

可使用以下方法之一安装适用于 Linux 的 Log Analytics 代理。 有关使用每种方法的详细信息在本文中后面提供。

* [手动下载并安装](#install-the-agent-manually)代理。 如果 Linux 计算机无法访问 Internet，并通过 [Log Analytics 网关](gateway.md)与 Azure Monitor 或 Azure 自动化进行通信，则需要执行此步骤。 
* [使用 GitHub 上托管的包装器脚本安装 Linux 代理](#install-the-agent-using-wrapper-script)。 如果计算机已直接或通过代理服务器连接到 Internet，则建议使用此方法安装和升级代理。

若要了解支持的配置，请查看[支持的 Linux 操作系统](log-analytics-agent.md#supported-linux-operating-systems)和[网络防火墙配置](log-analytics-agent.md#network-requirements)。

>[!NOTE]
>无法将适用于 Linux 的 Log Analytics 代理配置为向多个 Log Analytics 工作区报告。 只能将此代理配置为同时向 System Center Operations Manager 管理组和 Log Analytics 工作区报告，或者向两者之一报告。

## <a name="agent-install-package"></a>代理安装包

适用于 Linux 的 Log Analytics 代理由多个包组成。 发行文件包含以下包，可通过结合 `--extract` 参数运行 shell 捆绑包来获取这些包：

**包** | **版本** | **说明**
----------- | ----------- | --------------
omsagent | 1.12.15 | 适用于 Linux 的 Log Analytics 代理
omsconfig | 1.1.1 | Log Analytics 代理的配置代理
omi | 1.6.3 | Open Management Infrastructure (OMI) - 一款轻型 CIM 服务器。 请注意，OMI 要求拥有 root 访问权限，以运行所需的 cron 作业来使服务正常工作
scx | 1.6.3 | 操作系统性能指标的 OMI CIM 提供程序
apache-cimprov | 1.0.1 | OMI 的 Apache HTTP 服务器性能监视提供程序。 仅当检测到 Apache HTTP 服务器时才安装。
mysql-cimprov | 1.0.1 | OMI 的 MySQL 服务器性能监视提供程序。 仅当检测到 MySQL/MariaDB 服务器时才安装。
docker-cimprov | 1.0.0 | OMI 的 Docker 提供程序。 仅当检测到 Docker 时才安装。

### <a name="agent-installation-details"></a>代理安装详细信息

安装适用于 Linux 的 Log Analytics 代理包后，将应用下述额外的系统范围的配置更改。 卸载 omsagent 程序包时会删除这些项目。

* 创建一个名为 `omsagent` 的非特权用户。 守护程序通过此凭据运行。 
* 将在 `/etc/sudoers.d/omsagent` 中创建一个 sudoers *include* 文件。 这会授权 `omsagent` 重启 syslog 和 omsagent 守护程序。 如果安装的 sudo 版本不支持 sudo *include* 指令，则会将这些条目写入 `/etc/sudoers`。
* 修改 syslog 配置，以将事件子集转发到代理。 有关详细信息，请参阅[配置 Syslog 数据收集](data-sources-syslog.md)。

在受监视的 Linux 计算机上，代理列为 `omsagent`。 `omsconfig` 是每隔 5 分钟便会查找一次新门户端配置的 Log Analytics Linux 代理的配置代理。 新的和已更新的配置应用到 `/etc/opt/microsoft/omsagent/conf/omsagent.conf` 中的代理配置文件。

## <a name="obtain-workspace-id-and-key"></a>获取工作区 ID 和密钥

在安装适用于 Linux 的 Log Analytics 代理前，需要先获得 Log Analytics 工作区的工作区 ID 和秘钥。 在安装代理期间，需要使用此信息来正确配置代理，确保它能与 Azure Monitor 成功通信。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. 在 Azure 门户左上角选择“所有服务”。 在搜索框中输入 **Log Analytics**。 键入时，列表会根据输入的内容进行筛选。 选择“Log Analytics 工作区”。

2. 在 Log Analytics 工作区列表中，选择前面创建的工作区。 （可能已将其命名为 **DefaultLAWorkspace**。）

3. 选择“高级设置”：

    ![Azure 门户中 Log Analytics 的“高级设置”菜单](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. 选择“已连接的源”，然后选择“Linux 服务器” 。

5. “工作区 ID”和“主密钥”右侧的值 。 将它们复制并粘贴到喜爱的编辑器中。

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

## <a name="supported-linux-hardening"></a>支持的 Linux 强化
OMS 代理对 Linux 提供了有限的自定义支持。 

目前支持以下各项： 
- FIPs

以下项已计划，但尚不受支持：
- CIS
- SELINUX

其他强化和自定义方法不受支持，也不会计划用于 OMS 代理。  


## <a name="upgrade-from-a-previous-release"></a>从以前的版本升级

从版本 1.0.0-47 开始，每个版本都支持从旧版升级。 使用 `--upgrade` 参数执行安装可将代理的所有组件升级到最新版本。

## <a name="next-steps"></a>后续步骤

- 查看[管理和维护 Windows 和 Linux 的 Log Analytics 代理](agent-manage.md)以了解如何重新配置、升级代理或从虚拟机中删除代理。

- 如果在安装或管理代理时遇到问题，请查看 [Linux 代理疑难解答](agent-linux-troubleshoot.md)。

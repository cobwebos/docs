---
title: "Azure Log Analytics 中的容器解决方案 | Microsoft 文档"
description: "Log Analytics 中的容器解决方案可帮助用户在单个位置查看和管理 Docker 和 Windows 容器主机。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 05dfdc3491e6c7f838f5e7e2c16951bc1328e32b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017


---
# <a name="containers-preview-solution-log-analytics"></a>容器（预览版）解决方案 Log Analytics
本文介绍如何设置和使用 Log Analytics 中的容器解决方案，它可以帮助用户在单个位置查看和管理 Docker 和 Windows 容器主机。 Docker 是一种软件虚拟化系统，用于创建自动将软件部署到其 IT 基础结构的容器。

使用该解决方案，你可以查看容器主机上正在运行的容器以及容器中运行的映像。 你可以查看详细审核信息，它显示了与容器一起使用的命令。 并且，用户可以通过查看和搜索集中式日志来排查容器问题，而无需远程查看 Docker 或 Windows 主机。 你可以在主机上找到可能具有干扰性并且占用过多资源的容器。 并且，你可以查看容器的集中式 CPU、内存、存储器、网络使用情况和性能信息。 在运行 Windows 的计算机上，可以集中比较 Windows Server、Hyper-V 和 Docker 容器中的日志。

下图显示了 OMS 中各种容器主机和代理之间的关系。

![容器关系图](./media/log-analytics-containers/containers-diagram.png)

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案
使用以下信息安装和配置解决方案。

从 [Azure 应用商店](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview)或使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将容器解决方案添加到 OMS 工作区。

有多种 OMS 的使用方法可用来安装和使用 Docker：

* 在支持的 Linux 操作系统上，安装并运行 Docker，然后安装和配置 OMS Agent for Linux。
* 在 CoreOS 上，无法运行 OMS Agent for Linux。 取而代之，你将运行 OMS Agent for Linux 的容器化版本。
* 在 Windows Server 2016 和 Windows 10 上安装 Docker 引擎和客户端，然后连接到代理以收集信息并将其发送到 Log Analytics。


可以在 [GitHub](https://github.com/Microsoft/OMS-docker) 上查看容器主机支持的 Docker 和 Linux 操作系统版本。

- 如果你的某个 Kubernetes 群集 使用 Azure 容器服务，请前往[使用 Microsoft Operations Management Suite (OMS) 监视 Azure 容器服务群集](../container-service/container-service-kubernetes-oms.md)了解详细信息。
- 如果你拥有 Azure 容器服务 DC/OS 群集，请前往[通过 Operations Management Suite 监视 Azure 容器服务 DC/OS 群集](../container-service/container-service-monitoring-oms.md)了解详细信息。
- 如果在 Service Fabric 中使用容器，请在 [Azure Service Fabric 概述](../service-fabric/service-fabric-overview.md)中了解更多信息。
- 请参阅 [Windows 上的 Docker 引擎](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)一文，详细了解如何在运行 Windows 的计算机上安装和配置 Docker 引擎。

> [!IMPORTANT]
> 在你在容器主机上安装 [OMS Agent for Linux](log-analytics-linux-agents.md) **之前**，你的主机上必须运行 Docker。 如果你在安装 Docker 之前已经安装了代理，则需要重新安装 OMS Agent for Linux。 有关 Docker 的详细信息，请参阅 [Docker 网站](https://www.docker.com)。
>
>

你需要在容器主机上配置以下设置，然后才能监视容器。

## <a name="configure-settings-for-a-linux-container-host"></a>配置 Linux 容器主机的设置

支持的 Linux 版本：

- Docker 1.11 到 Docker 1.13
- Docker CE 和 EE v17.03


支持将以下 x64 Linux 分发用作容器主机：

- Ubuntu 14.04 LTS、16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2、7.3
- SLES 12
- RHEL 7.2、7.3


安装 Docker 之后，请使用以下容器主机设置来配置代理以供 Docker 使用。 你需要 [OMS 工作区 ID 和密钥](log-analytics-linux-agents.md)。


### <a name="for-all-linux-container-hosts-except-coreos"></a>对于除了 CoreOS 之外的所有 Linux 容器主机

- 按照[安装 OMS Agent for Linux 的步骤](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)中的说明进行操作。

### <a name="for-all-linux-container-hosts-including-coreos"></a>对于包括 CoreOS 在内的所有 Linux 容器主机

启动想要监视的 OMS 容器。 修改并使用以下示例。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>从使用已安装的 Linux 代理切换为使用容器中的 Linux 代理
如果你以前使用直接安装的代理，并且想要改为使用容器中运行的代理，则必须首先删除 OMSAgent。 请参阅 [OMS Agent for Linux 的安装步骤](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)。

## <a name="supported-windows-versions"></a>支持的 Windows 版本

- Windows Server 2016
- Windows 10 周年版（专业版或企业版）

### <a name="docker-versions-supported-on-windows"></a>Windows 上支持的 Docker 版本

- Docker 1.12 – 1.13
- Docker 17.03.0 [稳定]

### <a name="preparation-before-installing-agents"></a>安装代理之前的准备

在运行 Windows 的计算机上安装代理之前，需配置 Docker 服务。 配置允许 Windows 代理或 Log Analytics 虚拟机扩展使用 Docker TCP 套接字，因此代理能够远程访问 Docker 守护程序并捕获用于监视的数据。

#### <a name="to-start-docker-and-verify-its-configuration"></a>启动 Docker 并验证其配置

为 Windows Server 设置 TCP 命名管道需要执行以下步骤：

1. 在 Windows PowerShell 中，启用 TCP 管道和命名的管道。

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. 使用 TCP 管道和命名管道的配置文件配置 Docker。 该配置文件位于 C:\ProgramData\docker\config\daemon.json。

    在 daemon.json 文件中，需要以下项：

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

若要详细了解用于 Windows 容器的 Docker 守护程序配置，请参阅 [Windows 上的 Docker 引擎](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)。


### <a name="install-windows-agents"></a>安装 Windows 代理

若要启用 Windows 和 Hyper-V 容器监视，请在属于容器主机的 Windows 计算机上安装代理。 若要了解在本地环境中运行 Windows 的计算机，请参阅[将 Windows 计算机连接到 Log Analytics](log-analytics-windows-agents.md)。 为使虚拟机在 Azure 中运行，请使用[虚拟机扩展](log-analytics-azure-vm-extension.md)将其连接到 Log Analytics。

可以监视在 Service Fabric 上运行的 Windows 容器。 但是，目前 Service Fabric 仅支持[在 Azure 中运行的虚拟机](log-analytics-azure-vm-extension.md)和[在本地环境中运行 Windows 的计算机](log-analytics-windows-agents.md)。

若要验证是否已正确设置容器解决方案，请执行以下步骤：

- 检查管理包是否已正确下载，查找 *ContainerManagement.xxx*。
    - 文件应位于 C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs 文件夹中。
- 转到“控制面板” > “系统和安全”，验证 OMS 工作区 ID 是否正确。
    - 打开 **Microsoft Monitoring Agent**，验证工作区信息是否正确。


## <a name="containers-data-collection-details"></a>容器数据收集详细信息
容器解决方案使用用户启用的代理从容器主机和容器中收集各种性能指标和日志数据。

下表显示了数据收集方法以及有关如何为容器收集数据的其他详细信息。

| 平台 | [适用于 Linux 的 OMS 代理](log-analytics-linux-agents.md) | SCOM 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![是](./media/log-analytics-containers/oms-bullet-green.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |每隔 3 分钟 |

| 平台 | [Windows 代理](log-analytics-windows-agents.md) | SCOM 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![是](./media/log-analytics-containers/oms-bullet-green.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |每隔 3 分钟 |

| 平台 | [Log Analytics VM 扩展](log-analytics-azure-vm-extension.md) | SCOM 代理 | Azure 存储 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |![是](./media/log-analytics-containers/oms-bullet-green.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |![否](./media/log-analytics-containers/oms-bullet-red.png) |每隔 3 分钟 |

下表显示了容器解决方案收集的数据类型以及日志搜索和结果中使用的数据类型的示例。

| 数据类型 | 日志搜索中的数据类型 | 字段 |
| --- | --- | --- |
| 主机和容器的性能 | `Type=Perf` | 计算机、ObjectName、CounterName、处理器时间百分比、磁盘读取 MB、磁盘写入 MB、内存使用 MB、网络接收字节数、网络发送字节数、处理器使用秒数、网络、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| 容器库存 | `Type=ContainerInventory` | TimeGenerated、计算机、容器名称、ContainerHostname、映像、ImageTag、ContinerState、ExitCode、EnvironmentVar、命令、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| 容器映像库存 | `Type=ContainerImageInventory` | TimeGenerated、计算机、映像、ImageTag、ImageSize、VirtualSize、正在运行、暂停、停止、失败、SourceSystem、ImageID、TotalContainer |
| 容器日志 | `Type=ContainerLog` | TimeGenerated、计算机、映像 ID、容器名称、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| 容器服务日志 | `Type=ContainerServiceLog`  | TimeGenerated、计算机、TimeOfCommand、映像、命令、SourceSystem、ContainerID |

## <a name="monitor-containers"></a>监视容器
在 OMS 门户中启用解决方案后，你将看到**容器**磁贴显示有关容器主机和在主机中运行的容器的摘要信息。

![容器磁贴](./media/log-analytics-containers/containers-title.png)

该磁贴概述了你在环境中拥有多少个容器以及它们是失败、正在运行还是已停止。

### <a name="using-the-containers-dashboard"></a>使用容器仪表板
单击“容器”磁贴。 你将在磁贴中看到按以下内容组织的视图：

* 容器事件
* 错误
* 容器状态
* 容器映像库存
* CPU 和内存性能

仪表板中的每个窗格都是以可视化形式表示的对收集的数据执行的搜索。

![容器仪表板](./media/log-analytics-containers/containers-dash01.png)

![容器仪表板](./media/log-analytics-containers/containers-dash02.png)

在“**容器状态**”边栏选项卡中，单击顶部区域，如下所示。

![容器状态](./media/log-analytics-containers/containers-status.png)

日志搜索将打开，显示有关其中运行的主机和容器的信息。

![容器的日志搜索](./media/log-analytics-containers/containers-log-search.png)

你可以在此处编辑搜索查询以对其进行修改，以便查找你感兴趣的特定信息。 有关日志搜索的详细信息，请参阅 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)。

例如，通过将搜索查询中的“**正在运行**”更改为“**停止**”，你可以对搜索查询进行修改，使其显示所有已停止的容器，而不是正在运行的容器。

## <a name="troubleshoot-by-finding-a-failed-container"></a>通过查找失败的容器进行故障排除
如果容器退出时带有非零退出代码，则 OMS 会将其标记为“**失败**”。 你可以在“**失败容器**”边栏选项卡中查看环境中的错误和故障的概述。

### <a name="to-find-failed-containers"></a>查找失败的容器
1. 单击“**容器事件**”边栏选项卡。  
   ![容器事件](./media/log-analytics-containers/containers-events.png)
2. 显示容器状态的日志搜索将打开，如下所示。  
   ![容器状态](./media/log-analytics-containers/containers-container-state.png)
3. 接下来，单击失败的值可查看其他信息，如映像大小和停止和失败的映像数量。 展开“**显示更多**”以查看映像 ID。  
   ![失败的容器](./media/log-analytics-containers/containers-state-failed.png)
4. 接下来，找到运行此映像的容器。 在搜索查询中键入以下内容。
   `Type=ContainerInventory <ImageID>` 此时将显示日志。 你可以滚动查看失败的容器。  
   ![失败的容器](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>在日志中搜索容器数据
当你解决特定错误时，它可以帮助你查看环境中发生错误的位置。 以下日志类型将帮助你创建查询以返回所需的信息。

* **ContainerInventory** – 当你需要有关容器位置、容器名称和容器中运行的映像的信息时，请使用此类型。
* **ContainerImageInventory** – 当你尝试查找按映像组织的信息并查看映像 ID 或大小等映像信息时，请使用此类型。
* **ContainerLog** – 当你想要查找特定的错误日志信息和条目时，请使用此类型。
* **ContainerServiceLog** – 当你尝试查找 Docker 守护程序的审核线索信息（如 start、stop、delete 或 pull 命令）时，请使用此类型。

### <a name="to-search-logs-for-container-data"></a>搜索容器数据的日志
* 选择一个最近失败的映像，并找到它的错误日志。 首先，通过使用 **ContainerInventory** 搜索查找运行该映像的容器名称。 例如，搜索 `Type=ContainerInventory ubuntu Failed`  
    ![搜索 Ubuntu 容器](./media/log-analytics-containers/search-ubuntu.png)

  记下**名称**旁边容器的名称，然后搜索这些日志。 在此示例中，它是 `Type=ContainerLog adoring_meitner`。

**查看性能信息**

开始构造查询时，最好首先了解哪些查询可以实现。 例如，若要查看所有性能数据，请输入以下搜索查询，以尝试广泛的查询。

```
Type=Perf
```

![容器性能](./media/log-analytics-containers/containers-perf01.png)

当你在结果中单击**指标**一词时，可以更加图形化的形式查看此结果。

![容器性能](./media/log-analytics-containers/containers-perf02.png)

你可以通过在查询右侧键入特定容器的名称，将性能数据的显示范围限定到特定容器。

```
Type=Perf <containerName>
```

它列出了为单个容器收集的性能指标。

![容器性能](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>日志搜索查询示例
从一或两个示例开始生成查询，然后修改它们以适应你的环境，这通常很有用。 你可以首先尝试使用“**重要查询**”边栏选项卡，它可以帮助你构建更高级的查询。

![容器查询](./media/log-analytics-containers/containers-queries.png)

## <a name="saving-log-search-queries"></a>保存日志搜索查询
保存查询是 Log Analytics 中的一项标准功能。 通过保存这些查询，你日后可以方便地使用你觉得有用的查询。

创建一个对你有用的查询后，单击“日志搜索”页底部的“**收藏夹**”对其进行保存。 稍后可以从“**我的仪表板**”页轻松访问它。

## <a name="next-steps"></a>后续步骤
* [搜索日志](log-analytics-log-searches.md)以查看详细的容器数据记录。


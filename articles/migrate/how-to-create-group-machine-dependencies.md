---
title: 在 Azure Migrate 中使用计算机依赖项分组计算机 | Microsoft 文档
description: 介绍了如何在 Azure Migrate 服务中使用计算机依赖项创建评估。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: 96d1c44eb9ecb71684e817a89f9376a07dbe3ccb
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514963"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>为评估设置依赖项可视化

本文介绍如何在 Azure Migrate：服务器评估中设置依赖关系映射。

依赖关系映射有助于可视化要评估和迁移的计算机之间的依赖关系。

- 在 Azure Migrate：服务器评估中，将计算机收集在一起以进行评估。 通常是要一起迁移的计算机。
- 当您想要评估具有更高置信度的组时，通常使用依赖关系映射。
- 在运行评估和迁移之前，依赖关系映射有助于交叉检查计算机依赖关系。
- 映射和可视化依赖项有助于有效地计划迁移到 Azure。 这有助于确保不会遗留任何内容，从而避免在迁移过程中出现意外中断。
- 使用映射，可以发现需要一起迁移的相互依赖的系统。 你还可以确定正在运行的系统是否仍在为用户提供服务，或者是否要解除授权而非迁移。

[详细了解](concepts-dependency-visualization.md#how-does-it-work)依赖关系可视化。

## <a name="before-you-start"></a>开始之前

- 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
- 如果已创建项目，请确保已[添加](how-to-assess.md)Azure Migrate： Server 评估工具。
- 请确保已在 Azure Migrate 中发现了计算机;为此，可以设置适用于[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 设备。 设备将发现本地计算机，并将元数据和性能数据发送到 Azure Migrate：服务器评估。 [了解详细信息](migrate-appliance.md)。


**功能特色** | **注意**
--- | ---
可用性 | 依赖关系可视化在 Azure 政府版中不可用。
服务地图 | 依赖项可视化使用 Azure Monitor 中服务映射解决方案。 [服务映射](../azure-monitor/insights/service-map.md)会自动发现并显示服务器之间的连接。
代理 | 若要使用依赖关系可视化，请在要映射的计算机上安装以下代理：<br/> - [Log Analytics 代理](../azure-monitor/platform/log-analytics-agent.md)程序代理（以前称为 MICROSOFT MONITORING AGENT （MMA）。<br/> [服务映射依赖关系代理](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent)- 。<br/><br/> 若要自动完成代理安装，你可以使用 Configuration Manager 的部署工具，该工具具有用于 Azure Migrate 的代理部署解决方案。
依赖关系代理 | 查看 Windows 和 Linux 的[依赖关系代理支持](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent)。<br/><br/> [了解](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)有关使用脚本安装依赖关系代理的详细信息。
Log Analytics 代理（MMA） | [了解](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent)有关 MMA 安装方法的详细信息。<br/><br/> 对于 System Center Operations Manager 2012 R2 或更高版本监视的计算机，无需安装 MMA 代理。 服务映射与 Operations Manager 集成。 你可以使用[此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)的指南启用该集成。 但请注意，依赖关系代理将需要安装在这些计算机上。<br/><br/> [查看](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)Log Analytics 代理支持的 Linux 操作系统。
评估组 | 要可视化其依赖项的组所包含的计算机不应超过 10 台。 如果计算机超过10台，请将它们拆分成较小的组，以可视化依赖项。

## <a name="associate-a-log-analytics-workspace"></a>关联 Log Analytics 工作区

若要使用依赖关系可视化，需要将[Log Analytics 工作区](../azure-monitor/platform/manage-access.md)与 Azure Migrate 项目相关联。

- 只能在 Azure Migrate 项目订阅中附加工作区。
- 可以附加现有的工作区，或创建一个新的工作区。
- 首次为计算机设置依赖项可视化效果时，将附加工作区。
- 仅在 Azure Migrate 项目中发现计算机后才能附加工作区。 为此，可以设置适用于[VMware](how-to-set-up-appliance-vmware.md)或[hyper-v](how-to-set-up-appliance-hyper-v.md)的 Azure Migrate 设备。 设备将发现本地计算机，并将元数据和性能数据发送到 Azure Migrate：服务器评估。 [了解详细信息](migrate-appliance.md)。

按如下所示附加工作区：

1. 在**Azure Migrate：服务器评估**中，单击 "**概述**"。 如果尚未添加服务器评估工具，请[先执行该操作](how-to-assess.md)。
2. 在 "**概述**" 中，单击向下箭头以展开 " **Essentials**"。
3. 在**OMS 工作区**中，单击 "**需要配置**"。
4. 在 "**配置工作区**" 中，指定是否要创建新的工作区或使用现有工作区：

    ![添加工作区](./media/how-to-create-group-machine-dependencies/workspace.png)

    - 为新工作区指定名称后，可以选择将在其中创建工作区的[区域](https://azure.microsoft.com/global-infrastructure/regions/)。
    - 附加现有的工作区时，可以从迁移项目所在订阅中的所有可用工作区进行选择。
    - 需要对工作区的读取器访问权限才能附加该工作区。
    - 附加项目后，不能修改与该项目关联的工作区。

## <a name="download-and-install-the-vm-agents"></a>下载并安装 VM 代理

在要通过依赖关系映射进行可视化的每台本地计算机上下载并安装代理。

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 对于要使用依赖关系可视化的每台计算机，单击 "**需要代理安装**"。
3. 在计算机的 "**依赖关系**" 页中 >**下载并安装 MMA**，下载相应的代理，并按如下所述进行安装。
4. 在 "**下载并安装依赖关系代理**" 中，下载相应的代理，并按如下所述进行安装。
5. 在 "**配置 MMA 代理**" 下，复制 "工作区 ID" 和 "密钥"。 安装 MMA 代理时需要用到它们。

### <a name="install-the-mma"></a>安装 MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>在 Windows 计算机上安装代理

若要在 Windows 计算机上安装代理：

1. 双击下载的代理。
2. 在“欢迎”页面上，单击“下一步”。 在“许可条款”页面上，单击“我接受”以接受许可协议。
3. 在“目标文件夹”中，保留或修改默认安装文件夹，然后选择“下一步”。
4. 在“代理安装选项”中，选择“Azure Log Analytics” > “下一步”。
5. 单击“添加”以添加 Log Analytics 工作区。 粘贴从门户复制的工作区 ID 和密钥。 单击“下一步”。

你可以从命令行安装代理，也可以使用自动方法（如 Configuration Manager 或[Intigua](https://go.microsoft.com/fwlink/?linkid=2104196)）来安装代理。 [详细了解](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)如何使用这些方法安装 MMA 代理。 还可使用此[脚本](https://go.microsoft.com/fwlink/?linkid=2104394)安装 MMA 代理。

[了解](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)有关 MMA 支持的 Windows 操作系统的详细信息。

#### <a name="install-the-agent-on-a-linux-machine"></a>在 Linux 计算机上安装代理

若要在 Linux 计算机上安装代理：

1. 使用 scp/sftp 将相应的捆绑包（x86 或 x64）传输到 Linux 计算机。
2. 使用 --install 参数安装捆绑包。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[详细了解 MMA 支持的 Linux 操作系统的列表](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems)。 

### <a name="install-the-dependency-agent"></a>安装依赖项代理
1. 若要在 Windows 计算机上安装依赖项代理，请双击安装程序文件，然后按照向导操作。
2. 若要在 Linux 计算机上安装依赖项代理，请使用以下命令安装为根目录：

    ```sh InstallDependencyAgent-Linux64.bin```

[详细了解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples)如何使用脚本来安装依赖项代理。

[详细了解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems)依赖关系代理支持的操作系统。


## <a name="create-a-group-using-dependency-visualization"></a>使用依赖项可视化创建组

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 在 "**依赖关系**" 列中，单击要查看的每个计算机的 "**查看依赖关系**"。
3. 在依赖关系映射上，可以看到以下内容：
    - 到计算机的入站（客户端）和出站（服务器） TCP 连接。
    - 未安装依赖关系代理的依赖计算机按端口号进行分组。
    - 安装了依赖关系代理的依赖计算机显示为单独的框。
    - 在计算机内运行的进程。 展开每个计算机框可查看进程。
    - 计算机属性（包括 FQDN、操作系统、MAC 地址）。 单击每个计算机框可查看详细信息。

4. 可通过单击时间范围标签中的持续时间，查看不同持续时间的依赖项。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。

    > [!NOTE]
    > 时间范围最长可达一小时。 如果需要更长的范围，请使用 Azure Monitor 来查询依赖数据的时间较长。

5. 确定要组合在一起的依赖计算机后，请使用 Ctrl + 单击在地图上选择多台计算机，然后单击 "**分组计算机**"。
6. 指定组名。
7. 确认 Azure Migrate 已发现依赖计算机。

    - 如果 Azure Migrate：服务器评估未发现从属计算机，则无法将其添加到组中。
    - 若要添加计算机，请再次运行发现，并验证是否发现了计算机。

8. 如果想要为该组创建评估，请选中相应复选框，为该组创建新的评估。
8. 单击“确定”以保存组。

创建组后，建议您在该组中的所有计算机上安装代理，然后将整个组的依赖项可视化。

## <a name="query-dependency-data-in-azure-monitor"></a>查询 Azure Monitor 中的依赖关系数据

可以查询与 Azure Migrate 项目关联的 Log Analytics 工作区中服务映射捕获的依赖关系数据。 Log Analytics 用于编写和运行 Azure Monitor 日志查询。

- [了解如何](../azure-monitor/insights/service-map.md#log-analytics-records)在 Log Analytics 中搜索服务映射数据。
- [大致了解如何](../azure-monitor/log-query/get-started-queries.md)在[Log Analytics](../azure-monitor/log-query/get-started-portal.md)中编写日志查询。

为依赖关系数据运行查询，如下所示：

1. 安装代理后，请转到门户并单击“概述”。
2. 在**Azure Migrate：服务器评估**中，单击 "**概述**"。 单击向下箭头以展开 " **Essentials**"。
3. 在**OMS 工作区**中，单击工作区名称。
3. 在 "Log Analytics 工作区" 页上 > "**常规**"，单击 "**日志**"。
4. 编写查询，然后单击 "**运行**"。

### <a name="sample-queries"></a>示例查询

我们提供了一系列可用于提取依赖关系数据的示例查询。

- 可修改查询以提取首选数据点。
- [查看](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)依赖关系数据记录的完整列表。
- [查看](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)其他示例查询。

#### <a name="sample-review-inbound-connections"></a>示例：查看入站连接

查看一组 Vm 的入站连接。

- 表中的连接指标（VMConnection）的记录并不表示单独的物理网络连接。
- 多个物理网络连接分组到一个逻辑连接中。
- [详细了解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)如何在 VMConnection 中聚合物理网络连接数据。

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>示例：汇总已发送和已接收的数据

此示例汇总了一组计算机之间的入站连接上发送和接收的数据量。

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>后续步骤

为组[创建评估](how-to-create-assessment.md)。

---
title: 在 Azure Migrate 服务器评估中设置基于代理的依赖项分析
description: 本文介绍如何在 Azure Migrate 服务器评估中设置基于代理的依赖项分析。
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: 47fd7e7c864e82400288bb67da952a18b648849e
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996879"
---
# <a name="set-up-dependency-visualization"></a>设置依赖项可视化

本文介绍如何在 Azure Migrate：服务器评估中设置基于代理的依赖项分析。 [依赖关系分析](concepts-dependency-visualization.md)有助于识别和了解要评估并迁移到 Azure 的计算机之间的依赖关系。

## <a name="before-you-start"></a>开始之前

- [了解](concepts-dependency-visualization.md#agent-based-analysis)基于代理的依赖项分析。
- 查看为[VMware vm](migrate-support-matrix-vmware.md#agent-based-dependency-analysis-requirements)、[物理服务器](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)和[hyper-v vm](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)设置基于代理的依赖项可视化项的先决条件和支持要求。
- 请确保已[创建](how-to-add-tool-first-time.md)Azure Migrate 项目。
- 如果已创建项目，请确保已[添加](how-to-assess.md)Azure Migrate： Server 评估工具。
- 请确保已将[Azure Migrate 设备](migrate-appliance.md)设置为发现本地计算机。 了解如何为[VMware](how-to-set-up-appliance-vmware.md)、 [hyper-v](how-to-set-up-appliance-hyper-v.md)或[物理服务器](how-to-set-up-appliance-physical.md)设置设备。 设备将发现本地计算机，并将元数据、性能数据发送到 Azure Migrate：服务器评估。
- 若要使用依赖关系可视化，请将[Log Analytics 工作区](../azure-monitor/platform/manage-access.md)与 Azure Migrate 项目关联：
    - 仅在设置了 Azure Migrate 设备并在 Azure Migrate 项目中发现计算机后才能附加工作区。
    - 请确保订阅中具有包含 Azure Migrate 项目的工作区。
    - 工作区必须位于美国东部、东南亚或西欧区域。 其他区域中的工作区不能与项目关联。
    - 工作区必须位于[支持服务映射](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)的区域中。
    - 可以将新的或现有的 Log Analytics 工作区与 Azure Migrate 项目相关联。
    - 首次为计算机设置依赖项可视化效果时，将附加工作区。 添加 Azure Migrate 项目后，不能修改该工作区的工作区。
    - 在 Log Analytics 中，与 Azure Migrate 相关联的工作区用迁移项目密钥和项目名称进行标记。

## <a name="associate-a-workspace"></a>关联工作区

1. 发现用于评估的计算机后，请在 "**服务器** > **Azure Migrate：服务器评估**" 中单击 "**概述**"。  
2. 在**Azure Migrate：服务器评估**中，单击 " **Essentials**"。
3. 在**OMS 工作区**中，单击 "**需要配置**"。

     ![配置 Log Analytics 工作区](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. 在 "**配置 OMS 工作区**" 中，指定是否要创建新的工作区或使用现有的工作区。
    - 你可以从 "迁移项目订阅" 中的所有工作区选择现有工作区。
    - 你需要对工作区的读取器访问权限来关联它。
5. 如果创建新的工作区，请选择该工作区的位置。

    ![添加新工作区](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>下载并安装 VM 代理

在要分析的每台计算机上，安装代理。

> [!NOTE]
> 对于 System Center Operations Manager 2012 R2 或更高版本监视的计算机，无需安装 MMA 代理。 服务映射与 Operations Manager 集成。 [遵循](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)集成指南。

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 对于要通过依赖关系可视化进行分析的每台计算机，在 "**依赖项**" 列中，单击 "**需要代理安装**"。
3. 在 "**依赖关系**" 页上，下载适用于 Windows 或 LINUX 的 MMA 和依赖项代理。
4. 在 "**配置 MMA 代理**" 下，复制 "工作区 ID" 和 "密钥"。 安装 MMA 代理时需要用到它们。

    ![安装代理](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>安装 MMA

在要分析的每台 Windows 或 Linux 计算机上安装 MMA。

### <a name="install-mma-on-a-windows-machine"></a>在 Windows 计算机上安装 MMA

若要在 Windows 计算机上安装代理：

1. 双击下载的代理。
2. 在“欢迎”**** 页上，单击“下一步”****。 在 "**许可条款**" 页上，单击 "**我同意**接受许可"。
3. 在“目标文件夹”**** 中，保留或修改默认安装文件夹，然后选择“下一步”****。
4. 在“代理安装选项”**** 中，选择“Azure Log Analytics”**** > “下一步”****。
5. 单击“添加”**** 以添加 Log Analytics 工作区。 粘贴从门户复制的工作区 ID 和密钥。 单击“下一步”  。

你可以从命令行安装代理，也可以使用自动方法（如 Configuration Manager 或[Intigua](https://www.intigua.com/intigua-for-azure-migration)）来安装代理。
- [详细了解](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)如何使用这些方法安装 MMA 代理。
- 还可使用此[脚本](https://go.microsoft.com/fwlink/?linkid=2104394)安装 MMA 代理。
- [了解](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems)有关 MMA 支持的 Windows 操作系统的详细信息。

### <a name="install-mma-on-a-linux-machine"></a>在 Linux 计算机上安装 MMA

若要在 Linux 计算机上安装 MMA：

1. 使用 scp/sftp 将相应的捆绑包（x86 或 x64）传输到 Linux 计算机。
2. 使用 --install 参数安装捆绑包。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[详细了解 MMA 支持的 Linux 操作系统的列表](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems)。 

## <a name="install-the-dependency-agent"></a>安装依赖项代理

1. 若要在 Windows 计算机上安装依赖项代理，请双击安装程序文件，然后按照向导操作。
2. 若要在 Linux 计算机上安装依赖项代理，请使用以下命令安装为根目录：

    ```sh InstallDependencyAgent-Linux64.bin```

- [详细了解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples)如何使用脚本来安装依赖项代理。
- [详细了解](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems)依赖关系代理支持的操作系统。


## <a name="create-a-group-using-dependency-visualization"></a>使用依赖项可视化创建组

现在创建一个组进行评估。 


> [!NOTE]
> 要可视化其依赖项的组所包含的计算机不应超过 10 台。 如果计算机超过10台，请将它们拆分成较小的组。

1. 在**Azure Migrate：服务器评估**中，单击 "**发现的服务器**"。
2. 在 "**依赖关系**" 列中，单击要查看的每个计算机的 "**查看依赖关系**"。
3. 在依赖关系映射上，可以看到以下内容：
    - 到计算机的入站（客户端）和出站（服务器） TCP 连接。
    - 未安装依赖关系代理的依赖计算机按端口号进行分组。
    - 安装了依赖关系代理的依赖计算机显示为单独的框。
    - 在计算机内运行的进程。 展开每个计算机框可查看进程。
    - 计算机属性（包括 FQDN、操作系统、MAC 地址）。 单击每个计算机框可查看详细信息。

4. 可通过单击时间范围标签中的持续时间，查看不同持续时间的依赖项。
    - 时间范围默认为 1 小时。 
    - 你可以修改时间范围，或指定开始和结束日期以及持续时间。
    - 时间范围最长可达一小时。 如果需要更长的范围，请使用 Azure Monitor 来查询依赖数据的时间较长。

5. 确定要组合在一起的依赖计算机后，请使用 Ctrl + 单击在地图上选择多台计算机，然后单击 "**分组计算机**"。
6. 指定组名。
7. 确认 Azure Migrate 已发现依赖计算机。

    - 如果 Azure Migrate：服务器评估未发现从属计算机，则无法将其添加到组中。
    - 若要添加计算机，请再次运行发现，并验证是否发现了计算机。

8. 如果想要为该组创建评估，请选中相应复选框，为该组创建新的评估。
8. 单击“确定”**** 以保存组。

创建组后，建议您在该组中的所有计算机上安装代理，然后将整个组的依赖项可视化。

## <a name="query-dependency-data-in-azure-monitor"></a>查询 Azure Monitor 中的依赖关系数据

您可以查询与 Azure Migrate 项目关联的 Log Analytics 工作区中服务映射捕获的依赖关系数据。 Log Analytics 用于编写和运行 Azure Monitor 日志查询。

- [了解如何](../azure-monitor/insights/service-map.md#log-analytics-records)在 Log Analytics 中搜索服务映射数据。
- [大致了解如何](../azure-monitor/log-query/get-started-queries.md)在[Log Analytics](../azure-monitor/log-query/get-started-portal.md)中编写日志查询。

为依赖关系数据运行查询，如下所示：

1. 安装代理后，请转到门户并单击“概述”****。
2. 在**Azure Migrate：服务器评估**中，单击 "**概述**"。 单击向下箭头以展开 " **Essentials**"。
3. 在**OMS 工作区**中，单击工作区名称。
3. 在 "Log Analytics 工作区" 页上 > "**常规**"，单击 "**日志**"。
4. 编写查询，然后单击 "**运行**"。

### <a name="sample-queries"></a>示例查询

下面是一些可用于提取依赖关系数据的示例查询。

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



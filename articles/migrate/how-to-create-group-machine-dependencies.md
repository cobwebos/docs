---
title: 在 Azure Migrate 中使用计算机依赖项分组计算机 | Microsoft 文档
description: 介绍了如何在 Azure Migrate 服务中使用计算机依赖项创建评估。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 686c91669e5eccd7979c248db42d6f5b5079308b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280904"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>使用计算机依赖项映射分组计算机

本文介绍如何通过可视化计算机的依赖项为 [Azure Migrate](migrate-overview.md) 评估创建计算机组。 当你想要在运行评估之前通过交叉检查计算机依赖项评估可信度较高的 VM 组时，通常都会使用此方法。 依赖项可视化有助于有效地计划如何迁移到 Azure。 它帮助确保在迁移到 Azure 的过程中不会遗留任何内容，也不会发生意外中断。 可以发现所有需要一起迁移的互相依赖的系统，并识别运行中的系统仍然为用户提供服务还是在等待解除授权而非迁移。

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

## <a name="prepare-for-dependency-visualization"></a>准备依赖项可视化
Azure Migrate 利用 Azure Monitor 日志，若要启用的计算机的依赖项可视化效果中的服务映射解决方案。

### <a name="associate-a-log-analytics-workspace"></a>关联 Log Analytics 工作区
若要利用依赖项可视化功能，需要将现有或新的 Log Analytics 工作区与 Azure Migrate 项目进行关联。 只能在创建迁移项目的同一订阅中创建或附加工作区。

- 若要将 Log Analytics 工作区附加到项目，请在“概述”中转到项目的“Essentials”部分，单击“需要配置”

    ![关联 Log Analytics 工作区](./media/concepts-dependency-visualization/associate-workspace.png)

- 关联一个工作区时，可以选择是创建新的工作区还是附加现有工作区：
  - 创建新工作区时，需要指定工作区的名称。 然后在与迁移项目相同的 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中的区域内创建工作区。
  - 附加现有的工作区时，可以从迁移项目所在订阅中的所有可用工作区进行选择。 请注意，只有在[服务映射受支持](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions)的区域中创建的那些工作区才会列出。 为了能够附加工作区，请确保对该工作区有“读取者”访问权限。

> [!NOTE]
> 你无法更改与迁移项目关联的工作区。

### <a name="download-and-install-the-vm-agents"></a>下载并安装 VM 代理
配置工作区后，需要在要评估的每个本地计算机上下载并安装代理。 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 [Log Analytics 网关](../azure-monitor/platform/gateway.md)。

1. 在“概述”中，单击“管理” > “计算机”，然后选择所需的计算机。
2. 在“依赖项”列中，单击“安装代理”。
3. 在要评估的每台 VM 上，从“依赖项”页下载并安装 Microsoft 监视代理 (MMA) 和依赖项代理。
4. 复制工作区 ID 和密钥。 在本地计算机上安装 MMA 时将需要这些信息。

> [!NOTE]
> 若要自动安装代理，可以使用任何部署工具（例如 System Center Configuration Manager）或使用我们的合作伙伴工具 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)，它提供了针对 Azure Migrate 的代理部署解决方案。

### <a name="install-the-mma"></a>安装 MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>在 Windows 计算机上安装代理

若要在 Windows 计算机上安装代理：

1. 双击下载的代理。
2. 在“欢迎”页面上，单击“下一步”。 在“许可条款”页面上，单击“我接受”以接受许可协议。
3. 在“目标文件夹”中，保留或修改默认安装文件夹，然后选择“下一步”。
4. 在“代理安装选项”中，选择“Azure Log Analytics” > “下一步”。
5. 单击“添加”以添加 Log Analytics 工作区。 粘贴从门户复制的工作区 ID 和密钥。 单击“下一步”。

可以从命令行或使用自动的方法，如 System Center Configuration Manager 安装代理。 [详细了解](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)如何使用这些方法安装 MMA 代理。

#### <a name="install-the-agent-on-a-linux-machine"></a>在 Linux 计算机上安装代理

若要在 Linux 计算机上安装代理：

1. 使用 scp/sftp 将相应的捆绑包（x86 或 x64）传输到 Linux 计算机。
2. 使用 --install 参数安装捆绑包。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[详细了解 MMA 支持的 Linux 操作系统的列表](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)。

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>在 SCOM 监视的计算机上安装代理

受 System Center Operations Manager 2012 R2 或更高版本监视的计算机无需安装 MMA 代理。 服务映射具有与 SCOM 的集成，可利用 SCOM MMA 收集必要的依赖项数据。 你可以使用[此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)的指南启用该集成。 但请注意，需要在这些计算机上安装依赖项代理。


### <a name="install-the-dependency-agent"></a>安装依赖项代理
1. 若要在 Windows 计算机上安装依赖项代理，请双击安装程序文件，然后按照向导操作。
2. 若要在 Linux 计算机上安装依赖项代理，请使用以下命令安装为根目录：

    ```sh InstallDependencyAgent-Linux64.bin```

深入了解针对 [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) 和 [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) 操作系统的依赖项代理支持。

[详细了解](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)如何使用脚本来安装依赖项代理。


## <a name="create-a-group"></a>创建组

1. 安装代理后，请转到门户并单击“管理” > “计算机”。
2. 搜索在其中安装代理的计算机。
3. 计算机的“依赖项”列现在应显示为“查看依赖项”。 单击该列，查看计算机的依赖项。
4. 计算机的依赖项映射显示以下详细信息：
    - 与计算机的入站（客户端）和出站（服务器）TCP 连接
        - 没有安装 MMA 和依赖项代理的依赖计算机按端口号进行分组
        - 安装了 MMA 和依赖项代理的依赖计算机显示在单独的框中
    - 计算机内运行的进程，可以展开每个计算机框查看这些进程
    - 每台计算机的属性（如，完全限定的域名、操作系统、MAC 地址等），可单击每个计算机框查看相关详细信息

      ![查看计算机依赖项](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. 可通过单击时间范围标签中的持续时间，查看不同持续时间的依赖项。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。

   > [!NOTE]
   >    目前，依赖性可视化效果 UI 不支持选择超过一小时的时间范围。 使用 Azure Monitor 记录到[查询依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)通过更长的时间。

5. 确定想要分成一组的依赖计算机之后，按住 Ctrl 键单击，在映射上选择多台计算机，然后单击“分组计算机”。
6. 指定组名。 确认 Azure Migrate 已发现依赖计算机。

    > [!NOTE]
    > 如果 Azure Migrate 没有发现依赖计算机，则无法将其添加到组中。 要将此类计算机添加到组中，需要再次在 vCenter Server 的正确范围内运行发现进程，确保 Azure Migrate 发现该计算机。  

7. 如果想要为该组创建评估，请选中相应复选框，为该组创建新的评估。
8. 单击“确定”以保存组。

创建组后，建议在该组中的所有计算机上安装代理，然后通过可视化整个组的依赖项优化该组。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>查询依赖项数据从 Azure Monitor 日志

服务映射捕获的依赖项数据可用于在与 Azure Migrate 项目关联的 Log Analytics 工作区中进行查询。 [了解详细信息](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)有关服务映射数据表来查询 Azure 监视器中的信息记录。 

若要运行 Kusto 查询：

1. 安装代理后，请转到门户并单击“概述”。
2. 在“概述”中，转到项目的“Essentials”部分，然后单击“OMS 工作区”旁边提供的工作区名称。
3. 在“Log Analytics 工作区”页上，单击“常规” > “日志”。
4. 编写查询来收集依赖项数据使用 Azure Monitor 日志。 [此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)提供用于收集依赖项数据的示例查询。
5. 通过单击“运行”，运行查询。 

[了解详细信息](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)有关如何编写 Kusto 查询。 

### <a name="sample-azure-monitor-logs-queries"></a>示例 Azure Monitor 日志查询

以下是可用来提取依赖关系数据的示例查询。 请注意，可以修改查询以提取你的首选的数据点。 依赖项数据记录中的字段的详尽列表是可用[此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>汇总一组计算机上的入站的连接

请注意，对于连接指标，VMConnection，表中的记录不表示单个物理网络连接。 多个物理网络连接被分组到逻辑连接。 [了解详细信息](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)如何物理网络连接有关的数据聚合到 VMConnection 中的单个逻辑记录。 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>汇总一组计算机之间的入站连接上发送和接收的数据的量

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

- [详细了解有关依赖项可视化的常见问题解答](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization)。
- [了解如何通过可视化组依赖项来优化组](how-to-create-group-dependencies.md)。
- [详细了解](concepts-assessment-calculation.md)如何计算评估。

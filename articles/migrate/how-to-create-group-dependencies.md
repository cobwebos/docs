---
title: 在 Azure Migrate 中使用组依赖项映射优化评估组 | Microsoft 文档
description: 介绍如何在 Azure Migrate 服务中使用组依赖项映射优化评估。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357175"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>使用组依赖项映射优化组

本文介绍如何通过可视化组中所有计算机的依赖项优化组。 如果想要在运行评估之前通过交叉检查组依赖项来优化现有组的成员身份，通常都会使用此方法。 通过依赖项可视化来优化组可帮助你有效地计划到 Azure 的迁移。 你可以发现所有需要一起迁移的互相依赖的系统。 它帮助确保在迁移到 Azure 的过程中不会遗留任何内容，也不会发生意外中断。


> [!NOTE]
> 要可视化其依赖项的组所包含的计算机不应超过 10 台。 如果组中所包含的计算机超过 10 台，建议将该组拆分为更小的组，以便利用依赖项可视化功能。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>准备依赖项可视化
Azure Migrate 利用 Azure Monitor 日志，若要启用的计算机的依赖项可视化效果中的服务映射解决方案。

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

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
要查看组的依赖项，需要在组中包含的每台本地计算机上下载并安装代理。 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 [Log Analytics 网关](../azure-monitor/platform/gateway.md)。

1. 在“概述”中，单击“管理” > “组”，转到所需组。
2. 在计算机列表中，单击“依赖项代理”列中的“需要安装”，查看有关如何下载并安装代理的说明。
3. 在组中包含的每台 VM 上，从“依赖项”页下载并安装 Microsoft Monitoring Agent (MMA) 和依赖项代理。
4. 复制工作区 ID 和密钥。 在本地计算机上安装 MMA 时需要这些信息。

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

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>在受 System Center Operations Manager 监视的计算机上安装代理

受 Operations Manager 2012 R2 或更高版本监视的计算机无需安装 MMA 代理。 服务映射与 Operations Manager 集成，利用 Operations Manager MMA 收集必要的依赖项数据。 可以使用[此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)的指南启用集成。 但请注意，需要在这些计算机上安装依赖项代理。

### <a name="install-the-dependency-agent"></a>安装依赖项代理
1. 若要在 Windows 计算机上安装依赖项代理，请双击安装程序文件，然后按照向导操作。
2. 若要在 Linux 计算机上安装依赖项代理，请使用以下命令安装为根目录：

    ```sh InstallDependencyAgent-Linux64.bin```

深入了解针对 [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) 和 [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) 操作系统的依赖项代理支持。

[详细了解](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)如何使用脚本来安装依赖项代理。

## <a name="refine-the-group-based-on-dependency-visualization"></a>通过依赖项可视化优化组
在组中的所有计算机上都安装代理后，可以可视化该组的依赖项，并通过执行以下步骤优化该组。

1. 在 Azure Migrate 项目中，在 **“管理”** 下，单击 **“组”**，然后选择组。
2. 在组页面上，单击 **“查看依赖项”**，打开组依赖项映射。
3. 组的依赖项映射显示了以下详细信息：
   - 与组中包含的所有计算机的入站（客户端）和出站（服务器）TCP 连接
       - 没有安装 MMA 和依赖项代理的依赖计算机按端口号进行分组
       - 安装了 MMA 和依赖项代理的依赖计算机显示在单独的框中
   - 计算机内运行的进程，可以展开每个计算机框查看这些进程
   - 每台计算机的属性（如，完全限定的域名、操作系统、MAC 地址等），可单击每个计算机框查看相关详细信息

     ![查看组依赖项](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. 若要查看更精细的依赖项，请单击时间范围并进行修改。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。

   > [!NOTE]
   >    目前，依赖性可视化效果 UI 不支持选择超过一小时的时间范围。 使用 Azure Monitor 记录到[查询依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-a-group)通过更长的时间。

4. 验证依赖计算机，以及每台计算机内运行的进程，标识应在组中添加或删除的计算机。
5. 按住 Ctrl 键单击，在映射上选择想要在组中添加或删除的计算机。
    - 你只能添加已发现的计算机。
    - 在组中添加和删除计算机会使过去对此组的评估失效。
    - 修改组时，你可以视情况创建新的评估。
5. 单击“确定”以保存组。

    ![添加或删除计算机](./media/how-to-create-group-dependencies/add-remove.png)

如果你想要检查显示在组依赖项映射中的特定计算机的依赖项，请[设置计算机依赖项映射](how-to-create-group-machine-dependencies.md)。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>查询依赖项数据从 Azure Monitor 日志

捕获的服务映射依赖关系数据是可用于查询与你的 Azure Migrate 项目相关联的 Log Analytics 工作区中。 [了解详细信息](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)有关服务映射数据表来查询 Azure 监视器中的信息记录。 

若要运行 Kusto 查询：

1. 安装代理后，请转到门户并单击“概述”。
2. 在“概述”中，转到项目的“Essentials”部分，然后单击“OMS 工作区”旁边提供的工作区名称。
3. 在“Log Analytics 工作区”页上，单击“常规” > “日志”。
4. 编写查询来收集依赖项数据使用 Azure Monitor 日志。 在下一部分中查找示例的查询。
5. 通过单击“运行”，运行查询。 

[了解详细信息](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)有关如何编写 Kusto 查询。 

## <a name="sample-azure-monitor-logs-queries"></a>示例 Azure Monitor 日志查询

以下是可用来提取依赖关系数据的示例查询。 您可以修改查询以提取你的首选的数据点。 依赖项数据记录中的字段的详尽列表是可用[此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)。 查找更多的示例查询[此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)。

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>汇总一组计算机上的入站的连接

请注意，对于连接指标，VMConnection，表中的记录不表示单个物理网络连接。 多个物理网络连接被分组到逻辑连接。 [了解详细信息](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)如何物理网络连接有关的数据聚合到 VMConnection 中的单个逻辑记录。 

```
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
- [详细了解](concepts-assessment-calculation.md)如何计算评估。

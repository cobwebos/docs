---
title: "使用 Log Analytics 中的 Service Fabric 解决方案优化环境 | Microsoft Docs"
description: "可以使用 Service Fabric 解决方案访问 Service Fabric 应用程序、微服务、节点和群集的风险和运行状况。"
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: nini
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2fe6c2b23c816a5ec5bb00199725cdf0b29b31f0


---
# <a name="service-fabric-solution-in-log-analytics"></a>Log Analytics 中的 Service Fabric 解决方案
> [!div class="op_single_selector"]
> * [资源管理器](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
> 
> 

本文介绍如何使用 Log Analytics 中的 Service Fabric 解决方案帮助识别和解决 Service Fabric 群集中的问题。

Service Fabric 解决方案通过从 Azure WAD 表中收集相关数据来使用 Service Fabric VM 中的 Azure 诊断数据。 然后，Log Analytics 读取 Service Fabric 框架事件，包括“可靠服务事件”、“执行组件事件”、“操作事件”和“自定义 ETW 事件”。 借助解决方案仪表板，可以查看 Service Fabric 环境中值得注意的问题和相关事件。

若要开始使用解决方案，需要将 Service Fabric 群集连接到 Log Analytics 工作区。 下面是需要考虑的三种方案：

1. 如果你尚未部署 Service Fabric 群集，请使用***部署已连接到 Log Analytics 工作区的 Service Fabric 群集***中的步骤部署新群集，并将其配置为 Log Analytics 的报告。
2. 如果你需要从主机中收集性能计数器以便在 Service Fabric 群集上使用其他 OMS 解决方案（如安全性），请按照***部署已连接到装有 VM 扩展的 OMS 工作区的 Service Fabric 群集***中的步骤操作。
3. 如果已部署了 Service Fabric 群集并且希望将其连接到 Log Analytics，请按照***将现有的存储帐户添加到 Log Analytics*** 中的步骤操作。

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>部署连接到 Log Analytics 工作区的 Service Fabric 群集。
此模板执行以下操作：

1. 部署已连接到 Log Analytics 工作区的 Azure Service Fabric 群集。 可以选择在部署模板时创建新工作区，或者输入现有 Log Analytics 工作区的名称。
2. 将诊断存储帐户添加到 Log Analytics 工作区。
3. 在 Log Analytics 工作区中启用 Service Fabric 解决方案。

[![部署到 Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

在选择上述部署按钮后，将转到 Azure 门户，其中具有可供编辑的参数。 如果输入新的 Log Analytics 工作区名称，请确保创建新的资源组：![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

接受法律条款，并点击“创建”以启动部署。 完成部署后，你应该会看到新创建的工作区和群集，以及添加的 WADServiceFabric*Event、WADWindowsEventLogs 和 WADETWEvent 表：

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-an-oms-workspace-with-vm-extension-installed"></a>部署已连接到装有 VM扩展的 OMS 工作区的 Service Fabric 群集。
此模板执行以下操作：

1. 部署已连接到 Log Analytics 工作区的 Azure Service Fabric 群集。 可以创建新的工作区或使用现有的工作区。
2. 将诊断存储帐户添加到 Log Analytics 工作区。
3. 在 Log Analytics 工作区中启用 Service Fabric 解决方案。
4. 在 Service Fabric 群集的每个 VM 规模集中安装 MMA 代理扩展。 安装 MMA 代理后，可以查看有关节点的性能指标。

[![部署到 Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

按照上述相同的步骤，输入必需的参数并开始部署。 你应该会再次看到所有创建的新工作区、群集和 WAD 表：

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>查看性能数据
若要从节点查看性能数据：
</br>

* 从 Azure 门户启动 Log Analytics 工作区。

![Service Fabric](./media/log-analytics-service-fabric/6.png)

* 转到左侧窗格上的“设置”，并依次选择“数据”>>“Windows 性能计数器”>>“添加所选的性能计数器”：![Service Fabric](./media/log-analytics-service-fabric/7.png)
* 在“日志搜索”中，使用以下查询深入探讨有关节点的关键指标：
  </br>
  
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 比较过去一小时所有节点中的平均 CPU 利用率，以查看哪些节点存在问题以及节点在什么时间间隔达到峰值：
  
    ``` Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR. ```
  
    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. 通过以下查询，查看每个节点上可用内存的类似折线图：

    ```Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.```

    若要查看所有节点的列表（显示每个节点可用空间 (MB) 的精确平均值），可以使用以下查询：

    ```Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)


    c. 如果你想要通过检查每小时平均值、最小值、最大值和 75% CPU 使用率向下钻取到特定节点，你将能够使用以下查询（替换计算机字段）来执行此操作：

    ```Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

    在[此处](https://blogs.technet.microsoft.com/msoms/tag/metrics/)阅读有关 Log Analytics 中性能指标的详细信息。


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>将现有存储帐户添加到 Log Analytics
此模板仅将现有存储帐户添加到新的或现有 Log Analytics 工作区。
</br>

[![部署到 Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> 在选择资源组时，如果使用的是现有 Log Analytics 工作区，请选择“使用现有工作区”，然后搜索包含 OMS 工作区的资源组。 否则，请创建新的工作区。
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
> 
> 

部署完此模板后，你将能够看到存储帐户连接到 Log Analytics 工作区。 在此情况下，我已向前面创建的 Exchange 工作区添加了多个存储帐户。
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>查看 Service Fabric 事件
在完成部署并且已在工作区中启用 Service Fabric 解决方案后，选择 Log Analytics 门户中的“Service Fabric”磁贴以启动 Service Fabric 仪表板。 仪表板包含下表中的列。 每个列按照指定时间范围内符合该列条件的计数列出了前十个事件。 可以通过在每一列右下方单击“查看全部”或单击列标题来运行提供整个列表的日志搜索。

| **Service Fabric 事件** | **说明** |
| --- | --- |
| 值得注意的问题 |显示 RunAsyncFailures RunAsynCancellations 和 Node Down 等问题。 |
| 操作事件 |应用程序升级和部署等值得注意的操作事件。 |
| 可靠服务事件 |Runasyncinvocations 等值得注意的可靠服务事件。 |
| 执行组件事件 |由微服务生成的，值得注意的执行组件事件，例如执行组件方法产生的异常、执行组件激活和停用等。 |
| 应用程序事件 |由应用程序生成的所有自定义 ETW 事件。 |

![Service Fabric 仪表板](./media/log-analytics-service-fabric/sf3.png)

![Service Fabric 仪表板](./media/log-analytics-service-fabric/sf4.png)

下表显示了数据收集方法和有关如何为 Service Fabric 收集数据的其他详细信息。

| 平台 | 直接代理 | SCOM 代理 | Azure 存储空间 | 是否需要 SCOM？ | 通过管理组发送的 SCOM 代理数据 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![否](./media/log-analytics-malware/oms-bullet-red.png) |![否](./media/log-analytics-malware/oms-bullet-red.png) |![是](./media/log-analytics-malware/oms-bullet-green.png) |![否](./media/log-analytics-malware/oms-bullet-red.png) |![否](./media/log-analytics-malware/oms-bullet-red.png) |10 分钟 |

> [!NOTE]
> 可以通过单击仪表板顶部的“基于最近 7 天的数据”，更改这些事件在 Service Fabric 解决方案中的作用域。 还可以显示最近 7 天、1 天或 6 小时内生成的事件。 或者，可以选择“自定义”来指定自定义的日期范围。
> 
> 

## <a name="next-steps"></a>后续步骤
* 使用 [Log Analytics 中的日志搜索](log-analytics-log-searches.md)查看详细的 Service Fabric 事件数据。




<!--HONumber=Nov16_HO3-->



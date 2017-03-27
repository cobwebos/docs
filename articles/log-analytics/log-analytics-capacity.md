---
title: "Log Analytics 中的容量管理解决方案 | Microsoft Docs"
description: "Log Analytics 中的容量规划解决方案可用于帮助你了解 System Center Virtual Machine Manager 管理的 Hyper-V 服务器的容量"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 57e7fbdaa393e078b62a6d6a0b181b67d532523d
ms.openlocfilehash: c34cda0da164c711c8effc78d2af38ad8df581aa


---
# <a name="capacity-management-solution-in-log-analytics"></a>Log Analytics 中的容量管理解决方案
可使用 Log Analytics 中的容量管理解决方案帮助理解 Hyper-V 服务器的容量。 此解决方案需要 System Center Operations Manager 和 System Center Virtual Machine Manager。 如果使用直接连接的代理，容量规划解决方案无法运行。 该解决方案会读取受监视服务器上的性能计数器，然后将使用情况数据发送到云中的 OMS 服务进行处理。 逻辑应用于使用情况数据，云服务记录数据。 随着时间的推移，将基于当前消耗量确定使用模式并预测容量。

例如，预测可以确定单个服务器何时需要额外的处理器核心或内存。 在本示例中，该预测可能会指出：服务器在 30 天后会需要额外的内存。 此预测可帮助你在服务器下一个维护时段期内规划内存升级。

> [!NOTE]
> 不能将容量管理解决方案添加到工作区。 已安装容量管理解决方案的客户可以继续使用该解决方案。  
>
>

个人预览版提供替换容量和性能解决方案。 此替换解决方案旨在通过原始容量管理解决方案解决以下客户报告的难题：

* 使用 Virtual Machine Manager 和 Operations Manager 的要求
* 无法基于组进行自定义/筛选
* 以小时为单位的数据聚合，频率不够高
* 不存在 VM 级别的见解
* 数据可靠性

新的容量解决方案的优点：

* 支持精细的数据收集，改善了可靠性和准确性
* 支持 Hyper-V，无需使用 VMM
* 关于 VM 级别利用率的见解

新解决方案当前需要 Hyper-V Server 2012 或更高版本。 该解决方案可深入了解 Hyper-V 环境，并可查看在这些 Hyper-V 服务器上运行的主机和 VM 的总体利用率（CPU、内存和磁盘）。 将收集在这些服务器上运行的所有主机和 VM 的 CPU、内存和磁盘度量值。

此页的剩余文档介绍旧的容量管理解决方案。 此文档在公共预览版提供新解决方案时更新。

## <a name="installing-and-configuring-the-solution"></a>安装和配置解决方案
使用以下信息安装和配置解决方案。

* 容量管理解决方案需要 Operations Manager。
* 容量管理解决方案需要 Virtual Machine Manager。
* 需要将 Operations Manager 与 Virtual Machine Manager (VMM) 连接起来。 有关连接系统的详细信息，请参阅[如何将 VMM 与 Operations Manager 连接起来](http://technet.microsoft.com/library/hh882396.aspx)。
* Operations Manager 必须连接到 Log Analytics。
* 使用[从解决方案库中添加 Log Analytics 解决方案](log-analytics-add-solutions.md)中所述的过程，将容量管理解决方案添加到 OMS 工作区。  无需进一步配置。

## <a name="capacity-management-data-collection-details"></a>容量管理数据集合详细信息
容量管理使用已启用的代理收集性能数据、元数据和状态数据。

下表显示了数据收集方法以及有关如何为容量管理收集数据的其他详细信息。

| 平台 | 直接代理 | Operations Manager 代理 | Azure 存储空间 | 需要 Operations Manager？ | Operations Manager 代理数据通过管理组发送 | 收集频率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![否](./media/log-analytics-capacity/oms-bullet-red.png) |![是](./media/log-analytics-capacity/oms-bullet-green.png) |![否](./media/log-analytics-capacity/oms-bullet-red.png) |![是](./media/log-analytics-capacity/oms-bullet-green.png) |![是](./media/log-analytics-capacity/oms-bullet-green.png) |每小时 |

下表显示容量管理收集的数据类型的示例：

| **数据类型** | **字段** |
| --- | --- |
| Metadata |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| 性能 |ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded |
| 状态 |StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

## <a name="capacity-management-page"></a>容量管理页面
安装容量规划解决方案后，可通过使用 OMS 中**概述**页上的“容量规划”磁贴，查看受监视服务器的容量。

![“容量规划”磁贴图像](./media/log-analytics-capacity/oms-capacity01.png)

该磁贴打开“容量管理”仪表板，可在其中查看服务器容量摘要。 该页面显示以下可单击的磁贴：

* *虚拟机计数*：显示虚拟机容量的剩余天数
* *计算*：显示处理器核心和可用内存
* *存储*：显示所用磁盘空间和平均磁盘延迟
* *搜索*：可使用数据资源管理器搜索 OMS 系统中的任何数据

![“容量管理”仪表板图像](./media/log-analytics-capacity/oms-capacity02.png)

### <a name="to-view-a-capacity-page"></a>查看容量页面
* 在“概述”页面上，单击“容量管理”，然后单击“计算”或“存储”。

## <a name="compute-page"></a>计算页
可使用 Microsoft Azure OMS 中的“计算”仪表板查看关于利用率的容量信息、预测的容量天数以及与基础结构相关的效率。 使用“利用率”区域查看虚拟机主机中的 CPU 内核和内存利用率。 可使用预测工具评估给定日期范围需要的容量。 可使用“效率”区域查看虚拟机主机的效率。 可通过单击链接项来查看有关详细信息。

可为以下类别生成 Excel 工作簿：

* 内核利用率最高的顶部主机
* 内存利用率最高的顶部主机
* 虚拟机效率低下的顶部主机
* 按利用率分类的顶部主机
* 按利用率分类的底部主机

![“容量管理计算”页面图像](./media/log-analytics-capacity/oms-capacity03.png)

以下区域显示在“计算”仪表板上：

**利用率**：查看虚拟机主机上的 CPU 核心和内存利用率。

* *使用的核心数*：所有主机的总和（CPU 使用百分比乘以主机上物理核心数）。
* *可用核心数*：总物理核心数减去使用的核心数。
* *可用核心数百分比*：可用物理核心数除以物理核心总数。
* *每个虚拟机的虚拟核心数*：系统中的总虚拟核心数除以系统中的虚拟机总数。
* *虚拟核心数与物理核心数之比*：物理核心总数与系统中虚拟机使用的物理核心数之比。
* *可用的虚拟核心数*：虚拟核心数与物理核心数之比乘以可用的物理核心数。
* *使用的内存*：所有主机使用的总内存。
* *可用内存*：总物理内存减去使用的内存。
* *可用内存百分比*：可用物理内存除以总物理内存。
* *每个虚拟机的虚拟内存*：系统中的总虚拟内存除以系统中的虚拟机总数。
* *虚拟内存与物理内存之比*：系统中的总虚拟内存除以系统中的总物理内存。
* *可用的虚拟内存*：虚拟内存与物理内存之比乘以可用的物理内存。

**预测工具**

使用预测工具可查看资源利用率的历史趋势。 这包括虚拟机、内存、核心和存储的使用趋势。 预测功能使用预测算法，有助于了解耗尽每个资源的时间。 这有助于计算相应的容量规划，以便了解何时需要购买更多容量（例如内存、内核或存储）。

**效率**

* *空闲虚拟机*：在指定时间内使用少于 10% 的 CPU 和 10% 的内存。
* *过度使用的虚拟机*：在指定时间内使用多于 90% 的 CPU 和 90% 的内存。
* *空闲主机*：在指定时间内使用少于 10% 的 CPU 和 10% 的内存。
* *过度使用的主机*：在指定时间内使用多于 90% 的 CPU 和 90% 的内存。

### <a name="to-work-with-items-on-the-compute-page"></a>在“计算”页面处理项目
1. 在“计算”仪表板的“利用率”区域中，查看有关使用的 CPU 内核和内存的容量信息。
2. 在“搜索”页中单击项目以打开它，然后查看有关详细信息。
3. 在“预测工具”中，移动日期滑块，显示将在所选日期使用的容量预测情况。
4. 在“效率”区域中，查看有关虚拟机和虚拟机主机的容量效率信息。

## <a name="direct-attached-storage-page"></a>“直接附加存储”页
可使用 OMS 中的“直接附加存储”仪表板查看有关存储利用率、磁盘性能和预测磁盘容量天数的容量信息。 使用“利用率”区域查看虚拟机主机中的磁盘空间使用情况。 可使用“磁盘性能”区域查看虚拟机主机的磁盘吞吐量和延迟。 还可使用预测工具估算在给定日期范围内预期可用的容量。 可通过单击链接项来查看有关详细信息。

通过此容量信息为以下类别生成 Excel 工作簿：

* 按主机排序的最高磁盘空间使用率
* 按主机排序的最高平均延迟

以下区域显示在“存储”页面上：

* *利用率*：查看虚拟机主机中的磁盘空间使用情况。
* *总磁盘空间*：所有主机的总和（逻辑磁盘空间）
* *使用的磁盘空间*：所有主机的总和（使用的逻辑磁盘空间）
* *可用的磁盘空间*：总磁盘空间减去使用的磁盘空间
* *已用磁盘的百分比*：已经使用的磁盘空间除以总磁盘空间
* *可用磁盘百分比*：可用磁盘空间除以总磁盘空间

![“容量管理直接附加存储”页面的图像](./media/log-analytics-capacity/oms-capacity04.png)

**磁盘性能**

使用 OMS 可查看磁盘空间的历史使用趋势。 预测功能使用算法预测未来的使用情况。 特别是对空间使用情况，预测功能支持预测耗尽磁盘空间的时间。 此预测有助于规划相应的存储，并了解何时需要购买更多存储。

**预测工具**

使用预测工具可查看磁盘空间利用率的历史趋势。 预测功能还支持预测耗尽磁盘空间的时间。 此预测有助于规划相应的存储，并了解何时需要购买更多存储容量。

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>在“直接附加存储”页上处理项目
1. 在“直接附加存储”仪表板的“利用率”区域中，查看磁盘利用率信息。
2. 在“搜索”页中单击链接项目打开它，然后查看有关详细信息。
3. 在“磁盘性能”区域中，查看磁盘吞吐量和延迟信息。
4. 在“预测工具”中，移动日期滑块，显示将在所选日期使用的容量预测情况。

## <a name="next-steps"></a>后续步骤
* 使用[在 Log Analytics 中进行日志搜索](log-analytics-log-searches.md)查看详细的容量管理数据。



<!--HONumber=Nov16_HO3-->



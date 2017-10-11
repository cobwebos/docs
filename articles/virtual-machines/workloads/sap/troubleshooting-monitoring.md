---
title: "Azure 上的 SAP HANA（大型实例）的故障排除与监视 | Microsoft 文档"
description: "对 Azure 上的 SAP HANA（大型实例）进行故障排除和监视。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee5be707b443cbe42bf4a492d79390e534d4b91f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-troubleshoot-and-monitor-sap-hana-large-instances-on-azure"></a>如何对 Azure 上的 SAP HANA（大型实例）进行故障排除和监视


## <a name="monitoring-in-sap-hana-on-azure-large-instances"></a>在 Azure 上的 SAP HANA（大型实例）中监视

Azure 上的 SAP HANA（大型实例）与其他任何 IaaS 部署一样，都需要监视 OS 与应用程序在执行哪些操作，以及这些组件对以下资源的消耗情况：

- CPU
- 内存
- 网络带宽
- 磁盘空间

对于 Azure 虚拟机，需要确定上述资源类是否足够，或者这些资源是否已耗尽。 下面提供了有关每个不同的类的更多详细信息：

**CPU 资源消耗：**系统实施 SAP 针对 HANA 上的特定工作负荷定义的消耗比，确保有足够的 CPU 资源用于处理内存中存储的数据。 不过，在某些情况下，HANA 可能会由于缺失索引或类似问题，在执行查询时消耗大量 CPU。 这意味着，应该监视 HANA 大型实例单元消耗的 CPU 资源，以及特定 HANA 服务消耗的 CPU 资源。

**内存消耗：**必须从 HANA 内部监视，以及从单元上的 HANA 外部监视。 在 HANA 内部，应该监视数据对 HANA 分配的内存的消耗情况，使之不会超出 SAP 规定的消耗量指导原则。 此外，还需要在大型实例级别监视内存消耗，确保其他已安装的非 HANA 软件不会消耗过多的内存，从而与 HANA 争用内存。


            **网络带宽：**将数据移入 Azure VNet 时，Azure VNet 网关会限制带宽，因此，监视 VNet 中的所有 Azure VM 接收的数据有助于确定与所选 Azure 网关 SKU 的限制还有多大的差距。 在 HANA 大型实例单元上，监视传入和传出的网络流量以及跟踪不同时间处理的数据量确实很有作用。

**磁盘空间：**磁盘空间消耗量通常会随着时间的推移而增大。 原因有多种，但最主要的原因包括：数据量增加、执行事务日志备份、存储跟踪文件，以及执行存储快照。 因此，必须监视磁盘空间用量，管理与 HANA 大型实例单元相关的磁盘空间。

## <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA 端的监视和故障排除

为了有效地分析与 Azure 上的 SAP HANA（大型实例）相关的问题，最好是能够问题的根本原因局限到某个范围。 SAP 发布了大量的文档来帮助用户分析问题。

在以下 SAP 说明中可以找到与 SAP HANA 性能相关的常见问题解答：

- [SAP Note #2222200 - FAQ: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)（SAP 说明 #2222200 - 常见问题：SAP HANA 网络）
- [SAP Note #2100040 - FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)（SAP 说明 #2100040 - 常见问题：SAP HANA CPU）
- [SAP Note #199997 - FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/2177064)（SAP 说明 #199997 - 常见问题：SAP HANA 内存）
- [SAP Note #200000 - FAQ: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000)（SAP 说明 #200000 - 常见问题：SAP HANA 性能优化）
- [SAP Note #199930 - FAQ: SAP HANA Analysis](https://launchpad.support.sap.com/#/notes/1999930)（SAP 说明 #199930 - 常见问题：SAP HANA 分析）
- [SAP Note #2177064 - FAQ: SAP HANA Service Restart and Crashes](https://launchpad.support.sap.com/#/notes/2177064)（SAP 说明 #2177064 - 常见问题：SAP HANA 服务重新启动和崩溃）

**SAP HANA 警报**

第一步是检查当前的 SAP HANA 警报日志。 在 SAP HANA Studio 中，转到“Administration Console: Alerts: Show: all alerts”（管理控制台: 警报: 显示: 所有警报）。 此选项卡会显示超出设置的最小和最大阈值的特定值（可用物理内存、CPU 利用率等）的所有相关 SAP HANA 警报。 默认情况下，检查结果每隔 15 分钟自动刷新一次。

![在 SAP HANA Studio 中，转到“Administration Console: Alerts: Show: all alerts”（管理控制台: 警报: 显示: 所有警报）](./media/troubleshooting-monitoring/image1-show-alerts.png)

**CPU**

对于由于阈值设置不正确而触发的警报，解决方法是重置为默认值，或指定更合理的阈值。

![重置为默认值，或指定更合理的阈值](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

以下警报可能表明 CPU 资源出现问题：

- 主机 CPU 使用率（警报 5）
- 最近的保存点操作（警报 28）
- 保存点持续时间（警报 54）

可以通过以下线索之一来判断 SAP HANA 数据库的 CPU 消耗量偏高：

- 系统针对当前或过去的 CPU 使用率引发了警报 5（主机 CPU 使用率）
- 概述屏幕上显示的 CPU 使用率

![概述屏幕上显示的 CPU 使用率](./media/troubleshooting-monitoring/image3-cpu-usage.png)

“负载”图可能会显示 CPU 消耗量偏高或者在过去偏高：

![“负载”图可能会显示 CPU 消耗量偏高或者在过去偏高](./media/troubleshooting-monitoring/image4-load-graph.png)

由于 CPU 利用率偏高而触发警报的原因有多种，包括但不限于：执行特定的事务、数据加载、挂起作业、长时间运行 SQL 语句，以及查询性能不良（例如，使用 BW on HANA 多维数据集）。

有关详细故障排除步骤，请参阅 [SAP HANA Troubleshooting: CPU Related Causes and Solutions](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false)（SAP HANA 故障排除：CPU 相关的原因和解决方法）站点。

**操作系统**

对于 Linux 上的 SAP HANA，最重要的检查之一是确保禁用透明巨页，具体请参阅 [SAP Note #2131662 - Transparent Huge Pages (THP) on SAP HANA Servers](https://launchpad.support.sap.com/#/notes/2131662)（SAP 说明 #2131662 - SAP HANA 服务器上的透明巨页 (THP)）。

- 可通过以下 Linux 命令检查是否已启用透明巨页：**cat /sys/kernel/mm/transparent\_hugepage/enabled**
- 如果 _always_ 括在方括号中（如下所示），则表示已启用透明巨页：[always] madvise never；如果 _never_ 括在方括号中（如下所示），则表示已禁用透明巨页：always madvise [never]

以下 Linux 命令应该不会返回任何结果：**rpm qa | grep ulimit**。 如果显示 _ulimit_ 已安装，请立即卸载它。

**内存**

你可能会注意到 SAP HANA 数据库分配的内存量超过了预期。 以下警报指示存在内存用量偏高的问题：

- 主机物理内存用量（警报 1）
- 名称服务器的内存用量（警报 12）
- 列存储表的总内存用量（警报 40）
- 服务的内存用量（警报 43）
- 列存储表主存储的内存用量（警报 45）
- 运行时转储文件（警报 46）

有关详细故障排除步骤，请参阅 [SAP HANA Troubleshooting: Memory Problems](http://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)（SAP HANA 故障排除：内存问题）站点。

**网络**

请参阅 [SAP Note #2081065 - Troubleshooting SAP HANA Network](https://launchpad.support.sap.com/#/notes/2081065)（SAP 说明 #2081065 - SAP HANA 网络故障排除）并执行此 SAP 说明中的网络故障排除步骤。

1. 分析服务器与客户端之间的往返时间。
  A. 运行 SQL 脚本 [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_。_
  
2. 分析节点间通信。
  A. 运行 SQL 脚本 [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_。_

3. 运行 Linux 命令 **ifconfig**（输出会显示是否发生了任何丢包情况）。
4. 运行 Linux 命令 **tcpdump**。

此外，请使用开源 [IPERF](https://iperf.fr/) 工具（或类似的工具）来测量实际的应用程序网络性能。

有关详细故障排除步骤，请参阅 [SAP HANA Troubleshooting: Networking Performance and Connectivity Problems](http://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false)（SAP HANA 故障排除：网络性能和连接问题）站点。

**存储**

从最终用户的角度看，如果 I/O 性能有问题，应用程序（或整个系统）会运行缓慢、无响应，甚至呈现死机状态。 在 SAP HANA Studio 中的“Volumes”（卷）选项卡上，可以查看附加的卷，以及每个服务使用了哪些卷。

![在 SAP HANA Studio 中的“Volumes”（卷）选项卡上，可以查看附加的卷，以及每个服务使用了哪些卷](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

在屏幕下半部分显示的附加卷中，可以查看卷的详细信息，如文件和 I/O 统计信息。

![在屏幕下半部分显示的附加卷中，可以查看卷的详细信息，如文件和 I/O 统计信息](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

有关详细故障排除步骤，请参阅 [SAP HANA Troubleshooting: I/O Related Root Causes and Solutions](http://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false)（SAP HANA 故障排除：I/O 相关的根本原因和解决方法）及 [SAP HANA Troubleshooting: Disk Related Root Causes and Solutions](http://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false)（SAP HANA 故障排除：磁盘相关的根本原因和解决方法）站点。

**诊断工具**

通过 HANA\_Configuration\_Minichecks 执行 SAP HANA 运行状况检查。 此工具将返回潜在的严重技术问题，这些问题应该已在 SAP HANA Studio 中以警报的形式指出。

请参阅 [SAP Note #1969700 - SQL statement collection for SAP HANA](https://launchpad.support.sap.com/#/notes/1969700)（SAP 说明 #1969700 - SAP HANA 的 SQL 语句集合），并下载该说明随附的 SQL Statements.zip 文件。 将此 .zip 文件存储在本地硬盘上。

在 SAP HANA Studio 中的“System Information”（系统信息）选项卡上，右键单击“Name”（名称）并选择“Import SQL Statements”（导入 SQL 语句）。

![在 SAP HANA Studio 中的“System Information”（系统信息）选项卡上，右键单击“Name”（名称）并选择“Import SQL Statements”（导入 SQL 语句）](./media/troubleshooting-monitoring/image7-import-statements-a.png)

选择存储在本地的 SQL Statements.zip 文件，随后会导入包含相应 SQL 语句的文件夹。 此时，可以使用这些 SQL 语句运行多种不同的诊断检查。

例如，要测试 SAP HANA 系统复制带宽要求，请在 SQL 控制台中右键单击“Replication: Bandwidth”（复制: 带宽）下面的“Bandwidth”（带宽）语句，并选择“Open”（打开）。

整个 SQL 语句会打开，允许用户更改然后执行输入参数（modification 节）。

![整个 SQL 语句会打开，允许用户更改然后执行输入参数（modification 节）](./media/troubleshooting-monitoring/image8-import-statements-b.png)

另一种做法是右键单击“Replication: Overview”（复制: 概述）下面的语句。 从上下文菜单中选择“Execute”（执行）：

![另一种做法是右键单击“Replication: Overview”（复制: 概述）下面的语句。 从上下文菜单中选择“Execute”（执行）](./media/troubleshooting-monitoring/image9-import-statements-c.png)

随后会返回可帮助进行故障排除的信息：

![随后会返回可帮助进行故障排除的信息](./media/troubleshooting-monitoring/image10-import-statements-d.png)

在 HANA\_Configuration\_Minichecks 中执行与上述相同的操作，检查 _C_（严重）列中出现的所有 _X_ 标记。

示例输出：

用于常规 SAP HANA 检查的 **HANA\_Configuration\_MiniChecks\_Rev102.01+1**。

![用于常规 SAP HANA 检查的 HANA\_Configuration\_MiniChecks\_Rev102.01+1](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

用于概述哪些 SAP HANA 服务当前正在运行的 **HANA\_Services\_Overview**。

![用于概述哪些 SAP HANA 服务当前正在运行的 HANA\_Services\_Overview](./media/troubleshooting-monitoring/image12-services-overview.png)

用于显示 SAP HANA 服务信息（CPU、内存等）的 **HANA\_Services\_Statistics**。

![用于显示 SAP HANA 服务信息的 HANA\_Services\_Statistics ](./media/troubleshooting-monitoring/image13-services-statistics.png)

用于显示有关 SAP HANA 实例的常规信息的 **HANA\_Configuration\_Overview\_Rev110+**。

![用于显示有关 SAP HANA 实例的常规信息的 HANA\_Configuration\_Overview\_Rev110+](./media/troubleshooting-monitoring/image14-configuration-overview.png)

用于检查 SAP HANA 参数的 **HANA\_Configuration\_Parameters\_Rev70+**。

![用于检查 SAP HANA 参数的 HANA\_Configuration\_Parameters\_Rev70+](./media/troubleshooting-monitoring/image15-configuration-parameters.png)


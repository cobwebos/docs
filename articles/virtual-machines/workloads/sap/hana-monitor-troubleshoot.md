---
title: 从 HANA 端针对 Azure SAP HANA（大型实例）进行监视和故障排除 | Microsoft Docs
description: 从 HANA 端针对 Azure SAP HANA（大型实例）进行监视和故障排除。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2c596a876817f0a501025c37e463a7eebb55cf2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099826"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>HANA 端的监视和故障排除

为了有效地分析与 Azure 上的 SAP HANA（大型实例）相关的问题，最好是能够问题的根本原因局限到某个范围。 SAP 发布了大量的文档来帮助用户分析问题。

在以下 SAP 说明中可以找到与 SAP HANA 性能相关的常见问题解答：

- [SAP 说明 #2222200 –常见问题解答:SAP HANA 网络](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP 说明 #2100040 –常见问题解答:SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP 说明 #199997 –常见问题解答:SAP HANA 内存](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP 说明 #200000 –常见问题解答:SAP HANA 性能优化](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP 说明 #199930 –常见问题解答:SAP HANA i/o 分析](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP 说明 #2177064 –常见问题解答:SAP HANA 服务重启和崩溃](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA 警报

第一步是检查当前的 SAP HANA 警报日志。 在 SAP HANA Studio 中, 切换**到 "管理控制台":提醒显示: 所有警报**。 此选项卡会显示超出设置的最小和最大阈值的特定值（可用物理内存、CPU 利用率等）的所有相关 SAP HANA 警报。 默认情况下，检查结果每隔 15 分钟自动刷新一次。

![在 SAP HANA Studio 中, 切换到 "管理控制台":提醒显示: 所有警报](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

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

由于 CPU 使用率较高而触发的警报可能由多种原因引起, 其中包括但不限于执行某些事务、数据加载、没有响应的作业、长时间运行的 SQL 语句以及错误的查询性能 (例如通过 BW on HANA 多维数据集)。

请参阅 SAP HANA 故障排除: [与 CPU 相关的原因](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false)和解决方案站点以获取详细的故障排除步骤。

## <a name="operating-system"></a>操作系统

对于 Linux 上的 SAP HANA，最重要的检查之一是确保禁用透明巨页，具体请参阅 [SAP Note #2131662 - Transparent Huge Pages (THP) on SAP HANA Servers](https://launchpad.support.sap.com/#/notes/2131662)（SAP 说明 #2131662 - SAP HANA 服务器上的透明巨页 (THP)）。

- 可通过以下 Linux 命令检查是否已启用透明巨页：**cat /sys/kernel/mm/transparent\_hugepage/enabled**
- 如果 _always_ 括在方括号中（如下所示），则表示已启用透明巨页：[always] madvise never；如果 _never_ 括在方括号中（如下所示），则表示已禁用透明巨页：always madvise [never]

以下 Linux 命令应该不会返回任何结果：**rpm qa | grep ulimit**。 如果显示 _ulimit_ 已安装，请立即卸载它。

## <a name="memory"></a>内存

你可能会注意到 SAP HANA 数据库分配的内存量超过了预期。 以下警报指示存在内存用量偏高的问题：

- 主机物理内存用量（警报 1）
- 名称服务器的内存用量（警报 12）
- 列存储表的总内存用量（警报 40）
- 服务的内存用量（警报 43）
- 列存储表主存储的内存用量（警报 45）
- 运行时转储文件（警报 46）

请参阅 SAP HANA 故障排除: [内存问题](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false)站点以获取详细的故障排除步骤。

## <a name="network"></a>网络

请参阅 [SAP Note #2081065 - Troubleshooting SAP HANA Network](https://launchpad.support.sap.com/#/notes/2081065)（SAP 说明 #2081065 - SAP HANA 网络故障排除）并执行此 SAP 说明中的网络故障排除步骤。

1. 分析服务器与客户端之间的往返时间。
  A. 运行 SQL 脚本 [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700) _。_
  
2. 分析节点间通信。
  A. 运行 SQL 脚本 [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700) _。_

3. 运行 Linux 命令 **ifconfig**（输出会显示是否发生了任何丢包情况）。
4. 运行 Linux 命令 **tcpdump**。

此外，请使用开源 [IPERF](https://iperf.fr/) 工具（或类似的工具）来测量实际的应用程序网络性能。

请参阅 SAP HANA 故障排除: [网络性能和连接问题](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false)站点以获取详细的故障排除步骤。

## <a name="storage"></a>存储

从最终用户的角度来看, 应用程序 (或整个系统) 运行缓慢, 没有响应, 如果 i/o 性能出现问题, 甚至可能会停止响应。 在 SAP HANA Studio 中的“Volumes”（卷）选项卡上，可以查看附加的卷，以及每个服务使用了哪些卷。

![在 SAP HANA Studio 中的“Volumes”（卷）选项卡上，可以查看附加的卷，以及每个服务使用了哪些卷](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

在屏幕下半部分显示的附加卷中，可以查看卷的详细信息，如文件和 I/O 统计信息。

![在屏幕下半部分显示的附加卷中，可以查看卷的详细信息，如文件和 I/O 统计信息](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

请参阅 SAP HANA 故障排除: [与 i/o 相关的根本原因和解决方案](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false)以及[SAP HANA 故障排除:与磁盘相关的根本原因](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false)和解决方案站点, 详细了解故障排除步骤。

## <a name="diagnostic-tools"></a>诊断工具

通过 HANA\_Configuration\_Minichecks 执行 SAP HANA 运行状况检查。 此工具将返回潜在的严重技术问题，这些问题应该已在 SAP HANA Studio 中以警报的形式指出。

请参阅 [SAP Note #1969700 - SQL statement collection for SAP HANA](https://launchpad.support.sap.com/#/notes/1969700)（SAP 说明 #1969700 - SAP HANA 的 SQL 语句集合），并下载该说明随附的 SQL Statements.zip 文件。 将此 .zip 文件存储在本地硬盘上。

在 SAP HANA Studio 中的“System Information”（系统信息）选项卡上，右键单击“Name”（名称）并选择“Import SQL Statements”（导入 SQL 语句）。

![在 SAP HANA Studio 中的“System Information”（系统信息）选项卡上，右键单击“Name”（名称）并选择“Import SQL Statements”（导入 SQL 语句）](./media/troubleshooting-monitoring/image7-import-statements-a.png)

选择存储在本地的 SQL Statements.zip 文件，随后将导入包含相应 SQL 语句的文件夹。 此时，可以使用这些 SQL 语句运行多种不同的诊断检查。

例如, 若要测试 SAP HANA 系统复制带宽要求, 请右键单击 "复制" 下**的 "带宽" 语句:然后选择 "在 SQL 控制台中打开"。**

整个 SQL 语句会打开，允许用户更改然后执行输入参数（modification 节）。

![整个 SQL 语句会打开，允许用户更改然后执行输入参数（modification 节）](./media/troubleshooting-monitoring/image8-import-statements-b.png)

另一个示例是右键单击 "复制" 下**的语句:概述**。 从上下文菜单中选择“Execute”（执行）：

![另一个示例是右键单击 "复制" 下的语句:叙述. 从上下文菜单中选择“Execute”（执行）](./media/troubleshooting-monitoring/image9-import-statements-c.png)

随后会返回可帮助进行故障排除的信息：

![随后会返回可帮助进行故障排除的信息](./media/troubleshooting-monitoring/image10-import-statements-d.png)

在 HANA\_Configuration\_Minichecks 中执行与上述相同的操作，检查 _C_（严重）列中出现的所有 _X_ 标记。

示例输出：

用于常规 SAP HANA 检查的 **HANA\_Configuration\_MiniChecks\_Rev102.01+1**。

![用于常规 SAP HANA 检查的 HANA\_Configuration\_MiniChecks\_Rev102.01+1](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

用于概述哪些 SAP HANA 服务当前正在运行的 **HANA\_Services\_Overview**。

![用于概述哪些 SAP HANA 服务当前正在运行的 HANA\_Services\_Overview](./media/troubleshooting-monitoring/image12-services-overview.png)

用于显示 SAP HANA 服务信息（CPU、内存等）的 **HANA\_Services\_Statistics**。

![用于显示 SAP HANA 服务信息的 HANA\_Services\_Statistics](./media/troubleshooting-monitoring/image13-services-statistics.png)

用于显示有关 SAP HANA 实例的常规信息的 **HANA\_Configuration\_Overview\_Rev110+** 。

![用于显示有关 SAP HANA 实例的常规信息的 HANA\_Configuration\_Overview\_Rev110+](./media/troubleshooting-monitoring/image14-configuration-overview.png)

用于检查 SAP HANA 参数的 **HANA\_Configuration\_Parameters\_Rev70+** 。

![用于检查 SAP HANA 参数的 HANA\_Configuration\_Parameters\_Rev70+](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**后续步骤**

- 请参阅[使用 STONITH 在 SUSE 中进行高可用性设置](ha-setup-with-stonith.md)。
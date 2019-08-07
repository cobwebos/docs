---
title: 监视器 BUG 软锁定 Azure HDInsight 群集的 CPU 错误
description: 内核一种方法中显示了监视器 BUG 软锁定 CPU
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 9278c174d96cb6b1823c8dbfdcba197b7a3c05cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2019
ms.locfileid: "68829172"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>方案: "监视器:BUG: Azure HDInsight 群集出现软锁定-CPU "错误

本文介绍有关在与 Azure HDInsight 群集交互时遇到的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

内核一种方法包含错误消息: `watchdog: BUG: soft lockup - CPU`。

## <a name="cause"></a>原因

Linux 内核中的[bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437)导致 CPU 软锁定。

## <a name="resolution"></a>解决

应用内核修补程序。 下面的脚本升级 linux 内核, 并在24小时内的不同时间重新启动计算机。 在两个批处理中执行脚本操作。 第一个批处理在头节点之外的所有节点上。 第二个批处理在头节点上。 不要同时在头节点和其他节点上运行。

1. 从 Azure 门户导航到 HDInsight 群集。

1. 请参阅脚本操作。

1. 选择 "**提交新**" 并按如下所示输入输入

    | 属性 | 值 |
    | --- | --- |
    | 脚本类型 | -Custom |
    | 名称 |解决内核软锁定问题 |
    | Bash 脚本 URI |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | 节点类型 |辅助角色, Zookeeper |
    | Parameters |不可用 |

    如果希望在添加新节点时执行脚本, 请选择 "**保留此脚本操作 ...** "。

1. 选择“创建”。

1. 等待执行成功。

1. 按照与步骤3相同的步骤执行头节点上的脚本操作, 但这一次使用节点类型:头.

1. 等待执行成功。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 通过[Azure 社区支持](https://azure.microsoft.com/support/community/)获得 azure 专家的解答。

* 与[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帐户联系, 通过将 Azure 社区连接到适当的资源来改进客户体验: 答案、支持和专家。

* 如果需要更多帮助, 可以从[Azure 门户](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择 "**支持**" 或打开 "**帮助 + 支持**中心"。 有关更多详细信息, 请查看[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅中包含对订阅管理和计费支持的访问权限, 并且通过一个[Azure 支持计划](https://azure.microsoft.com/support/plans/)提供技术支持。

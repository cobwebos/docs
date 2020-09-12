---
title: 监视虚拟机上的改动模式
description: 了解如何使用 Azure Site Recovery 监视受保护的虚拟机上的改动模式
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 09/09/2020
ms.author: sharrai
ms.openlocfilehash: 51f45b5c0b8a2729f64eb376e06875719474cbfc
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663741"
---
# <a name="monitoring-churn-patterns-on-virtual-machines"></a>监视虚拟机上的改动模式

本文概述了可用于监视虚拟机上的改动模式的各种工具。 通过使用正确的工具，可以轻松找到导致较高流失的应用程序，然后执行针对该应用程序的更多操作。

## <a name="for-azure-virtual-machines-windows-or-linux"></a>对于 (Windows 或 Linux 的 Azure 虚拟机) 

如果计算机托管在 Azure 中，并且使用托管或非托管磁盘进行存储，则可以通过跟踪磁盘指标轻松跟踪性能。 这使你可以密切监视和选择正确的磁盘，以适合你的应用程序使用模式。 你还可以使用它来创建警报、诊断和生成自动化。 [了解详细信息](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)。

使用 Azure Site Recovery 保护计算机后，可以通过使用 Azure Monitor 日志和 Log Analytics 来监视计算机。 [了解详细信息](https://docs.microsoft.com/azure/site-recovery/monitor-log-analytics)。

还可以使用一些特定于操作系统的工具。

## <a name="for-windows-machines"></a>对于 Windows 计算机

如果你的计算机是本地计算机，或者在运行 Windows 操作系统的计算机上，则可以使用几个其他工具。

除了检查任务管理器上的磁盘使用情况外，你始终可以参考 **资源监视器** 和 **性能监视器**。 这些工具在 Windows 计算机上已存在。

### <a name="resource-monitor"></a>资源监视器

**资源监视器** 实时显示有关硬件和软件资源使用情况的信息。 若要在 Windows 计算机上运行资源监视器，请按照以下步骤操作–

1. 按 Win + R，然后键入 _resmon_。
1. Resmon （即资源监视器）打开窗口时，将切换到 "磁盘" 选项卡。它提供以下视图-

    ![资源监视器磁盘 "选项卡](./media/monitoring-high-churn/resmon-disk-tab.png)

1. 必须不断监视此选项卡，才能获取清楚的图片。 在上面的示例中，我们看到 _wmiprv.exe_ 改动高。

确定在计算机上导致高改动的应用程序后，您可以采取必要的措施来解决与这些应用程序相关的改动。

### <a name="performance-monitor"></a>性能监视器

**性能监视器** 监视计算机上的各种活动，如 CPU 或内存使用率。 若要在 Windows 计算机上运行性能监视器，请执行以下步骤-

1. 按 Win + R 并键入 _perfmon_。
1. 一旦 perfmon 出现，性能监视器就会打开，它提供以下视图-

    ![性能监视器步骤1](./media/monitoring-high-churn/perfmon-step1.png)

1. 展开右侧的 " **监视工具** " 文件夹，然后单击 "性能监视器"。 这会打开以下视图，其中提供了有关当前性能的实时信息。

    ![性能监视器步骤2](./media/monitoring-high-churn/perfmon-step1.png)

1. 此图当前正在仅监视一个监视器，即 "% Processor Time" –如关系图下方的表所示。 您可以通过单击工具顶部的 **"+"** 来添加更多项进行监视。
1. 下面直观地说明了在向其添加更多计数器后性能监视器的外观-

    ![性能监视器步骤3](./media/monitoring-high-churn/perfmon-step3.png)

[在此处](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/administration/monitor-use-performance-monitor-collect-event-trace-data)了解有关性能监视器的详细信息。

## <a name="for-linux-machines"></a>对于 Linux 计算机

如果你的计算机是本地计算机，或者在运行 Linux 操作系统的计算机上，则可以使用其他一些工具来监视改动模式。

### <a name="iotop"></a>Iotop

其中一个最常用的工具是 _iotop_。 它是用于显示实时磁盘活动的实用程序。 它可以列出正在执行 i/o 的进程，以及它们所使用的磁盘带宽。

打开命令提示符并运行命令 `iotop` 。

### <a name="iostat"></a>IoStat

IoStat 是一个简单的工具，它将收集和显示系统输入和输出存储设备统计信息。 此工具通常用于跟踪存储设备性能问题，包括设备、本地磁盘、远程磁盘。

打开命令提示符并运行命令 `iostat` 。

## <a name="next-steps"></a>后续步骤

了解如何监视 [Azure Monitor](monitor-log-analytics.md)。

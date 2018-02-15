---
title: "在 Azure Site Recovery 中创建并自定义恢复计划进行故障转移和恢复 | Microsoft Docs"
description: "介绍如何在 Azure Site Recovery 中创建并自定义恢复计划，以故障转移和恢复 VM 及物理服务器"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/26/2018
ms.author: raynew
ms.openlocfilehash: 9839a989246b28c1a194b8d1f0e99c1bd80ac2e5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="create-recovery-plans"></a>创建恢复计划


本文介绍如何在 [Azure Site Recovery](site-recovery-overview.md) 中创建和自定义恢复计划。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

 若要创建恢复计划，请执行以下操作：

* 定义要一起信息故障转移再同时启动的计算机组。
* 将计算机一起分组到恢复计划组中，为计算机之间的依赖关系建模。 例如，要故障转移并启动特定的应用程序，可将该应用程序的所有 VM 分组到同一个恢复计划组中。
* 运行故障转移。 可以对恢复计划运行测试、计划或非计划的故障转移。

## <a name="why-use-recovery-plans"></a>为什么要使用恢复计划？

恢复计划有助于通过创建可管理的小型独立单元，计划系统恢复进程。 这些单元通常表示环境中的应用程序。 恢复计划不仅允许定义虚拟机启动的顺序，还有助于在恢复时自动执行常见任务。


**从根本上讲，检查已做好云迁移或灾难恢复准备的一种方法便是确保每个应用程序都是恢复计划的一部分并且每个恢复计划都已进行测试以便恢复到 Microsoft Azure。做了此种准备后，便可放心地将完整的数据中心迁移或故障转移到 Microsoft Azure。**
 
以下是恢复计划的三个主要价值主张：

### <a name="model-an-application-to-capture-dependencies"></a>对应用程序进行建模以捕获依赖关系

恢复计划是一组虚拟机，通常包含一起故障转移的应用程序。 使用恢复计划构造，可以增强此组以捕获应用程序特定的属性。
 
让我们以一个具有以下三层的典型应用程序为例：

* 一个 SQL 后端
* 一个中间件
* 一个 Web 前端

可以自定义恢复计划，确保虚拟机在故障转移后以正确顺序出现。 首先应出现 SQL 后端，然后显示中间件，最后出现 Web 前端。 此顺序可确保应用程序一直工作到最后一个虚拟机出现。 例如，当中间件出现时，它将尝试连接到 SQL 层，恢复计划可确保已运行 SQL 层。 前端服务器最后出现还可确保在所有组件已启动运行并且应用程序已准备好接受请求之前，最终用户不会错误连接到应用程序 URL。 若要生成这些依赖关系，可以自定义恢复计划来添加组。 然后选择虚拟机，更改其组，并在组之间移动。

![示例恢复计划](./media/site-recovery-create-recovery-plans/rp.png)

完成自定义后，便可直观显示恢复的确切步骤。 以下是在恢复计划故障转移期间的步骤执行顺序：

* 首先是关闭步骤，尝试关闭本地虚拟机（在主站点需要继续运行的测试故障转移除外）
* 接下来，它将并行触发恢复计划的所有虚拟机的故障转移。 故障转移步骤从复制的数据中准备虚拟机的磁盘。
* 最后按其顺序启动组执行，启动每个组的虚拟机 - 首先是组 1，然后是组 2，最后是组 3。 如果任何组中有多个虚拟机（例如，负载平衡的 Web 前端），则并行启动这些虚拟机。

**跨越各组进行排序，可确保各个应用程序层之间的依赖关系得到尊重，并且适当情况下进行并行处理来改进应用程序恢复的 RTO。**

   > [!NOTE]
   > 属于单个组的计算机将并行进行故障转移。 而属于不同组的计算机将按照组的顺序进行故障转移。 仅当组 1 所有的虚拟机故障转移并启动后，组 2 中的虚拟机才能开始故障转移。

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>自动执行大多数的恢复任务来减少 RTO

恢复大型应用程序可能是一项复杂的任务。 记住故障转移或迁移后的确切自定义步骤也很难。 有时，常常有人无法意识到应用程序的复杂性，但仍需要触发故障转移。 在混沌的时候记住太多的手动步骤是很困难的，而且极易出错。 恢复计划提供了一种方法，可借助 Microsoft Azure 自动化 Runbook 来自动执行每个步骤所需的操作。 通过 Runbook，可以自动执行常见的恢复任务，比如下面给出的示例。 对于无法自动执行的任务，恢复计划还提供插入手动操作的功能。

* 故障转移之后，Azure 虚拟机的任务 – 通常需要这些才能连接到虚拟机，例如：
    * 故障转移后在虚拟机上创建公共 IP
    * 将 NSG 分配到故障转移之后的虚拟机的 NIC
    * 将负载均衡器添加到可用性集
* 故障转移后虚拟机内部的任务 – 这些任务重新配置应用程序，让应用程序在新的环境中继续正常运行，例如：
    * 修改虚拟机内部的数据库连接字符串
    * 更改 Web 服务器配置/规则

**使用完整的恢复计划，通过自动化 Runbook 自动执行恢复后的任务，可以实现一键故障转移并优化 RTO。**

### <a name="test-failover-to-be-ready-for-a-disaster"></a>测试故障转移以防范灾难

恢复计划可用于触发故障转移或测试故障转移。 在应用程序上完成测试故障转移之后，才能执行故障转移。 测试故障转移有助于检查应用程序是否会在恢复站点上显示。  如果丢失了某些内容，可以轻松触发清理并重做测试故障转移。 多次执行测试故障转移，直到明确知道应用程序可顺利恢复。

![测试恢复计划](./media/site-recovery-create-recovery-plans/rptest.png)

**每个应用程序都是不一样的，需要为每个应用程序都生成自定义恢复计划。另外，在动态数据中心世界中，应用程序及其依赖关系一直在变化。每季度对应用程序进行一次测试故障转移，检查恢复计划是否是最新的。**

## <a name="how-to-create-a-recovery-plan"></a>如何创建恢复计划

1. 单击“恢复计划” > “创建恢复计划”。
   为恢复计划指定一个名称，并指定源和目标。 源位置必须具有已针对故障转移和恢复启用的虚拟机。 根据想要其作为恢复计划的一部分的虚拟机，选择源和目标。 

   |方案                   |Source               |目标           |
   |---------------------------|---------------------|-----------------|
   |Azure 到 Azure             |Azure 区域         |Azure 区域     |
   |VMware 到 Azure            |配置服务器 |Azure            |
   |VMM 到 Azure               |VMM 友好名称    |Azure            |
   |Hyper-v 站点到 Azure      |Hyper-v 站点名称    |Azure            |
   |物理计算机到 Azure |配置服务器 |Azure            |
   |VMM 到 VMM                 |VMM 友好名称    |VMM 友好名称|

   > [!NOTE]
   > 恢复计划可包含具有相同源和目标的虚拟机。 VMware 和 VMM 的虚拟机不能属于相同的恢复计划。 但是，VMware 虚拟机和物理计算机可以添加到相同的计划，因为两者的源都是配置服务器时。

2. 在“选择虚拟机”中，选择要添加到恢复计划中的默认组 (Group 1) 的虚拟机（或复制组）。 仅允许选择在源上（在恢复计划中选定）受到保护并针对目标（在恢复计划中选定）受到保护的虚拟机。

## <a name="how-to-customize-and-extend-recovery-plans"></a>如何自定义和扩展恢复计划

可以通过转到 Site Recovery 恢复计划资源边栏选项卡并单击“自定义”选项卡来自定义和扩展恢复计划。

可自定义和扩展恢复计划：

- **添加新组** - 向默认组添加其他恢复计划组（最多 7 个），并向这些组添加更多计算机或复制组。 组将按添加顺序进行编号。 虚拟机或复制组只能包含在一个恢复计划组中。
- **添加手动操作** - 可以添加要在恢复计划组之前或之后运行的手动操作。 恢复计划运行时，会在手动操作的插入点停止。 将显示对话框，提示指定该手动操作已完成。
- **添加脚本** - 可添加在恢复计划组前或后运行的脚本。 添加脚本时，将为该组添加一组新的操作。 例如，将使用以下名称创建组 1 的一组预先步骤：“组 1：预先步骤”。 该集中将列出所有预先步骤。 仅当已部署 VMM 服务器时，才能在主站点上添加脚本。 [了解详细信息](site-recovery-how-to-add-vmmscript.md)。
- **添加 Azure Runbook** - 可通过 Azure Runbook 扩展恢复计划。 例如，自动执行任务或创建单步恢复。 [了解详细信息](site-recovery-runbook-automation.md)。


## <a name="how-to-add-a-script-runbook-or-manual-action-to-a-plan"></a>如何向计划添加脚本、Runbook 或手动操作

向默认恢复计划组添加 VM 或复制组并创建该计划后，即可向该计划组添加脚本或手动操作。

1. 打开恢复计划。
2. 在“步骤”列表中单击某项，并单击“脚本”或“手动操作”。
3. 指定是要在选定项的前面还是后面添加该脚本或操作。 使用“上移”和“下移”按钮，上下移动脚本的位置。
4. 如果添加 VMM 脚本，请选择“故障转移到 VMM 脚本”。 在“脚本路径”中，键入共享的相对路径。 在以下 VMM 示例中指定路径：**\RPScripts\RPScript.PS1**。
5. 要添加 Azure 自动化 Runbook，请指定该 Runbook 所在的 Azure 自动化帐户，并选择相应的 Azure Runbook 脚本。
6. 若要确保脚本按预期运行，请执行恢复计划的故障转移。

执行故障转移或故障回复时，仅可在以下方案中使用脚本或 Runbook 选项。 手动操作在故障转移和故障回复时均可使用。


|方案               |故障转移 |故障回复 |
|-----------------------|---------|---------|
|Azure 到 Azure         |Runbook |Runbook  |
|VMware 到 Azure        |Runbook |不可用       | 
|VMM 到 Azure           |Runbook |脚本   |
|Hyper-v 站点到 Azure  |Runbook |不可用       |
|VMM 到 VMM             |脚本   |脚本   |


## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-failover.md)如何运行故障转移。

观看本视频，在操作中了解恢复计划。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]

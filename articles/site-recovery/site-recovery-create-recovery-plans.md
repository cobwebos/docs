---
title: "在 Azure Site Recovery 中创建并自定义恢复计划进行故障转移和恢复 | Microsoft Docs"
description: "了解如何在 Azure Site Recovery 中创建和自定义恢复计划。 本文介绍如何故障转移和恢复 VM 及物理服务器。"
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
ms.openlocfilehash: 6ad82a8a2f8e16ab794ba90c109904bd45cb6b89
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-recovery-plan-by-using-site-recovery"></a>使用 Site Recovery 创建恢复计划

本文介绍如何在 [Azure Site Recovery](site-recovery-overview.md) 中创建和自定义恢复计划。

创建恢复计划可完成以下操作：

* 定义要一起进行故障转移再同时启动的计算机组。
* 通过将计算机一起分组到恢复计划组中，为计算机之间的依赖关系建模。 例如，要故障转移并启动特定应用程序，请将该应用程序的所有 VM 包括到同一个恢复计划组中。
* 运行故障转移。 可以对恢复计划运行测试、计划或非计划的故障转移。

## <a name="why-use-a-recovery-plan"></a>使用恢复计划的原因

恢复计划有助于通过创建可管理的小型独立单元，准备系统恢复进程。 这些单元通常表示环境中的应用程序。 恢复计划可帮助定义启动 VM 的顺序。 还可使用恢复计划在恢复过程中自动完成常见任务。

> [!TIP]
> 检查是否已为云迁移或灾难恢复做好准备的一种方法是确保每个应用程序都是恢复计划的一部分。 此外，确保针对恢复到 Azure 对每个恢复计划进行了测试。 做了此种准备后，便可放心地将完整的数据中心迁移或故障转移到 Azure。
 
恢复计划具有三个重要价值主张：

* 对应用程序进行建模以捕获依赖关系。
* 自动执行大多数的恢复任务来减少 RTO。
* 测试故障转移以防范灾难。

### <a name="model-an-application-to-capture-dependencies"></a>对应用程序进行建模以捕获依赖关系

恢复计划是一组虚拟机，通常包含一起故障转移的应用程序。 使用恢复计划构造，可以增强恢复计划组以捕获应用程序特定的属性。 

在本文中，使用了典型的三层应用程序，该应用程序可能具有 SQL 后端、中间件和 Web 前端。 可以自定义恢复计划，帮助确保故障转移后虚拟机按正确的顺序启动。 SQL 后端应首先启动，随后应启动中间件，Web 前端应最后启动。 此顺序可确保应用程序一直工作到最后一个虚拟机启动。 

例如，当中间件启动时，它尝试连接到 SQL 层。 恢复计划可确保 SQL 层已在运行。 前端服务器最后启动还可帮助确保在所有组件已启动运行并且应用程序已准备好接受请求之前，最终用户不会连接到应用程序 URL。 若要生成这些依赖关系，请自定义恢复计划来添加组，然后选择虚拟机。 要在组之间移动虚拟机，请更改虚拟机的组。

![Site Recovery 中示例恢复计划的屏幕截图](./media/site-recovery-create-recovery-plans/rp.png)

完成自定义后，便可直观显示恢复的确切步骤。 以下是在恢复计划故障转移期间的步骤执行顺序：

1. 关闭步骤尝试在本地关闭虚拟机。 （测试故障转移除外，在此期间主站点需要继续运行。）
2. 关闭步骤尝试并行触发恢复计划中所有虚拟机的故障转移。 故障转移步骤使用复制的数据准备虚拟机磁盘。
3. 启动组按其顺序执行，并在每个组中启动虚拟机。 首先执行组 1，然后执行组 2，最后执行组 3。 如果任何组中有多个虚拟机（例如，负载均衡的 Web 前端），则并行启动所有虚拟机。

> [!TIP]
> 跨越各组进行排序，可确保各个应用程序层之间的依赖关系得到尊重。 并且适当情况下进行并行处理来改进应用程序恢复的 RTO。

   > [!NOTE]
   > 属于单个组的计算机将并行进行故障转移。 而属于不同组的计算机按照组的顺序进行故障转移。 组 1 的所有计算机故障转移并启动后，组 2 的计算机才能开始故障转移。

### <a name="automate-most-recovery-tasks-to-reduce-rto"></a>自动执行大多数的恢复任务来减少 RTO

恢复大型应用程序可能是一项复杂的任务。 在混沌的时候记住太多的手动步骤是很困难的，而且极易出错。 可能有人无法意识到应用程序的复杂性，但仍需要触发故障转移。 

可使用恢复计划自动完成执行每个步骤所需的操作。 可通过使用 Azure 自动化 runbook 来设置所需操作。 通过 runbook，可以自动执行常见的恢复任务，比如下面的示例中的任务。 对于无法自动执行的任务，可将手动操作插入恢复计划。

* **故障转移后 Azure 虚拟机上的任务**：通常需要这些任务才能连接到虚拟机。 示例:
    * 故障转移后在虚拟机上创建公共 IP 地址。
    * 将网络安全组分配到故障转移后的虚拟机上的 NIC。
    * 将负载均衡器添加到可用性集。
* **故障转移后虚拟机内部的任务**：这些任务重新配置应用程序，让应用程序在新的环境中继续正常运行。 示例:
    * 修改虚拟机内部的数据库连接字符串。
    * 更改 Web 服务器配置或规则。

> [!TIP]
> 使用完整的恢复计划，通过自动化 Runbook 自动执行恢复后的任务，可以实现一键故障转移并优化 RTO。

### <a name="test-failover-to-be-ready-for-a-disaster"></a>测试故障转移以防范灾难

开使用恢复计划触发故障转移或测试故障转移。 始终都应对应用程序完成测试故障转移之后，再执行故障转移。 测试故障转移有助于检查应用程序是否会在恢复站点上显示。 如果在设置中丢失了某些内容，可以轻松触发清理并重做测试故障转移。 多次执行测试故障转移，直到确定应用程序可顺利恢复。

![Site Recovery 中测试恢复计划示例的屏幕截图](./media/site-recovery-create-recovery-plans/rptest.png)

> [!TIP]
> 由于每个应用程序都是唯一的，需要为每个应用程序生成自定义恢复计划。 
>
> 此外，在当前专注于数据中心的动态环境中，应用程序及其依赖关系频繁更改。 每季度对应用程序进行一次测试故障转移，确保恢复计划是最新的。

## <a name="create-a-recovery-plan"></a>创建恢复计划

1. 选择“恢复计划” > “创建恢复计划”。
   为恢复计划指定一个名称，并指定源和目标。 源位置必须具有已针对故障转移和恢复启用的虚拟机。 根据想要其作为恢复计划的一部分的虚拟机，选择源和目标。 

   |场景                   |Source               |目标           |
   |---------------------------|---------------------|-----------------|
   |Azure 到 Azure             |Azure 区域         |Azure 区域     |
   |VMware 到 Azure            |配置服务器 |Azure            |
   |Virtual Machine Manager (VMM) 到 Azure               |VMM 显示名称    |Azure            |
   |Hyper-V 站点到 Azure      |Hyper-V 站点名称    |Azure            |
   |物理计算机到 Azure |配置服务器 |Azure            |
   |VMM 到 VMM                 |VMM 友好名称    |VMM 显示名称|

   > [!NOTE]
   > 恢复计划可包含具有相同源和目标的虚拟机。 VMware 和 System Center Virtual Machine Manager (VMM) 虚拟机不能在同一恢复计划中。 但是，可以将 VMware 虚拟机和物理机添加到同一恢复计划。 在这种情况下，虚拟机和物理机的源是一个配置服务器。

2. 在“选择虚拟机”下，选择要添加到恢复计划中的默认组（组 1）的虚拟机（或复制组）。 仅可选择在源上（在恢复计划中选定）受到保护并针对目标（在恢复计划中选定）受到保护的虚拟机。

## <a name="customize-and-extend-recovery-plans"></a>自定义和扩展恢复计划

要自定义和扩展恢复计划，请转到 Site Recovery 恢复计划资源窗格。 选择“自定义”选项卡。可使用以下选项自定义和扩展恢复计划：

- **添加新组**：可向默认组添加最多七个额外的恢复计划组。 然后可在这些恢复计划组中添加更多计算机组或复制组。 组将按添加顺序进行编号。 仅可将一个虚拟机或复制组包含在一个恢复计划组中。
- **添加手动操作**：可以添加要在恢复计划组之前或之后运行的手动操作。 恢复计划运行时，会在手动操作的插入点停止。 将显示对话框，提示指定该手动操作已完成。
- **添加脚本**：可添加在恢复计划组之前或之后运行的脚本。 添加脚本时，将为该组添加一组新的操作。 例如，使用以下名称创建了组 1 的一组预先步骤：“组 1：预先步骤”。 该集中将列出所有预先步骤。 仅当已部署 VMM 服务器时，才能在主站点上添加脚本。 有关详细信息，请参阅[向恢复计划添加 VMM 脚本](site-recovery-how-to-add-vmmscript.md)。
- **添加 Azure runbook**：可使用 Azure runbook 扩展恢复计划。 例如，可使用 runbook 自动执行任务或创建单步恢复。 有关详细信息，请参阅[将 Azure 自动化 runbook 添加到恢复计划](site-recovery-runbook-automation.md)。


## <a name="add-a-script-runbook-or-manual-action-to-a-plan"></a>向计划添加脚本、runbook 或手动操作

向默认恢复计划组添加虚拟机或复制组后，即可向恢复计划组添加脚本或手动操作。

1. 打开恢复计划。
2. 在“步骤”列表中，选择一个项。 然后，选择“脚本”或“手动操作”。
3. 指定是要在选定项的前面还是后面添加该脚本或操作。 要将上移或下移脚本，请选择“上移”或“下移”按钮。
4. 如果添加 VMM 脚本，请选择“故障转移到 VMM 脚本”。 在“脚本路径”中，输入共享的相对路径。 例如，\RPScripts\RPScript.PS1。
5. 如果添加自动化 runbook，请指定包含 runbook 的自动化帐户。 然后，选择 Azure runbook 脚本。
6. 要确保脚本按预期运行，请执行恢复计划故障转移。

故障转移或故障回复期间，仅可在以下方案中使用脚本或 runbook 选项。 手动操作在故障转移和故障回复时均可使用。


|场景               |故障转移 |故障回复 |
|-----------------------|---------|---------|
|Azure 到 Azure         |Runbook |Runbook  |
|VMware 到 Azure        |Runbook |不可用       | 
|VMM 到 Azure           |Runbook |脚本   |
|Hyper-V 站点到 Azure  |Runbook |不可用       |
|VMM 到 VMM             |脚本   |脚本   |


## <a name="next-steps"></a>后续步骤

* 详细了解如何[运行故障转移](site-recovery-failover.md)。  
* 观看本视频，了解恢复计划的实际操作：
    
    > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]

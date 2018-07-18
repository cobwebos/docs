---
title: 使用 Azure Site Recovery 中的恢复计划 | Microsoft Docs
description: 了解 Azure Site Recovery 中的恢复计划。
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 011c9acb5d34e15f65b64d59867e7501f0720a08
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920099"
---
# <a name="about-recovery-plans"></a>关于恢复计划

本文介绍了 [Azure Site Recovery](site-recovery-overview.md) 中的恢复计划。

恢复计划将计算机汇集到恢复组中。 可以通过向计划中添加顺序、说明和任务来自定义计划。 在定义计划后，可以根据它运行故障转移。





## <a name="why-use-a-recovery-plan"></a>为何使用恢复计划？

恢复计划通过创建可以进行故障转移的较小独立单元来帮助你定义系统性的恢复流程。 一个单元通常表示你的环境中的一个应用。 恢复计划定义计算机如何进行故障转移，以及它们在故障转移后按什么顺序启动。 使用恢复计划可以执行以下操作：

* 基于应用的依赖项对应用进行建模。
* 自动执行大多数的恢复任务来降低 RTO。
- 通过确保你的应用是恢复计划的一部分来验证你已做好了迁移或灾难恢复准备工作。
* 按恢复计划运行测试故障转移，以确保灾难恢复或迁移按预期工作。


## <a name="model-apps"></a>对应用进行建模

可以规划并创建一个恢复组来捕获特定于应用的属性。 例如，让我们考虑一个典型的三层应用程序，该应用程序具有 SQL Server 后端、中间件和 Web 前端。 通常，你将自定义恢复计划，以便使每层中的计算机在故障转移后按正确顺序启动。

    - SQL 后端应首先启动，接下来是中间件，最后是 Web 前端。
    - 此启动顺序可以确保应用在最后的计算机启动之前一直保持工作。
    - 此顺序可以确保当中间件启动并尝试连接到 SQL Server 层时，SQL Server 层已在运行。 
    - 此顺序还可帮助确保前端服务器最后启动，从而确保在所有组件已启动并运行并且应用已准备好接受请求之前，最终用户不会连接到应用 URL。

若要创建此顺序，请向恢复组中添加组，然后向组中添加计算机。 
    - 如果指定了顺序，则会使用序列。 操作会根据情况并行运行，从而改进应用程序恢复 RTO。
    - 单个组中的计算机将并行进行故障转移。
    - 不同组中的计算机将按组顺序进行故障转移，因此，只有当组 1 中的计算机已进行故障转移并启动后，组 2 中的计算机才会启动。

    ![示例恢复计划](./media/recovery-plan-overview/rp.png)

在此自定义就位后，按恢复计划运行故障转移时会发生以下事情： 

1. 一个关闭步骤尝试关闭本地计算机。 运行测试故障转移时例外，在这种情况下，主站点会继续运行。 
2. 关闭步骤会触发恢复计划中所有计算机的并行故障转移。
3. 故障转移使用复制的数据准备虚拟机磁盘。
4. 启动组按顺序运行，并启动每个组中的计算机。 首先，组 1 运行，然后是组 2，最后是组 3。 如果任何组中有一台以上计算机，则所有计算机将并行启动。


## <a name="automate-tasks"></a>自动执行任务

恢复大型应用程序可能是一项复杂的任务。 手动步骤会使流程容易出错，并且运行故障转移的人可能不了解所有的应用复杂情况。 可以通过用于故障转移到 Azure 的 Azure 自动化 runbook 或通过脚本使用恢复计划来维持秩序，自动执行每个步骤所需的操作。 对于无法自动执行的任务，可以在恢复计划中插入暂停以便手动执行操作。 可以配置许多类型的任务：

* **故障转移后 Azure VM 上的任务**：故障转移到 Azure 时，通常需要执行相关操作，以便可以在故障转移后连接到 VM。 例如： 
    * 在 Azure VM 上创建一个公用 IP 地址。
    * 将一个网络安全组分配给 Azure VM 的网络适配器。
    * 将负载均衡器添加到可用性集。
* **故障转移后 VM 内的任务**：这些任务通常重新配置在计算机上运行的应用，让应用程序能够在新的环境中继续正常运行。 例如：
    * 在计算机内修改数据库连接字符串。
    * 更改 Web 服务器配置或规则。


## <a name="test-failover"></a>测试故障转移

可以使用恢复计划来触发测试故障转移。 请使用以下最佳实践：

- 在运行完整故障转移之前，始终对应用运行测试故障转移。 测试故障转移可帮助你检查应用在恢复站点上是否正常运行。
- 如果你发现遗漏了某些事情，请触发清理，然后重新运行测试故障转移。 
- 多次运行测试故障转移，直到确定应用可顺利恢复。
- 因为每个应用都是唯一的，因此你需要为每个应用构建自定义恢复计划，然后对每个应用运行测试故障转移。
- 应用及其依赖项会经常更改。 若要确保恢复计划是最新的，请每个季度为每个应用运行一次测试故障转移。

    ![Site Recovery 中测试恢复计划示例的屏幕截图](./media/recovery-plan-overview/rptest.png)

## <a name="watch-the-video"></a>观看视频

观看一个快速示例视频，其中展示了如何通过点击来对一个两层 WordPress 应用进行故障转移。
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>后续步骤

- [创建](site-recovery-create-recovery-plans.md)恢复计划。
* 了解如何[运行故障转移](site-recovery-failover.md)。  

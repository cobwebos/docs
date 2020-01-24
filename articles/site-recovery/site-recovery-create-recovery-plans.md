---
title: 在 Azure Site Recovery 中创建/自定义恢复计划
description: 了解如何使用 Azure Site Recovery 服务创建和自定义灾难恢复的恢复计划。
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 6540317324a9f0d9bccc046ecf95824d4128bd09
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705830"
---
# <a name="create-and-customize-recovery-plans"></a>创建和自定义恢复计划

本文介绍如何在[Azure Site Recovery](site-recovery-overview.md)中创建和自定义用于故障转移的恢复计划。 在开始之前，请[详细了解](recovery-plan-overview.md)恢复计划。

## <a name="create-a-recovery-plan"></a>创建恢复计划

1. 在恢复服务保管库中，选择“恢复计划 (Site Recovery)” > “+恢复计划”。
2. 在“创建恢复计划”中，为计划指定一个名称。
3. 根据计划中的计算机选择一个源和目标，对于部署模型，选择“资源管理器”。 源位置必须具有已针对故障转移和恢复启用的计算机。 

    **故障转移** | **数据源** | **Target** 
   --- | --- | ---
   Azure 到 Azure | 选择 Azure 区域 | 选择 Azure 区域
   VMware 到 Azure | 选择配置服务器 | 选择“Azure”
   物理计算机到 Azure | 选择配置服务器 | 选择“Azure”   
   Hyper-V 到 Azure | 选择 Hyper-v 站点名称 | 选择“Azure”
   Hyper-v （由 VMM 管理）到 Azure  | 选择 VMM 服务器 | 选择“Azure”
  
    注意以下事项：
    -  只能将恢复计划用于从源位置故障转移到 Azure。 不能将恢复计划用于从 Azure 进行故障回复。
    - 源位置必须具有已针对故障转移和恢复启用的计算机。 
    - 恢复计划可以包含具有相同源和目标的计算机。 
    - 可以在同一计划中包括 VMM 管理的 VMware Vm 和 Hyper-v Vm。
    - VMware Vm 和物理服务器可以处于同一个计划中。

4. 在“选择项目虚拟机”中，选择要添加到计划中的计算机（或复制组）。 然后单击“确定”。
    - 计算机将添加到计划中的默认组（组 1）。 在故障转移后，此组中的所有计算机将同时启动。
    - 你只能选择位于你指定的源和目标位置的计算机。 
5. 单击“确定”以创建计划。

## <a name="add-a-group-to-a-plan"></a>向计划中添加组

可创建更多组，向各个组中添加计算机，以便可以逐个组指定不同的行为。 例如，你可以为每个组指定组中的计算机在故障转移后应该在何时启动，或者为每个组指定自定义操作。

1. 在“恢复计划”中，右键单击该计划 >“自定义”。 默认情况下，在创建计划后，添加到计划中的所有计算机都位于默认的组 1 中。
2. 单击“+组”。 默认情况下，新组按其添加顺序进行编号。 最多可以添加七个组。
3. 选择要移动到新组的计算机，单击“更改组”，然后选择新组。 或者，右键单击组名称 >“受保护的项”，然后向该组添加计算机。 在一个恢复计划中，一台计算机或一个复制组只能属于一个组。


## <a name="add-a-script-or-manual-action"></a>添加脚本或手动操作

可以通过添加脚本或手动操作来自定义恢复计划。 请注意：

- 如果要复制到 Azure，可以将 Azure 自动化 Runbook 集成到恢复计划中。 [了解详细信息](site-recovery-runbook-automation.md)。
- 如果要复制由 System Center VMM 托管的 Hyper-V VM，可以在本地 VMM 服务器上创建一个脚本，并将该脚本包括在恢复计划中。
- 添加脚本时，将为该组添加一组新的操作。 例如，使用以下名称创建了组 1 的一组预先步骤：“组 1：预先步骤”。 该集中将列出所有预先步骤。 仅当已部署 VMM 服务器时，才能在主站点上添加脚本。
- 如果添加手动操作，当恢复计划运行时，它会在手动操作的插入点停止。 将显示对话框，提示指定该手动操作已完成。
- 若要在 VMM 服务器上创建脚本，请遵循[此文章](hyper-v-vmm-recovery-script.md)中的说明。
- 在故障转移到辅助站点期间，以及从辅助站点故障回复到主站点期间，都可以应用脚本。 支持取决于复制方案：
    
    **方案** | **故障转移** | **故障回复**
    --- | --- | --- 
    Azure 到 Azure  | Runbook | Runbook
    VMware 到 Azure | Runbook | 不可用 
    从包含 VMM 的 Hyper-V 到 Azure | Runbook | 脚本
    Hyper-V 站点到 Azure | Runbook | 不可用
    从 VMM 到辅助 VMM | 脚本 | 脚本

1. 在恢复计划中，单击应将操作添加到的步骤，并指定执行操作的时间：
    1. 如果希望操作在故障转移后在组中的计算机启动之前发生，请选择“添加预操作”。
    1. 如果希望操作在故障转移后在组中的计算机启动之后发生，请选择“添加后操作”。 若要移动操作的位置，请选择“上移”或“下移”按钮。
2. 在“插入操作”中，选择“脚本”或“手动操作”。
3. 如果要添加手动操作，请执行以下操作：
    1. 为操作键入一个名称，然后键入操作说明。 运行故障转移的人员将会看到这些说明。
    1. 指定是否要为所有类型的故障转移（测试、故障转移、计划内故障转移（如果相关））添加手动操作。 然后单击“确定”。
4. 如果要添加脚本，请执行以下操作：
    1. 如果要添加 VMM 脚本，请选择“故障转移到 VMM 脚本”，并在“脚本路径”中键入共享的相对路径。 例如，如果共享位于 \\\<Vmmservername&gt > \MSSCVMMLibrary\RPScripts 上，则指定路径： \Rpscripts\rpscript.ps1。
    1. 如果要添加 Azure 自动化 Runbook，请指定该 Runbook 所在的 **Azure 自动化帐户**，并选择相应的 **Azure Runbook 脚本**。
5. 运行恢复计划的测试故障转移，以确保脚本按预期运行。

## <a name="watch-a-video"></a>观看视频

观看演示了如何构建恢复计划的视频。


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>后续步骤

详细了解如何[运行故障转移](site-recovery-failover.md)。  

    

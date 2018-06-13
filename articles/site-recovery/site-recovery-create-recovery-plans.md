---
title: 在 Azure Site Recovery 中创建并自定义恢复计划进行故障转移和恢复 | Microsoft Docs
description: 了解如何在 Azure Site Recovery 中创建和自定义恢复计划。 本文介绍如何故障转移和恢复 VM 及物理服务器。
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/21/2018
ms.author: raynew
ms.openlocfilehash: e02672ea76eada2d660b20f91c4417019d4efc97
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
ms.locfileid: "30189828"
---
# <a name="create-and-customize-recovery-plans"></a>创建和自定义恢复计划

本文介绍如何在 [Azure Site Recovery](site-recovery-overview.md) 中创建和自定义恢复计划。 在开始之前，请[详细了解](recovery-plan-overview.md)恢复计划。

## <a name="create-a-recovery-plan"></a>创建恢复计划

1. 在恢复服务保管库中，选择“恢复计划 (Site Recovery)” > “+恢复计划”。
2. 在“创建恢复计划”中，为计划指定一个名称。
3. 根据计划中的计算机选择一个源和目标，对于部署模型，选择“资源管理器”。 源位置必须具有已针对故障转移和恢复启用的计算机。 

   **故障转移** | **源** | **目标** 
   --- | --- | ---
   Azure 到 Azure | Azure 区域 |Azure 区域
   VMware 到 Azure | 配置服务器 | Azure
   物理计算机到 Azure | 配置服务器 | Azure   
   由 VMM 托管的 Hyper-V 到 Azure  | VMM 显示名称 | Azure
   从不含 VMM 的 Hyper-V 到 Azure | Hyper-V 站点名称 | Azure
   VMM 到 VMM |VMM 友好名称 | VMM 显示名称 

   > [!NOTE]
   > 恢复计划可以包含具有相同源和目标的计算机。 VMware 和由 VMM 托管的 Hyper-V VM 不能出现在同一计划中。 VMware VM 和物理服务器可以出现在同一计划中，其中，源是配置服务器。

2. 在“选择项目虚拟机”中，选择要添加到计划中的计算机（或复制组）。 然后单击“确定”。
    - 计算机将添加到计划中的默认组（组 1）。 在故障转移后，此组中的所有计算机将同时启动。
    - 你只能选择位于你指定的源和目标位置的计算机。 
1. 单击“确定”以创建计划。

## <a name="add-a-group-to-a-plan"></a>向计划中添加组

你可以创建更多组，向各个组中添加计算机，以便可以逐个组指定不同的行为。 例如，你可以为每个组指定组中的计算机在故障转移后应该在何时启动，或者为每个组指定自定义操作。

1. 在“恢复计划”中，右键单击该计划 >“自定义”。 默认情况下，在创建计划后，添加到计划中的所有计算机都位于默认的组 1 中。
2. 单击“+组”。 默认情况下，新组按其添加顺序进行编号。 最多可以添加七个组。
3. 选择要移动到新组的计算机，单击“更改组”，然后选择新组。 或者，右键单击组名称 >“受保护的项”，然后向该组添加计算机。 在一个恢复计划中，一台计算机或一个复制组只能属于一个组。


## <a name="add-a-script-or-manual-action"></a>添加脚本或手动操作

可以通过添加脚本或手动操作来自定义恢复计划。 请注意：

- 如果要复制到 Azure，可以将 Azure 自动化 Runbook 集成到恢复计划中。 [了解详细信息](site-recovery-runbook-automation.md)。
- 如果要复制由 System Center VMM 托管的 Hyper-V VM，可以在本地 VMM 服务器上创建一个脚本，并将该脚本包括在恢复计划中。
- 添加脚本时，将为该组添加一组新的操作。 例如，使用以下名称创建了组 1 的一组预先步骤：“组 1：预先步骤”。 该集中将列出所有预先步骤。 仅当已部署 VMM 服务器时，才能在主站点上添加脚本。
- 如果添加了手动操作，当恢复计划运行时，它会在你插入了手动操作的点停止。 将显示对话框，提示指定该手动操作已完成。
- 若要在 VMM 服务器上创建脚本，请遵循[此文章](hyper-v-vmm-recovery-script.md)中的说明。
- 在故障转移到辅助站点期间，以及从辅助站点故障回复到主站点期间，都可以应用脚本。 支持取决于复制方案：
    
    **方案** | **故障转移** | **故障回复**
    --- | --- | --- 
    Azure 到 Azure  | Runbook | Runbook
    VMware 到 Azure | Runbook | 不可用 
    从包含 VMM 的 Hyper-V 到 Azure | Runbook | 脚本
    Hyper-V 站点到 Azure | Runbook | 不可用
    从 VMM 到辅助 VMM | 脚本 | 脚本

1. 在恢复计划中，单击应当将操作添加到的步骤，并指定操作应在何时发生：a. 如果希望操作在故障转移后在组中的计算机启动之前发生，请选择“添加预操作”。
    b. 如果希望操作在故障转移后在组中的计算机启动之后发生，请选择“添加后操作”。 若要移动操作的位置，请选择“上移”或“下移”按钮。
2. 在“插入操作”中，选择“脚本”或“手动操作”。
3. 如果要添加手动操作，请执行以下操作。a. 为操作键入一个名称，然后键入操作说明。 运行故障转移的人员将会看到这些说明。
    b. 指定是否要为所有类型的故障转移（测试、故障转移、计划内故障转移（如果相关））添加手动操作。 然后单击“确定”。
4. 如果要添加脚本，请执行以下操作：a. 如果要添加 VMM 脚本，请选择“**故障转移到 VMM 脚本**”，并在“**脚本路径**”中键入共享的相对路径。 例如，如果共享位于 \\<VMMServerName>\MSSCVMMLibrary\RPScripts，请指定以下路径：\RPScripts\RPScript.PS1。
    b. 如果要添加 Azure 自动化 Runbook，请指定该 Runbook 所在的 **Azure 自动化帐户**，并选择相应的 **Azure Runbook 脚本**。
5. 运行恢复计划的测试故障转移，以确保脚本按预期运行。

## <a name="watch-a-video"></a>观看视频

观看演示了如何构建恢复计划的视频。


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>后续步骤

详细了解如何[运行故障转移](site-recovery-failover.md)。  

    

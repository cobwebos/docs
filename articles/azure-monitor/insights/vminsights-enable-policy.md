---
title: 使用 Azure Policy 启用用于 VM 的 Azure Monitor
description: 介绍如何使用 Azure 策略为多个 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 240c96016304c009c36485869ac15f5f38076fb7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088283"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>使用 Azure Policy 启用用于 VM 的 Azure Monitor
本文介绍如何使用 Azure 策略为使用 azure Arc (预览版) 连接的 Azure 虚拟机或混合虚拟机启用用于 VM 的 Azure Monitor。 Azure 策略允许你分配策略定义，用于在 Azure 环境中安装用于 VM 的 Azure Monitor 所需的代理，并在创建每个虚拟机时自动为 Vm 启用监视。 用于 VM 的 Azure Monitor 提供了一项功能，使你能够在你的环境中发现并修正不相容的 Vm。 使用此功能，而不是直接使用 Azure 策略。

如果你不熟悉 Azure 策略，请参阅 [使用 Azure 策略大规模部署 Azure Monitor](../deploy-scale.md)。

> [!NOTE]
> 若要通过 Azure 虚拟机规模集使用 Azure 策略，或直接使用 Azure 策略来启用 Azure 虚拟机，请参阅 [使用 Azure 策略大规模部署 Azure Monitor](../deploy-scale.md#azure-monitor-for-vms)。

## <a name="prerequisites"></a>必备条件
- [创建并配置 Log Analytics 工作区](vminsights-configure-workspace.md)。
- 请参阅 [支持的操作系统](vminsights-enable-overview.md#supported-operating-systems) ，以确保正在启用的虚拟机或虚拟机规模集的操作系统受支持。 


## <a name="azure-monitor-for-vms-initiative"></a>用于 VM 的 Azure Monitor 计划
用于 VM 的 Azure Monitor 提供了用于在 Azure 虚拟机上安装 Log Analytics 代理和依赖项代理的内置策略定义。 该计划 **启用用于 VM 的 Azure Monitor** 包括其中每个策略定义。 将此计划分配给管理组、订阅或资源组，以在该范围内的任何 Windows 或 Linux Azure 虚拟机上自动安装代理。

## <a name="open-policy-coverage-feature"></a>打开策略覆盖率功能
若要访问**用于 VM 的 Azure Monitor 策略覆盖率**，请在 Azure 门户的 " **Azure Monitor** " 菜单中访问**虚拟机**。 选择**其他载入选项**，然后在 "**启用使用策略**" 下**启用**。

[![用于 VM 的 Azure Monitor 的“开始”选项卡](./media/vminsights-enable-at-scale-policy/get-started-page.png)](./media/vminsights-enable-at-scale-policy/get-started-page.png#lightbox)

## <a name="create-new-assignment"></a>创建新分配
如果还没有分配，请单击 " **分配策略**" 来创建一个新分配。

[![创建分配](media/vminsights-enable-at-scale-policy/create-assignment.png)](media/vminsights-enable-at-scale-policy/create-assignment.png#lightbox)

这是在 Azure 策略中分配计划所用的相同页面，只不过它是用所选范围进行硬编码的，以及 **Enable 用于 VM 的 Azure Monitor** 计划定义。 你可以选择更改 **分配名称** 并添加 **描述**。 如果要为作用域提供排除项，请选择 " **排除** "。 例如，你的作用域可能是管理组，你可以指定要从分配中排除的管理组中的订阅。

[![分配计划](media/vminsights-enable-at-scale-policy/assign-initiative.png)](media/vminsights-enable-at-scale-policy/assign-initiative.png#lightbox)

在 " **参数** " 页上，选择要由分配中的所有虚拟机使用的 **Log Analytics 工作区** 。 如果要为不同的虚拟机指定不同的工作区，则必须创建多个分配，每个分配都有自己的作用域。 

   > [!NOTE]
   > 如果工作区超出分配范围，则将 *Log Analytics 参与者*权限授予策略分配的主体 ID。 如果不这样做，可能会看到类似于下面的部署失败 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...`

[![工作区](media/vminsights-enable-at-scale-policy/assignment-workspace.png)](media/vminsights-enable-at-scale-policy/assignment-workspace.png#lightbox)

单击 " **查看** " 以查看分配的详细信息，然后单击 " **创建** " 来创建该分配。 此时请勿创建修正任务，因为你很可能需要多次修正任务来启用现有的虚拟机。 请参阅下面的 [更正符合性结果](#remediate-compliance-results) 。

## <a name="review-compliance"></a>查看符合性
创建分配后，你可以查看和管理跨管理组和订阅的 **启用用于 VM 的 Azure Monitor** 计划的覆盖范围。 这将显示每个管理组或订阅中存在多少个虚拟机以及其符合性状态。

[![用于 VM 的 Azure Monitor 的“管理策略”页](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png#lightbox)


下表提供了此视图中信息的说明。

| 函数 | 说明 | 
|----------|-------------| 
| **范围** | 你拥有的或者继承了其访问权限的管理组和订阅，可以通过管理组层次结构向下钻取。|
| **角色** | 作用域中的角色，可以是读取者、所有者或参与者。 如果你有权访问订阅，但不能访问其所属的管理组，则此字段将为空。 此角色确定你可以查看的数据和可执行的操作， (所有者) 、编辑它们或查看符合性。 |
| **VM 总数** | 无论状态如何，该范围中的 Vm 的总数。 对于管理组，这是在订阅或子管理组下嵌套的 Vm 总计。 |
| **分配覆盖范围** | 计划涵盖的 Vm 的百分比。 |
| **分配状态** | **成功** -范围中的所有 vm 都部署了 Log Analytics 和依赖项代理。<br>**警告** -订阅不在管理组下。<br>**未启动** -添加了新分配。<br>**锁定** -你没有足够的权限来管理组。<br>**空白** -不存在 vm 或未分配策略。 |
| **合规的 VM 数** | 符合的 Vm 数，这是安装了 Log Analytics 代理和依赖关系代理的 Vm 的数目。 如果没有分配、作用域中没有 Vm 或权限不正确，则此值为空。 |
| **遵从性** | 整体合规性是不同合规资源的总和除以所有唯一资源。 |
| **符合性状态** | **合规** -范围内虚拟机中的所有 vm 都已将 Log Analytics 和依赖关系代理部署到这些虚拟机，或者在该分配的作用域中的任何新 vm 尚未进行评估。<br>不**符合**-有一些 vm 已被评估，但未启用并且可能需要进行修正。<br>**未启动** -添加了新分配。<br>**锁定** -你没有足够的权限来管理组。<br>**空白** -未分配策略。  |


分配计划时，在分配中选择的作用域可能是列出的作用域或其中的一个子集。 例如，你可能为某个订阅创建了分配（策略范围），但没有为管理组创建分配（覆盖范围）。 在这种情况下，" **分配覆盖率** " 的值指示计划范围内的 vm 除以覆盖率范围内的 vm。 另举一例。你可能从策略范围中排除了一些 VM、资源组或订阅。 如果该值为空白，表示策略或计划不存在，或者你没有权限。 “分配状态”下会提供信息。****


## <a name="remediate-compliance-results"></a>修正符合性结果
计划将在创建或修改时应用到虚拟机，但不会应用到现有 Vm。 如果分配不显示100% 的符合性，请创建更正任务以评估和启用现有 Vm，选择 " **查看符合性** "，方法是选择省略号 ( ") "。

[![查看符合性](media/vminsights-enable-at-scale-policy/view-compliance.png)](media/vminsights-enable-at-scale-policy/view-compliance.png#lightbox)

" **符合性** " 页列出与指定的筛选器匹配的分配以及是否符合该筛选器。 单击分配以查看其详细信息。

[![Azure VM 的策略合规性](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png#lightbox)

" **计划相容性** " 页列出了计划中的策略定义，以及每个策略的符合性。

[![合规性详细信息](media/vminsights-enable-at-scale-policy/compliance-details.png)](media/vminsights-enable-at-scale-policy/compliance-details.png#lightbox)

单击策略定义可查看其详细信息。 策略定义将显示为 "不符合" 的方案包括：

* 未部署 Log Analytics 代理或依赖项代理。 创建要缓解的补救任务。
* VM 映像 (OS) 未在策略定义中识别。 部署策略标准仅包含通过已知 Azure VM 映像部署的 VM。 请查看文档，了解 VM OS 是否受支持。
* Vm 未登录到指定的 Log Analytics 工作区。 计划范围内的某些 Vm 连接到 Log Analytics 工作区，而不是在策略分配中指定的工作区。

[![策略符合性详细信息](media/vminsights-enable-at-scale-policy/policy-compliance-details.png)](media/vminsights-enable-at-scale-policy/policy-compliance-details.png#lightbox)

若要创建纠正任务来缓解合规性问题，请单击 " **创建修正任务**"。 

[![新的补救任务](media/vminsights-enable-at-scale-policy/new-remediation-task.png)](media/vminsights-enable-at-scale-policy/new-remediation-task.png#lightbox)

单击 " **修正** " 以创建修正任务，然后 **修正** 以启动它。 您很可能需要创建多个修正任务，每个策略定义各有一个。 不能为计划创建补救任务。

[![屏幕截图显示监视器的 "策略更正" 窗格 |虚拟机。](media/vminsights-enable-at-scale-policy/remediation.png)](media/vminsights-enable-at-scale-policy/remediation.png#lightbox)


补救任务完成后，Vm 应符合为用于 VM 的 Azure Monitor 安装和启用的代理。 

## <a name="next-steps"></a>后续步骤

现已为虚拟机启用了监视，可在用于 VM 的 Azure Monitor 中使用此信息进行分析。 

- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 
- 若要通过 VM 的性能了解瓶颈和整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)。 

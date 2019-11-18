---
title: 使用 Azure Policy 启用用于 VM 的 Azure Monitor | Microsoft Docs
description: 本文介绍如何使用 Azure Policy 为多个 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 4a89eb36c9aa7369d6145304b572b4245cef3483
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109320"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>使用 Azure Policy 启用用于 VM 的 Azure Monitor（预览版）

本文介绍如何使用 Azure Policy 为 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor（预览版）。 在此过程结束时，即已成功配置并启用 Log Analytics 和依赖项代理，同时已识别不合规的虚拟机。

若要为所有 Azure 虚拟机或虚拟机规模集发现、管理和启用用于 VM 的 Azure Monitor，可以使用 Azure Policy 或 Azure PowerShell。 我们建议使用 Azure Policy 方法，因为这样可以管理策略定义，以便有效地监管订阅，确保始终保持合规，并自动启用新预配的 VM。 这些策略定义：

* 部署 Log Analytics 代理和 Dependency Agent。
* 报告合规性结果。
* 修正不合规的 VM。

如果你想要使用 Azure PowerShell 或 Azure 资源管理器模板完成这些任务，请参阅[使用 Azure PowerShell 或 Azure 资源管理器模板启用用于 VM 的 Azure Monitor（预览版）](vminsights-enable-at-scale-powershell.md)。

## <a name="manage-policy-coverage-feature-overview"></a>“管理策略覆盖范围”功能概述

最初，Azure Policy 中用于管理和部署用于 VM 的 Azure Monitor 策略定义的体验只能在 Azure Policy 中使用。 “管理策略覆盖范围”功能简化了大规模发现、管理和启用“启用用于 VM 的 Azure Monitor”计划（包括前面所述的策略定义）的过程。 可以通过用于 VM 的 Azure Monitor 中的“开始”选项卡访问此新功能。 选择“管理策略覆盖范围”打开“用于 VM 的 Azure Monitor 策略覆盖范围”页。

![用于 VM 的 Azure Monitor 的“开始”选项卡](./media/vminsights-enable-at-scale-policy/get-started-page.png)

在此处，可以检查和管理各个管理组与订阅中的计划的覆盖范围。 可以了解每个管理组和订阅中的 VM 数目及其合规状态。

![用于 VM 的 Azure Monitor 的“管理策略”页](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

此信息可帮助你在一个中心位置规划和执行用于 VM 的 Azure Monitor 的监管方案。 尽管在将某个策略或方案分配到某个范围时 Azure Policy 会提供合规性视图，但在此新页面中，可以查看尚未分配的策略或计划，并就地进行分配。 所有操作（例如分配、查看和编辑）将直接重定向到 Azure Policy。 “用于 VM 的 Azure Monitor 策略覆盖范围”页是一个扩展的集成式体验，仅适用于“启用用于 VM 的 Azure Monitor”计划。

从此页中，还可以配置用于 VM 的 Azure Monitor 的 Log Analytics 工作区，包括：

- 安装服务映射解决方案。
- 启用性能图表、工作簿和自定义日志查询与警报使用的操作系统性能计数器。

![用于 VM 的 Azure Monitor - 配置工作区](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此选项与任何策略操作都不相关。 使用此选项可以轻松满足启用用于 VM 的 Azure Monitor 所需的[先决条件](vminsights-enable-overview.md)。  

### <a name="what-information-is-available-on-this-page"></a>此页提供哪些信息？

下表详细提供了策略覆盖范围页上显示的信息及其解释。

| 函数 | 说明 | 
|----------|-------------| 
| **范围** | 你拥有的或者继承了其访问权限的管理组和订阅，可以通过管理组层次结构向下钻取。|
| **角色** | 你在该范围内的角色，可能是读取者、所有者或参与者。 在某些情况下，此项可能显示为空白，表示你可能有权访问订阅，但无权访问该订阅所属的管理组。 其他列中的信息根据角色的不同而异。 在确定可以查看哪些数据和执行哪些操作（包括分配策略或计划（所有者）、对其进行编辑，或查看合规性）时，角色非常关键。 |
| **VM 总数** | 该范围内的 VM 数目。 对于管理组，该数目是嵌套在订阅或子管理组下的 VM 数之和。 |
| **分配覆盖范围** | 策略或计划覆盖的 VM 百分比。 |
| **分配状态** | 有关策略或计划分配状态的信息。 |
| **合规的 VM 数** | 符合策略或计划的 VM 数目。 对于“启用用于 VM 的 Azure Monitor”计划，这是包含 Log Analytics 代理和依赖项代理的 VM 数目。 在某些情况下，可能会由于没有分配、VM 或足够的权限，此项显示为空白。 “合规状态”下会提供信息。 |
| **合规性** | 整体合规性是不同合规资源的总和除以所有唯一资源。 |
| **符合性状态** | 有关策略或计划分配的合规状态的信息。|

分配策略或计划时，在分配中选择的范围可能是列出的范围或者其子集。 例如，你可能为某个订阅创建了分配（策略范围），但没有为管理组创建分配（覆盖范围）。 在这种情况下，“分配覆盖范围”的值表示策略或计划范围内的 VM 数除以覆盖范围内的 VM 数。 另举一例。你可能从策略范围中排除了一些 VM、资源组或订阅。 如果该值为空白，表示策略或计划不存在，或者你没有权限。 “分配状态”下会提供信息。

## <a name="enable-by-using-azure-policy"></a>通过使用 Azure Policy 启用

若要在租户中使用 Azure Policy 启用用于 VM 的 Azure Monitor，请执行以下操作：

- 将计划分配到范围：管理组、订阅或资源组。
- 检查和修正合规性结果。

有关分配 Azure Policy 的详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md#policy-assignment)，并在继续操作前查看[管理组概述](../../governance/management-groups/overview.md)。

### <a name="policies-for-azure-vms"></a>Azure VM 的策略

下表列出了 Azure VM 的策略定义：

|名称 |说明 |类型 |
|-----|------------|-----|
|\[预览\]：启用用于 VM 的 Azure Monitor |在指定范围（管理组、订阅或资源组）中启用用于虚拟机的 Azure Monitor。 将 Log Analytics 工作区用作参数。 |计划 |
|\[预览\]：审核依赖关系代理部署–未列出 VM 映像（OS） |如果 VM 映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |
|\[预览\]：审核 Log Analytics 代理部署–未列出 VM 映像（OS） |如果 VM 映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |
|\[预览\]：部署适用于 Linux Vm 的依赖关系代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux VM 部署依赖项代理。 |策略 |
|\[预览版\]：为 Windows Vm 部署依赖关系代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows VM 部署依赖项代理。 |策略 |
|\[预览\]：部署适用于 Linux Vm 的 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux VM 部署 Log Analytics 代理。 |策略 |
|\[预览\]：部署适用于 Windows Vm 的 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows VM 部署 Log Analytics 代理。 |策略 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集的策略

下表列出了 Azure 虚拟机规模集的策略定义：

|名称 |说明 |类型 |
|-----|------------|-----|
|\[预览\]：启用虚拟机规模集的 Azure Monitor |在指定范围（管理组、订阅或资源组）中为虚拟机规模集启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 注意：如果规模集升级策略设置为 "手动"，则通过对其调用升级来将扩展应用到集中的所有 Vm。 在 CLI 中，这是 `az vmss update-instances`。 |计划 |
|\[预览\]：虚拟机规模集中的审核依赖关系代理部署–未列出 VM 映像（OS） |如果虚拟机规模集映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |
|\[预览版\]：在虚拟机规模集中审核 Log Analytics 代理部署–未列出 VM 映像（OS） |如果虚拟机规模集映像 (OS) 未在列表中定义且未安装代理，则报告 VM 不合规。 |策略 |
|\[预览\]：部署适用于 Linux 虚拟机规模集的依赖关系代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux 虚拟机规模集部署依赖项代理。 |策略 |
|\[预览\]：部署 Windows 虚拟机规模集的依赖关系代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows 虚拟机规模集部署依赖项代理。 |策略 |
|\[预览\]：部署适用于 Linux 虚拟机规模集的 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Linux 虚拟机规模集部署 Log Analytics 代理。 |策略 |
|\[预览\]：为 Windows 虚拟机规模集部署 Log Analytics 代理 |如果 VM 映像 (OS) 在列表中定义但未安装代理，请为 Windows 虚拟机规模集部署 Log Analytics 代理。 |策略 |

以下介绍独立策略（未包含在计划中）：

|名称 |说明 |类型 |
|-----|------------|-----|
|\]\[预览：审核 VM Log Analytics 工作区–报表不匹配 |如果 VM 未登录到策略或计划分配中指定的 Log Analytics 工作区，则报告 VM 不合规。 |策略 |

### <a name="assign-the-azure-monitor-initiative"></a>分配 Azure Monitor 计划

若要通过“用于 VM 的 Azure Monitor 策略覆盖范围”页创建策略分配，请执行以下步骤。 若要了解如何完成这些步骤，请参阅 [从 Azure 门户创建策略分配](../../governance/policy/assign-policy-portal.md)。

分配策略或计划时，在分配中选择的范围可能是此处列出的范围或者其子集。 例如，你可能为订阅创建了分配（策略范围），但没有为管理组创建分配（覆盖范围）。 在这种情况下，覆盖范围百分比表示策略或计划范围内的 VM 数除以覆盖范围内的 VM 数。 另举一例。你可能从策略范围中排除了一些 VM、资源组或订阅。 如果该值为空白，表示策略或计划不存在，或者你没有权限。 “分配状态”下会提供信息。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，选择“监视”。 

3. 在“见解”分区中选择“虚拟机(预览)”。
 
4. 选择 "**开始**" 选项卡。在 "" 页上，选择 "**管理策略覆盖率**"。

5. 从表中选择一个管理组或订阅。 选择 "**作用域**"，选择省略号（...）。在此示例中，作用域将策略分配限制为一组用于强制执行的虚拟机。

6. 在“Azure Policy 分配”页上，已经预先填充了“启用用于 VM 的 Azure Monitor”计划。 
    “分配名称”框中会自动填充计划名称，但可对其进行更改。 还可以添加可选的说明。 “分配者”框根据登录的用户自动填充。 此值是可选的。

7. （可选）若要从范围中删除一个或多个资源，请选择“排除项”。

8. 在受支持区域的“Log Analytics 工作区”下拉列表中，选择一个工作区。

   > [!NOTE]
   > 如果工作区超出分配范围，则将 *Log Analytics 参与者*权限授予策略分配的主体 ID。 如果不这样做，可能会看到部署失败，如 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` 授予访问权限，请查看[如何手动配置托管标识](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  "**托管标识**" 复选框处于选中状态，因为指定的计划包括具有*deployIfNotExists*效果的策略。
    
9. 在“管理标识位置”下拉列表中，选择适当的区域。

10. 选择“分配”。

创建分配后，“用于 VM 的 Azure Monitor 策略覆盖范围”页会更新“分配覆盖范围”、“分配状态”、“合规的 VM 数”和“合规状态”，以反映所做的更改。 

以下矩阵映射了计划每种可能的合规状态。  

| 符合性状态 | 说明 | 
|------------------|-------------|
| **合规** | 该范围内的所有 VM 都部署了 Log Analytics 和依赖项代理。|
| **不合规** | 该范围内并非所有 VM 都部署了 Log Analytics 和依赖项代理，可能需要修正。|
| **未启动** | 添加了新的分配。 |
| **锁定** | 你对管理组没有足够的特权。<sup>1</sup> | 
| **空白** | 未分配策略。 | 

<sup>1</sup> 如果你对管理组没有访问权限，可请求所有者提供访问权限。 或者，通过子管理组或订阅查看合规性和管理分配。 

下表映射了计划的每种可能的分配状态。

| 分配状态 | 说明 | 
|------------------|-------------|
| **Success** | 该范围内的所有 VM 都部署了 Log Analytics 和依赖项代理。|
| **警告** | 订阅不在管理组下。|
| **未启动** | 添加了新的分配。 |
| **锁定** | 你对管理组没有足够的特权。<sup>1</sup> | 
| **空白** | 没有 VM，或者未分配策略。 | 
| **操作** | 分配策略或编辑分配。 | 

<sup>1</sup> 如果你对管理组没有访问权限，可请求所有者提供访问权限。 或者，通过子管理组或订阅查看合规性和管理分配。

## <a name="review-and-remediate-the-compliance-results"></a>检查和修正合规结果

以下示例适用于 Azure VM，但也适用于虚拟机规模集。 若要了解如何查看合规性结果，请参阅[识别不合规性结果](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)。 在“用于 VM 的 Azure Monitor 策略覆盖范围”页上，从表中选择一个管理组或订阅。 选择省略号 (...)，然后选择“查看合规性”。   

![Azure VM 的策略合规性](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

根据计划中包含的策略的结果，VM 在以下情况中报告为不合规：

* 未部署 Log Analytics 代理或依赖项代理。  
    这对于已经拥有 VM 的范围来说是典型情况。 若要缓解此问题，请通过在不符合策略上[创建修正任务](../../governance/policy/how-to/remediate-resources.md)来部署所需的代理。  
    - \[预览\]：部署适用于 Linux Vm 的依赖关系代理
    - \[预览版\]：为 Windows Vm 部署依赖关系代理
    - \[预览\]：部署适用于 Linux Vm 的 Log Analytics 代理
    - \[预览\]：部署适用于 Windows Vm 的 Log Analytics 代理

* VM 映像 (OS) 未在策略定义中识别。  
    部署策略标准仅包含通过已知 Azure VM 映像部署的 VM。 请查看文档，了解 VM OS 是否受支持。 如果不受支持，则复制部署策略并更新或修改它来使映像合规。  
    - \[预览\]：审核依赖关系代理部署–未列出 VM 映像（OS）
    - \[预览\]：审核 Log Analytics 代理部署–未列出 VM 映像（OS）

* VM 未登录到指定的 Log Analytics 工作区。  
    可能存在计划范围中的某些 VM 登录到策略部署中指定的 Log Analytics 工作区以外的 Log Analytics 工作区的情况。 此策略是用于确定向不合规工作区报告的 VM 的工具。  
    - \]\[预览：审核 VM Log Analytics 工作区–报表不匹配

## <a name="edit-an-initiative-assignment"></a>编辑计划分配

将计划分配到管理组或订阅后，随时可以编辑该计划，以修改以下属性：

- 分配名称
- 说明
- 分配者
- Log Analytics 工作区
- 异常

## <a name="next-steps"></a>后续步骤

既然虚拟机已启用了监视，此信息在用于 VM 的 Azure Monitor 中可供分析。 

- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 

- 若要通过 VM 的性能了解瓶颈和整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)。 

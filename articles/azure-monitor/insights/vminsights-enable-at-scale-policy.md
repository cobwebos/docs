---
title: 使用 Azure 策略启用用于 VM 的 Azure Monitor |Microsoft Docs
description: 本文介绍如何使用 Azure 策略为多个 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 068d320c85a1e10027c92b92c1b3c205a999bed7
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553831"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>使用 Azure 策略启用用于 VM 的 Azure Monitor （预览版）

本文介绍如何使用 Azure 策略为 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor （预览版）。 在此过程结束时，你将成功配置启用 Log Analytics 和依赖项代理，并识别不符合的虚拟机。

若要发现、管理和启用所有 Azure 虚拟机或虚拟机规模集的用于 VM 的 Azure Monitor，可以使用 Azure 策略或 Azure PowerShell。 Azure 策略是我们建议的方法，因为你可以通过管理策略定义来有效地管理你的订阅，以确保一致的符合性和自动启用新预配的 Vm。 这些策略定义：

* 部署 Log Analytics 代理和 Dependency Agent。
* 报告合规性结果。
* 针对不符合要求的 Vm 进行修正。

如果你有兴趣使用 Azure PowerShell 或 Azure 资源管理器模板来完成这些任务，请参阅[使用 Azure PowerShell 或 azure 资源管理器模板启用用于 VM 的 Azure Monitor （预览版）](vminsights-enable-at-scale-powershell.md)。

## <a name="manage-policy-coverage-feature-overview"></a>管理策略覆盖功能概述

最初，用于管理和部署用于 VM 的 Azure Monitor 策略定义的 Azure 策略的经验是从 Azure 策略中独占完成的。 利用 "管理策略覆盖范围" 功能，可以更简单、更轻松地按比例发现、管理和启用**用于 VM 的 Azure Monitor**计划，其中包括前面提到的策略定义。 你可以从用于 VM 的 Azure Monitor 中的 "**入门**" 选项卡访问此新功能。 选择 "**管理策略覆盖率**"，打开 "**用于 VM 的 Azure Monitor 策略覆盖率**" 页。

![从 Vm 开始 Azure Monitor "选项卡](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

在这里，你可以查看和管理跨管理组和订阅的计划的覆盖范围。 你可以了解每个管理组和订阅中有多少个 Vm 以及其符合性状态。

![用于 VM 的 Azure Monitor 管理策略 "页](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

此信息可帮助您从一个中心位置规划和执行用于 VM 的 Azure Monitor 的监管方案。 当策略或计划分配到作用域时，Azure 策略会提供符合性视图，你可以在此新页面上发现未分配策略或计划的位置，并将其分配到位。 所有操作（例如分配、查看和编辑）都直接重定向到 Azure 策略。 "**用于 VM 的 Azure Monitor 策略覆盖范围**" 页面是仅**支持计划用于 VM 的 Azure Monitor**的已展开且集成的体验。

在此页上，还可以为用于 VM 的 Azure Monitor 配置 Log Analytics 工作区，这是：

- 安装服务映射解决方案。
- 启用性能图表、工作簿以及自定义日志查询和警报使用的操作系统性能计数器。

![用于 VM 的 Azure Monitor 配置工作区](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

此选项与任何策略操作无关。 它可提供一种简单的方法来满足启用用于 VM 的 Azure Monitor 所需的[先决条件](vminsights-enable-overview.md)。  

### <a name="what-information-is-available-on-this-page"></a>此页提供了哪些信息？

下表提供了 "策略覆盖范围" 页上提供的信息的细目，以及如何对其进行解释。

| 函数 | 描述 | 
|----------|-------------| 
| **范围** | 您拥有或继承的管理组和订阅，可以向下钻取管理组层次结构。|
| **角色** | 角色的作用域，该作用域可能是读取者、所有者或参与者。 在某些情况下，它可能会显示为空，指示你可能有权访问订阅，但不能访问其所属的管理组。 其他列中的信息根据你的角色而有所不同。 角色是确定你可以查看的数据的关键，以及你可以在分配策略或计划（所有者）、编辑它们或查看符合性方面执行的操作。 |
| **Vm 总数** | 该范围下的 Vm 数量。 对于管理组，它是在订阅或子管理组下嵌套的 Vm 的总和。 |
| **分配覆盖率** | 策略或计划所涵盖的 Vm 的百分比。 |
| **分配状态** | 有关策略或计划分配状态的信息。 |
| **兼容的 Vm** | 符合策略或计划的 Vm 的数量。 对于**启用用于 VM 的 Azure Monitor**的计划，这是同时具有 Log Analytics 代理和依赖项代理的 vm 的数目。 在某些情况下，由于没有分配、没有 Vm 或权限不足，它可能会显示为空白。 在**符合性状态**下提供信息。 |
| **合规性** | 总体符合性数字是相容的不同资源的总和除以所有不同资源的总和。 |
| **符合性状态** | 有关策略或计划分配的符合性状态的信息。|

分配策略或计划时，在分配中选择的作用域可能是列出的作用域或其中的一个子集。 例如，你可能已为订阅（策略作用域）创建分配，而不是管理组（覆盖范围）。 在这种情况下，"**分配覆盖率**" 的值指示策略或计划范围内的 vm 除以覆盖率范围内的 vm。 在另一种情况下，你可能已从策略作用域中排除了某些 Vm、资源组或订阅。 如果该值为空，则表示策略或计划不存在，或者你没有权限。 在**分配状态**下提供信息。

## <a name="enable-by-using-azure-policy"></a>通过使用 Azure Policy 启用

若要在租户中使用 Azure Policy 启用用于 VM 的 Azure Monitor，请执行以下操作：

- 将计划分配到作用域：管理组、订阅或资源组。
- 查看并修正符合性结果。

有关分配 Azure Policy 的详细信息，请参阅 [Azure Policy 概述](../../governance/policy/overview.md#policy-assignment)，并在继续操作前查看[管理组概述](../../governance/management-groups/overview.md)。

### <a name="policies-for-azure-vms"></a>Azure Vm 的策略

下表列出了 Azure VM 的策略定义。

|名称 |描述 |Type |
|-----|------------|-----|
|\[预览\]：启用用于 VM 的 Azure Monitor |为指定作用域（管理组、订阅或资源组）中的虚拟机启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 |计划 |
|\[Preview \]：审核依赖关系代理部署–未列出 VM 映像（OS） |如果未在列表中定义 VM 映像（OS），并且未安装代理，则将 Vm 报告为不符合要求。 |策略 |
|\] \[Preview：审核 Log Analytics 代理部署–未列出 VM 映像（OS） |如果未在列表中定义 VM 映像（OS），并且未安装代理，则将 Vm 报告为不符合要求。 |策略 |
|\[Preview \]：部署适用于 Linux Vm 的依赖关系代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则部署适用于 Linux Vm 的依赖关系代理。 |策略 |
|\[Preview \]：为 Windows Vm 部署依赖关系代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则部署适用于 Windows Vm 的依赖关系代理。 |策略 |
|\[Preview \]：部署适用于 Linux Vm 的 Log Analytics 代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则部署适用于 Linux Vm 的 Log Analytics 代理。 |策略 |
|\[Preview \]：部署适用于 Windows Vm 的 Log Analytics 代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则部署适用于 Windows Vm 的 Log Analytics 代理。 |策略 |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Azure 虚拟机规模集的策略

下表列出了 Azure 虚拟机规模集的策略定义。

|名称 |描述 |Type |
|-----|------------|-----|
|\[Preview \]：启用虚拟机规模集的 Azure Monitor |为指定作用域（管理组、订阅或资源组）中的虚拟机规模集启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 注意：如果规模集升级策略设置为 "手动"，则通过对其调用升级来将扩展应用到集中的所有 Vm。 在 CLI 中，这是 az vmss update-实例。 |计划 |
|\[Preview \]：虚拟机规模集中的审核依赖关系代理部署–未列出 VM 映像（OS） |如果未在列表中定义 VM 映像（OS），并且未安装代理，则将虚拟机规模集报告为不符合要求。 |策略 |
|\[Preview \]：在虚拟机规模集中审核 Log Analytics 代理部署–未列出 VM 映像（OS） |如果未在列表中定义 VM 映像（OS），并且未安装代理，则将虚拟机规模集报告为不符合要求。 |策略 |
|\[Preview \]：部署适用于 Linux 虚拟机规模集的依赖关系代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则部署适用于 Linux 虚拟机规模集的依赖关系代理。 |策略 |
|\[Preview \]：部署 Windows 虚拟机规模集的依赖关系代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则部署 Windows 虚拟机规模集的依赖关系代理。 |策略 |
|\[Preview \]：部署适用于 Linux 虚拟机规模集的 Log Analytics 代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则部署适用于 Linux 虚拟机规模集的 Log Analytics 代理。 |策略 |
|\[Preview \]：为 Windows 虚拟机规模集部署 Log Analytics 代理 |如果在列表中定义了 VM 映像（OS），并且未安装代理，则为 Windows 虚拟机规模集部署 Log Analytics 代理。 |策略 |

以下介绍独立策略（未包含在计划中）：

|名称 |描述 |Type |
|-----|------------|-----|
|\] \[Preview：审核 Log Analytics VM 的工作区–报表不匹配 |如果 Vm 未记录到策略或计划分配中指定的 Log Analytics 工作区，则将其报告为不符合要求。 |策略 |

### <a name="assign-the-azure-monitor-initiative"></a>分配 Azure Monitor 计划

若要从 "**用于 VM 的 Azure Monitor 策略覆盖范围**" 页创建策略分配，请执行以下步骤。 若要了解如何完成这些步骤，请参阅 [从 Azure 门户创建策略分配](../../governance/policy/assign-policy-portal.md)。

分配策略或计划时，在分配中选择的作用域可以是此处列出的范围或其中的一个子集。 例如，你可能已为订阅（策略范围）而不是管理组（覆盖范围）创建了分配。 在这种情况下，覆盖率百分比将指示策略或计划范围中的 Vm 除以覆盖率范围内的 Vm。 在另一种情况下，你可能已从策略作用域中排除了某些 Vm、资源组或订阅。 如果为空，则表示策略或计划不存在，或者你没有权限。 在**分配状态**下提供信息。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中选择“监视”。 

3. 在“见解”分区中选择“虚拟机(预览)”。
 
4. 选择 "**开始**" 选项卡。在 "" 页上，选择 "**管理策略覆盖率**"。

5. 从表中选择管理组或订阅。 选择 "**作用域**"，选择省略号（...）。在此示例中，作用域将策略分配限制为一组用于强制执行的虚拟机。

6. 在 " **Azure 策略分配**" 页上，将使用计划**启用用于 VM 的 Azure Monitor**。 
    "**分配名称**" 框会自动填充计划名称，但你可以更改它。 还可以添加可选说明。 系统会根据登录的用户自动填充 "**分配者**" 框。 此值是可选的。

7. （可选）若要从范围中删除一个或多个资源，请选择“排除项”。

8. 在受支持区域的“Log Analytics 工作区”下拉列表中，选择一个工作区。

   > [!NOTE]
   > 如果工作区超出分配范围，则将 *Log Analytics 参与者*权限授予策略分配的主体 ID。 如果不这样做，可能会看到部署失败，如 `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` 授予访问权限，请查看[如何手动配置托管标识](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)。
   > 
   >  "**托管标识**" 复选框处于选中状态，因为指定的计划包括具有*deployIfNotExists*效果的策略。
    
9. 在“管理标识位置”下拉列表中，选择适当的区域。

10. 选择“分配”。

创建分配之后，"**用于 VM 的 Azure Monitor 策略覆盖范围**" 页将更新**分配覆盖率**、**分配状态**、**符合的 vm**和**符合性状态**以反映所做的更改。 

下表映射了该计划的每个可能的符合性状态。  

| 符合性状态 | 描述 | 
|------------------|-------------|
| **要求** | 范围内的所有 Vm 都部署了 Log Analytics 和依赖项代理。|
| **不符合** | 并非范围内的所有 Vm 都部署了 Log Analytics 和依赖关系代理，可能需要进行修正。|
| **未启动** | 添加了新分配。 |
| **住** | 你没有足够的权限来管理组。<sup>1</sup> | 
| **空白** | 未分配策略。 | 

<sup>1</sup>如果您无权访问管理组，请要求所有者提供访问权限。 或者，通过子管理组或订阅查看符合性和管理分配。 

下表映射计划的每个可能的分配状态。

| 分配状态 | 描述 | 
|------------------|-------------|
| **Success** | 范围内的所有 Vm 都部署了 Log Analytics 和依赖项代理。|
| **警告** | 订阅不在管理组下。|
| **未启动** | 添加了新分配。 |
| **住** | 你没有足够的权限来管理组。<sup>1</sup> | 
| **空白** | 不存在 Vm 或未分配策略。 | 
| **Action** | 分配策略或编辑分配。 | 

<sup>1</sup>如果您无权访问管理组，请要求所有者提供访问权限。 或者，通过子管理组或订阅查看符合性和管理分配。

## <a name="review-and-remediate-the-compliance-results"></a>检查和修正合规结果

以下示例适用于 Azure VM，但也适用于虚拟机规模集。 若要了解如何查看符合性结果，请参阅[识别不符合](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)性结果。 在 "**用于 VM 的 Azure Monitor 策略覆盖范围**" 页上，从表中选择管理组或订阅。 选择省略号（...），选择 "**查看符合性**"。   

![Azure VM 的策略合规性](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

根据计划中包含的策略的结果，在以下情况下，Vm 将报告为不符合要求：

* 未部署 Log Analytics 代理或依赖关系代理。  
    这对于已经拥有 VM 的范围来说是典型情况。 若要缓解此问题，请通过在不符合策略上[创建修正任务](../../governance/policy/how-to/remediate-resources.md)来部署所需的代理。  
    - \[Preview \]：部署适用于 Linux Vm 的依赖关系代理
    - \[Preview \]：为 Windows Vm 部署依赖关系代理
    - \[Preview \]：部署适用于 Linux Vm 的 Log Analytics 代理
    - \[Preview \]：部署适用于 Windows Vm 的 Log Analytics 代理

* 未在策略定义中标识 VM 映像（OS）。  
    部署策略标准仅包含通过已知 Azure VM 映像部署的 VM。 请查看文档，了解 VM OS 是否受支持。 如果不受支持，则复制部署策略并更新或修改它来使映像合规。  
    - \[Preview \]：审核依赖关系代理部署–未列出 VM 映像（OS）
    - \] \[Preview：审核 Log Analytics 代理部署–未列出 VM 映像（OS）

* VM 未登录到指定的 Log Analytics 工作区。  
    可能存在计划范围中的某些 VM 登录到策略部署中指定的 Log Analytics 工作区以外的 Log Analytics 工作区的情况。 此策略是一种工具，用于确定哪些 Vm 向不符合要求的工作区进行报告。  
    - \] \[Preview：审核 Log Analytics VM 的工作区–报表不匹配

## <a name="edit-an-initiative-assignment"></a>编辑计划分配

向管理组或订阅分配计划后，你可以随时对其进行编辑，以修改以下属性：

- 分配名称
- 描述
- 分配者
- Log Analytics 工作区
- 例外

## <a name="next-steps"></a>后续步骤

为虚拟机启用监视后，可以使用用于 VM 的 Azure Monitor 分析此信息。 

- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 

- 若要确定虚拟机的性能瓶颈和总体利用率，请参阅[查看 AZURE vm 性能](vminsights-performance.md)。 

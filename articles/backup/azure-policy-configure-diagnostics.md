---
title: 大规模配置保管库诊断设置
description: 使用 Azure 策略为给定范围内的所有保管库配置 Log Analytics 诊断设置
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77584500"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>大规模配置保管库诊断设置

Azure 备份提供的报表解决方案利用 Log Analytics （LA）。 对于要发送到 LA 的任何给定保管库的数据，需要为该保管库创建[诊断设置](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

通常，为每个保管库手动添加诊断设置可能是一件繁重的任务。 此外，创建的任何新保管库还需要启用诊断设置才能查看此保管库的报表。 

若要简化按比例创建诊断设置（使用 LA 作为目标），Azure 备份提供了一个内置的[Azure 策略](https://docs.microsoft.com/azure/governance/policy/)。 此策略向给定订阅或资源组中的所有保管库添加 LA 诊断设置。 以下各节提供了有关如何使用此策略的说明。

## <a name="supported-scenarios"></a>支持的方案

* 策略可以一次应用于特定订阅中的所有恢复服务保管库（或订阅中的资源组）。 分配策略的用户需要具有对分配了策略的订阅的 "所有者" 访问权限。

* 用户指定的 "LA" 工作区（诊断数据将发送到该工作区）可以与分配该策略的保管库在不同的订阅中。 用户需要有 "读取者"、"参与者" 或 "所有者" 访问指定的 "LA" 工作区所在的订阅。

* 管理组作用域目前不受支持。

* 内置策略当前在国家云中不可用。

## <a name="assigning-the-built-in-policy-to-a-scope"></a>向作用域分配内置策略

若要为所需范围内的保管库分配策略，请执行以下步骤：

1. 登录到 Azure 门户并导航到 "**策略**" 仪表板。
2. 选择左侧菜单中的 "**定义**" 可获取跨 Azure 资源的所有内置策略的列表。
3. 筛选 "**类别 = 监视**" 列表。 找到名为 **[Preview]：将恢复服务保管库的诊断设置部署到 Log Analytics 工作区以获取资源特定类别**。

![策略定义边栏选项卡](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 单击该策略的名称。 你将被重定向到此策略的详细定义。

![详细策略定义](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 单击边栏选项卡顶部的 "**分配**" 按钮。 这会将你重定向到 "**分配策略**" 边栏选项卡。

6. 在 "**基本**信息" 下，单击 "**作用域**" 字段旁边的三个点。 这会打开一个正确的上下文边栏选项卡，你可以在其中选择要应用策略的订阅。 你还可以选择选择一个资源组，以便仅将该策略应用于特定资源组中的保管库。

![策略分配基础知识](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. 在 "**参数**" 下输入以下信息：

* **配置文件名称**-将分配给策略创建的诊断设置的名称。
* **Log Analytics 工作区**-诊断设置应关联到的 Log Analytics 工作区。 将策略分配范围内所有保管库的诊断数据推送到指定的 "LA" 工作区。

* **排除标记名称（可选）和排除标记值（可选）** -您可以选择从策略分配中排除包含特定标记名称和值的保管库。 例如，如果你**不**希望将诊断设置添加到将标记 "isTest" 设置为值 "yes" 的保管库，则必须在 "**排除标记**值" 字段中输入 "isTest"，并在 "**排除标记值**" 字段中输入 "yes"。 如果这两个字段中的任何一个（或两个）都为空，则策略将应用于所有相关保管库，而不考虑它们包含的标记。

![策略分配参数](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **创建修正任务**-将策略分配到作用域后，在该作用域中创建的任何新保管库都会自动获得配置的 "LA 诊断" 设置（在创建保管库的时间内的30分钟内）。 若要将诊断设置添加到作用域中的现有保管库，可以在策略分配时触发修正任务。 若要触发修正任务，请选中 "**创建修正任务**" 复选框。 

![策略分配修正](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. 导航到 "**查看 + 创建**" 选项卡，然后单击 "**创建**"。

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>修正任务适用于保管库的条件是什么？

根据策略的定义，将修正任务应用于不符合的保管库。 如果保管库满足以下任一条件，则它不符合：

* 保管库没有诊断设置。
* 此保管库有诊断设置，但这两个设置都没有启用了 LA 作为目标的**所有**资源特定事件，并在切换中选择了**资源**。 

因此，即使用户具有在 AzureDiagnostics 模式下启用了 AzureBackupReport 事件的保管库（备份报表支持此功能），修正任务仍将应用于此保管[库，因为](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)建议使用特定于资源的模式来创建诊断设置。

此外，如果用户的保管库仅包含六个已启用资源特定事件的子集，则修正任务将适用于此保管库，因为只有在启用了六个资源特定的事件后，备份报表才会按预期方式工作。

> [!NOTE]
>
> 如果保管库具有启用了**资源特定类别子集的**现有诊断设置，则将其配置为将数据发送到特定的 LA 工作区（如 "工作区 X"），如果策略分配中提供的目标 LA 工作区为**同一**"工作区 x"，则修正任务将失败（仅适用于该保管库）。 
>
>这是因为，如果同一资源的两个不同诊断设置启用的事件在某种形式下**重叠**，则设置不能与目标具有相同的 LA 工作区。 通过导航到相关保管库并使用不同的 LA 工作区作为目标来配置诊断设置，你将需要手动解决此故障。
>
> 请注意，如果现有诊断设置为仅启用了工作区 X 的 AzureBackupReport 作为目标，则更新任务**不**会失败，因为在这种情况下，现有设置启用的事件与由修正任务创建的设置所启用的事件之间不会有重叠。

## <a name="next-steps"></a>后续步骤

* [了解如何使用备份报表](https://docs.microsoft.com/azure/backup/configure-reports)
* [了解有关 Azure 策略的详细信息](https://docs.microsoft.com/azure/governance/policy/)
* [使用 Azure 策略自动为指定范围内的所有 Vm 启用备份](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)

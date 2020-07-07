---
title: 大规模配置保管库诊断设置
description: 使用 Azure Policy 为给定范围内的所有保管库配置 Log Analytics 诊断设置
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e6ba8eb98ca1d6af9fc745d9baf3840ccd1ac224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82195700"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>大规模配置保管库诊断设置

Azure 备份提供的报告解决方案利用了 Log Analytics (LA)。 为了将任何给定保管库的数据发送到 LA，需要为该保管库创建[诊断设置](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

通常，为每个保管库手动添加诊断设置是一项繁琐的任务。 此外，创建的任何新保管库也需要启用诊断设置才能查看此保管库的报表。

为了简化大规模创建诊断设置的过程（以 LA 为目标），Azure 备份提供了内置 [Azure Policy](https://docs.microsoft.com/azure/governance/policy/)。 此策略可为给定订阅或资源组中的所有保管库添加 LA 诊断设置。 以下部分介绍了如何使用此策略。

## <a name="supported-scenarios"></a>支持的方案

* 此策略可以一次应用于某个特定订阅中的所有恢复服务保管库（或该特定订阅中的资源组）。 分配策略的用户需要对向其分配策略的订阅具有“所有者”访问权限。

* 用户指定的 LA 工作区（诊断数据将发送到的工作区）可以位于与向其分配策略的保管库不同的订阅中。 用户需要对存在指定 LA 工作区的订阅具有“读者”、“参与者”或“所有者”访问权限。

* 当前不支持管理组范围。

* 内置策略当前在国家/地区云中不可用。

## <a name="assigning-the-built-in-policy-to-a-scope"></a>将内置策略分配到范围

若要为所需范围内的保管库分配策略，请遵循以下步骤进行操作：

1. 登录到 Azure 门户并导航到“策略”仪表板。
2. 在左边的菜单中选择“定义”以获取跨 Azure 资源的所有内置策略的列表。
3. 筛选“类别=监视”的列表。 找到名为“[预览]：将恢复服务保管库的诊断设置部署到资源专有类别的 Log Analytics 工作区”的策略。

    ![策略定义边栏选项卡](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 单击该策略的名称。 随后会重定向到此策略的详细定义。

    ![详细的策略定义](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 单击边栏选项卡顶部的“分配”按钮。 随后会重定向到“分配策略”边栏选项卡。

6. 在“基础”下，单击“范围”字段旁边的三个点 。 随即在右侧打开一个上下文边栏选项卡，可以在其中选择要应用策略的订阅。 你还可以选择资源组，以便该策略仅应用于特定资源组中的保管库。

    ![策略分配基础知识](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. 在“参数”下输入以下信息：

    * **配置文件名称** - 将分配给策略创建的诊断设置的名称。
    * **Log Analytics 工作区** - 应与诊断设置关联的 Log Analytics 工作区。 策略分配范围内的所有保管库的诊断数据都将被推送到指定的 LA 工作区。

    * **排除标记名称（可选）和排除标记值（可选）** - 可以选择从策略分配中排除包含特定标记名称和值的保管库。 例如，如果不希望将诊断设置添加到将标记“isTest”设置为“yes”值的保管库中，则必须在“排除标记名称”字段中输入“isTest”，并在“排除标记值”字段中输入“yes”  。 如果这两个字段中的任何一个（或两个）为空，则策略将应用到所有相关的保管库，而不考虑它们包含的标记。

    ![策略分配参数](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **创建修正任务** - 将策略分配到某个范围后，在该范围内创建的任何新保管库都会自动配置 LA 诊断设置（在创建保管库后的 30 分钟内）。 若要将诊断设置添加到范围内的现有保管库，可以在策略分配时触发修正任务。 若要触发修正任务，请选择“创建修正任务”复选框。

    ![策略分配修正](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. 导航到“查看 + 创建”选项卡，然后单击“创建” 。

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>在什么情况下，将对保管库应用修正任务？

根据策略的定义，如果保管库不符合要求，就会对其应用修正任务。 保管库满足以下任一条件将被视为不符合要求：

* 保管库没有诊断设置。
* 保管库具有诊断设置，但是没有一个设置启用了将 LA 作为目标的所有资源特定事件，并且在切换中选择了“资源特定”事件 。

因此，即使用户的保管库在 AzureDiagnostics 模式下启用了 AzureBackupReport 事件（由备份报告提供支持），也将对此保管库应用修正任务，因为该资源特定模式是[今后](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)创建诊断设置的建议方法。

此外，如果用户的保管库只启用了六个资源特定事件的子集，则将对此保管库应用修正任务，因为只有启用所有六个资源特定事件，备份报告才能按预期工作。

> [!NOTE]
>
> 如果保管库具有已启用资源特定子集的类别的现有诊断设置，并配置为将数据发送到特定的 LA 工作区（例如“工作区 X”），则当策略分配中提供的目标 LA 工作区与“工作区 X”相同时，修正任务将失败（仅针对该保管库） 。
>
>这是因为，如果在同一资源上由两个不同诊断设置启用的事件以某种形式重叠，则这些设置不能具有与目标相同的 LA 工作区。 你将需要手动解决此故障，方法是导航到相关保管库并使用不同的 LA 工作区作为目标来配置诊断设置。
>
> 请注意，如果现有诊断设置仅启用 AzureBackupReport（以工作区 X 为目标），则修正任务不会失败，因为在这种情况下，现有设置启用的事件与修正任务创建的设置启用的事件之间不会重叠。

## <a name="next-steps"></a>后续步骤

* [了解如何使用备份报告](https://docs.microsoft.com/azure/backup/configure-reports)
* [了解有关 Azure Policy 的详细信息](https://docs.microsoft.com/azure/governance/policy/)
* [使用 Azure Policy 自动为给定范围内的所有 VM 启用备份](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)

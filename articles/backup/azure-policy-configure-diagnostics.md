---
title: 大规模配置保管库诊断设置
description: 使用 Azure 策略为给定作用域中的所有保管库配置日志分析诊断设置
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584500"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>大规模配置保管库诊断设置

Azure 备份提供的报告解决方案利用日志分析 （LA）。 要将任何给定保管库的数据发送到 LA，需要为该保管库创建[诊断设置](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

通常，每个保管库手动添加诊断设置可能是一项繁琐的任务。 此外，创建的任何新保管库都需要启用诊断设置，以便能够查看此保管库的报告。 

为了简化大规模创建诊断设置（以 LA 为目标），Azure 备份提供了内置的 Azure[策略](https://docs.microsoft.com/azure/governance/policy/)。 此策略将 LA 诊断设置添加到给定订阅或资源组中的所有保管库。 以下各节提供有关如何使用此策略的说明。

## <a name="supported-scenarios"></a>支持的方案

* 该策略可以一次应用于特定订阅中的所有恢复服务保管库（或订阅中的资源组）。 分配策略的用户需要具有对策略分配的订阅的"所有者"访问权限。

* 用户指定的 LA 工作区（将诊断数据发送到该工作区）可以位于与策略分配到的保管库不同的订阅中。 用户需要对存在指定 LA 工作区的订阅具有"读者"、"贡献者"或"所有者"访问权限。

* 管理组作用域当前不受支持。

* 内置策略目前不在国家云中。

## <a name="assigning-the-built-in-policy-to-a-scope"></a>将内置策略分配给作用域

要在所需范围内为保管库分配策略，请按照以下步骤操作：

1. 登录到 Azure 门户并导航到**策略**仪表板。
2. 选择左侧菜单中**的定义**，以获取有关 Azure 资源的所有内置策略的列表。
3. 筛选**类别=监视**的列表。 找到名为 **[预览]的策略：将恢复服务保管库的诊断设置部署到资源特定类别的日志分析工作区**。

![策略定义边栏选项卡](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 单击策略的名称。 您将被重定向到此策略的详细定义。

![详细的策略定义](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 单击边栏选项卡顶部的 **"分配"** 按钮。 这将将您重定向到 **"分配策略"** 边栏选项卡。

6. 在 **"基础知识"** 下，单击 **"范围"** 字段旁边的三个点。 这将打开一个正确的上下文边栏选项卡，您可以在其中选择要应用的策略的订阅。 您还可以选择资源组，以便策略仅应用于特定资源组中的保管库。

![策略分配基础知识](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. 在**参数**下，输入以下信息：

* **配置文件名称**- 将分配给策略创建的诊断设置的名称。
* **日志分析工作区**- 应关联诊断设置的日志分析工作区。 策略分配范围内所有保管库的诊断数据将推送到指定的 LA 工作区。

* **排除标记名称（可选）和排除标记值（可选）** - 您可以选择从策略分配中排除包含特定标记名称和值的保管库。 例如，如果您**不希望**将诊断设置添加到标记"isTest"设置为值"是"的保管库，则必须在 **"排除标记名称**"字段中输入"isTest"，在 **"排除标记值"** 字段中输入"是"。 如果这两个字段中的任何一个（或两个）都留空，则策略将应用于所有相关保管库，而不管它们包含的标记。

![策略分配参数](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **创建修正任务**- 将策略分配给作用域后，该作用域中创建的任何新保管库将自动配置 LA 诊断设置（自创建保管库之日起 30 分钟内）。 要将诊断设置添加到作用域中的现有保管库，可以在策略分配时触发修正任务。 要触发修正任务，请选择复选框 **"创建修正任务**"。 

![策略分配补救](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. 导航到 **"审阅+创建**"选项卡，然后单击"**创建**"。

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>补救任务在什么条件下将应用于保管库？

根据策略的定义，修正任务应用于不符合要求的保管库。 如果保管库满足以下任一条件，则不符合该保管库：

* 保管库不存在诊断设置。
* 保管库存在诊断设置，但两个设置均未启用**所有**资源特定事件，LA 作为目标，并在切换中选择**特定于资源**的事件。 

因此，即使用户在 Azure 诊断模式下启用了 AzureBackupReport 事件（备份报告支持），修复任务仍将应用于此保管库，因为资源特定模式是创建诊断设置的建议方法，[并且将继续](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)使用 。

此外，如果用户只有启用了六个资源特定事件的子集，则修正任务将应用于此保管库，因为备份报告仅在启用了所有六个资源特定事件时按预期工作。

> [!NOTE]
>
> 如果保管库具有启用**了资源特定类别子集**的现有诊断设置，配置为将数据发送到特定的 LA 工作区（如"工作区 X"），则如果策略分配中提供的目标 LA 工作区**与"** 工作区 X"相同，则修正任务将失败（仅针对该保管库）。 
>
>这是因为，如果同一资源上的两个不同的诊断设置启用的事件以某种形式**重叠**，则这些设置不能与目标具有相同的 LA 工作区。 您必须手动解决此故障，方法是导航到相关保管库，并将具有其他 LA 工作区的诊断设置配置为目标。
>
> 请注意，如果现有诊断设置仅启用 AzureBackupReport，并将工作区 X 作为目标，则修正任务**不会**失败，因为在这种情况下，现有设置启用的事件与修复任务创建的设置启用的事件之间没有重叠。

## <a name="next-steps"></a>后续步骤

* [了解如何使用备份报告](https://docs.microsoft.com/azure/backup/configure-reports)
* [了解有关 Azure 策略的更多](https://docs.microsoft.com/azure/governance/policy/)
* [使用 Azure 策略自动启用给范围中所有 VM 的备份](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)

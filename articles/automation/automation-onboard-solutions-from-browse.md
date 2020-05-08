---
title: 了解如何在 Azure 自动化中为多台 VM 载入更新管理、更改跟踪和清单解决方案
description: 了解如何载入包含属于 Azure 自动化的一部分的更新管理、更改跟踪和清单解决方案的 Azure 虚拟机
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: d97fbe6ac515a2559340474105d73b7c9b9c6ee4
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731911"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>在多台 VM 上启用更新管理、更改跟踪和清单解决方案

Azure 自动化提供了解决方案来管理操作系统安全性更新、跟踪更改以及列出计算机上所安装项的清单。 可以通过多种方式来载入计算机，可以[通过虚拟机](automation-onboard-solutions-from-vm.md)、[通过自动化帐户](automation-onboard-solutions-from-automation-account.md)、在浏览虚拟机时或通过 [Runbook](automation-onboard-solutions.md) 载入解决方案。 本文介绍了在 Azure 中浏览虚拟机时如何载入这些解决方案。

## <a name="sign-in-to-azure"></a>登录 Azure

登录 Azure (https://portal.azure.com)

## <a name="enable-solutions"></a>启用解决方案

在 Azure 门户中，导航到“虚拟机”****。

使用复选框，选择要载入“更改跟踪”和“清单”或“更新管理”功能的虚拟机。 载入一次最多可用于三个不同的资源组。 无论自动化帐户的位置如何，Azure Vm 都可以存在于任何区域中。

![VM 列表](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> 使用筛选器控件修改虚拟机列表，然后单击顶部的复选框来选择列表中的所有虚拟机。

从命令栏中，单击“服务”****，然后选择“更改跟踪”****、“清单”**** 或“更新管理”****。

> [!NOTE]
> 更改跟踪和清单使用相同的解决方案。 启用后，也会启用另一个。

下图是关于“更新管理”的。 更改跟踪和库存具有相同的布局和行为。

虚拟机的列表已经过筛选，仅显示了位于相同订阅和位置的虚拟机。 如果你的虚拟机位于三个以上资源组中，则会选择前三个资源组。

### <a name="onboarding-limitations"></a><a name="resource-group-limit"></a> 载入限制

你可以用于载入的资源组的数量受限于[资源管理器部署限制](../azure-resource-manager/templates/cross-resource-group-deployment.md)。 资源管理器部署，不会与更新部署混淆，每个部署只能有5个资源组。 为确保载入的完整性，这些资源组中有 2 个保留用来配置 Log Analytics 工作区、自动化帐户和相关资源。 剩下的 3 个资源组供你选择用于部署。 此限制仅适用于同时加入，而不适用于自动化解决方案可以管理的资源组数。

你还可以使用用于载入的 runbook，有关详细信息，请参阅[Azure 自动化的内置更新和更改跟踪解决方案](automation-onboard-solutions.md)。

使用筛选器控件从不同的订阅、位置和资源组中选择虚拟机。

![载入更新管理解决方案](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

查看 Log Analytics 工作区和自动化帐户的选项。 默认情况下会选择一个现有的工作区和自动化帐户。 如果希望使用不同的 Log Analytics 工作区和自动化帐户，请单击“自定义”**** 来从“自定义配置”页面选择它们。**** 当你选择 Log Analytics 工作区时，将会进行检查来确定它是否与某个自动化帐户相链接。 如果找到了链接的自动化帐户，则会看到以下屏幕。 完成后单击“确定”****。

![选择工作区和帐户](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

如果所选的工作区未链接到自动化帐户，则会看到以下屏幕。 选择一个自动化帐户，完成后单击“确定”。****

![无工作区](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> 在启用解决方案时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。
>
> 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

取消选择不想启用的任何虚拟机旁边的复选框。 无法启用的虚拟机已被取消选择。

单击“启用”以启用解决方案。**** 启用此解决方案最长需要 15 分钟的时间。

## <a name="unlink-workspace"></a>取消链接工作区

以下解决方案依赖于 Log Analytics 工作区：

* [更新管理](automation-update-management.md)
* [更改跟踪](automation-change-tracking.md)
* [在非工作时间启动/停止 VM](automation-solution-vm-management.md)

如果你决定不再想要将自动化帐户与 Log Analytics 工作区集成，则可以直接从 Azure 门户取消链接你的帐户。 在继续之前，首先需要删除前面所述的解决方案，否则此过程将无法继续。 查看已导入的特定解决方案的主题，了解删除该解决方案所需的步骤。

删除这些解决方案后，可以执行以下步骤取消链接自动化帐户。

> [!NOTE]
> 某些解决方案（包括早期版本的 Azure SQL 监视解决方案）可能已创建自动化资产并可能还需要在取消链接工作区之前删除。

1. 从 Azure 门户中打开自动化帐户，并在“自动化帐户”页左侧的“相关资源”部分下，选择“链接工作区”********。

2. 在“取消链接工作区”页上，单击“取消链接工作区”****。

   ![“取消链接工作区”页](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   系统会提示用户确认是否要继续。

3. 当 Azure 自动化尝试从 Log Analytics 工作区中取消链接该帐户时，可以在菜单中的“通知”**** 下跟踪进度。

如果使用了“更新管理”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 更新计划 - 每个计划都将具有与所创建的更新部署匹配的名称

* 为解决方案创建的混合辅助角色组 - 每个混合辅助角色组的命名都将类似于 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8。

如果使用了“在非工作时间启动/停止 VM”解决方案，可能会选择要删除在删除该解决方案后不再需要的以下项。

* 启动和停止 VM Runbook 计划
* 启动和停止 VM Runbook
* 变量

此外，还可以从 "Log Analytics" 工作区中取消工作区与自动化帐户的链接。 在工作区中，选择 "**相关资源**" 下的 "**自动化帐户**"。 在 "自动化帐户" 页上，选择 "**取消链接帐户**"。

## <a name="troubleshooting"></a>疑难解答

当载入多台计算机时，可能会有显示为`Cannot enable`的计算机。 有各种原因会导致某些计算机无法启用。 以下部分说明了尝试载入 VM 时`Cannot enable`该虚拟机的状态的可能原因。

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM 向一个不同的工作区进行报告：“\<workspaceName\>”。  请更改配置以将其用于启用

**原因**：此错误表明你尝试载入的 VM 向另一个工作区进行报告。

**解决方案**：单击 "用作**配置**" 以更改目标自动化帐户并 Log Analytics "工作区"。

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM 向此订阅中不可用的工作区进行报告

**原因**：虚拟机向其进行报告的工作区：

* 位于一个不同的订阅中，或者
* 不再存在，或者
* 位于你无权访问的资源组中

**解决方案**：找到与 VM 向其进行报告的工作区关联的自动化帐户，并通过更改作用域配置来载入虚拟机。

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM 操作系统版本或分发版不受支持

**原因：** 并非所有 Linux 分发版或所有 Windows 版本都支持此解决方案。

**解决方案：** 请参阅[受支持的客户端的列表](automation-update-management.md#supported-client-types)。

### <a name="classic-vms-cannot-be-enabled"></a>无法启用经典 VM

**原因**：使用经典部署模型的虚拟机不受支持。

**解决方案**：将虚拟机迁移到资源管理器部署模型。 若要了解如何执行此操作，请参阅[迁移经典部署模型资源](../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

### <a name="vm-is-stopped-deallocated"></a>VM 已停止。 （已解除分配）

**原因**：虚拟机未处于**正在运行**状态。

**解决方案**：为了将 VM 载入到解决方案，VM 必须正在运行。 单击“启动 VM”**** 内联链接来启动 VM 且不离开页面。

## <a name="clean-up-resources"></a>清理资源

从更新管理中删除 VM：

* 在 Log Analytics 工作区中，从范围配置 `MicrosoftDefaultScopeConfig-Updates` 的已保存的搜索中删除 VM。 已保存的搜索位于工作区的“常规”下  。
* 删除[适用于 Windows 的 Log Analytics 代理](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources)或[适用于 Linux 的 Log Analytics 代理](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)。

## <a name="next-steps"></a>后续步骤

现在已为虚拟机启用了解决方案，请访问更新管理概述一文，了解如何为计算机创建**更新部署**。

> [!div class="nextstepaction"]
> [更新管理-管理 Azure Vm 的更新和修补程序](./automation-tutorial-update-management.md)

关于解决方案以及如何使用它们的其他教程：

* [教程-管理 VM 的更新](automation-tutorial-update-management.md)

* [教程 - 识别 VM 上的软件](automation-tutorial-installed-software.md)

* [教程 - 对 VM 上的更改进行故障排除](automation-tutorial-troubleshoot-changes.md)

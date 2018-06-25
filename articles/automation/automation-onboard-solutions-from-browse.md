---
title: 了解如何在 Azure 自动化中为多台 VM 载入更新管理、更改跟踪和清单解决方案
description: 了解如何载入包含属于 Azure 自动化的一部分的更新管理、更改跟踪和清单解决方案的 Azure 虚拟机
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0a624d850b8c3260acb24cb17566090e8ad0043e
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233931"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>在多台 VM 上启用更新管理、更改跟踪和清单解决方案

Azure 自动化提供了解决方案来管理操作系统安全性更新、跟踪更改以及列出计算机上所安装项的清单。 可以通过多种方式来载入计算机，可以[通过虚拟机](automation-onboard-solutions-from-vm.md)、[通过自动化帐户](automation-onboard-solutions-from-automation-account.md)、在浏览虚拟机时或通过 [Runbook](automation-onboard-solutions.md) 载入解决方案。 本文介绍了在 Azure 中浏览虚拟机时如何载入这些解决方案。

## <a name="log-in-to-azure"></a>登录 Azure

在 https://portal.azure.com 中登录 Azure

## <a name="enable-solutions"></a>启用解决方案

在 Azure 门户中，导航到“虚拟机”。

使用复选框，选择要载入“更改跟踪”和“清单”或“更新管理”功能的虚拟机。 载入一次最多可用于三个不同的资源组。

![VM 列表](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> 使用筛选器控件修改虚拟机列表，然后单击顶部的复选框来选择列表中的所有虚拟机。

从命令栏中，单击“服务”，然后选择“更改跟踪”、“清单”或“更新管理”。

> [!NOTE]
> “更改跟踪”和“清单”使用相同的解决方案，启用其中一个后，另一个也会启用。

下图是关于“更新管理”的。 “更改跟踪”和“清单”具有相同的布局和行为。

虚拟机的列表已经过筛选，仅显示了位于相同订阅和位置的虚拟机。 如果你的虚拟机位于三个以上资源组中，则会选择前三个资源组。

使用筛选器控件从不同的订阅、位置和资源组中选择虚拟机。

![载入更新管理解决方案](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

复查针对 Log Analytics 工作区和自动化帐户所做的选择。 默认情况下会选择一个新的工作区和自动化帐户。 如果有一个要使用的现有 Log Analytics 工作区和自动化帐户，请单击“更改”来从“配置”页面选择它们。 完成后，单击“保存”。

![选择工作区和帐户](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

取消选择不想启用的任何虚拟机旁边的复选框。 无法启用的虚拟机已被取消选择。

单击“启用”以启用解决方案。 启用此解决方案最长需要 15 分钟的时间。

## <a name="troubleshooting"></a>故障排除

当载入多台计算机时，可能会有显示为“无法启用”的计算机。 有各种原因会导致某些计算机无法启用。 以下各部分显示了当尝试载入时 VM 上出现“无法启用”状态的可能原因。

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM 向一个不同的工作区进行报告：“\<workspaceName\>”。  请更改配置以将其用于启用

**原因**：此错误表明你尝试载入的 VM 向另一个工作区进行报告。

**解决方案**：单击“用作配置”来更改目标自动化帐户和 Log Analytics 工作区。

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM 向此订阅中不可用的工作区进行报告

**原因**：虚拟机向其进行报告的工作区：

* 位于一个不同的订阅中，或者
* 不再存在，或者
* 位于你无权访问的资源组中

**解决方案**：找到与 VM 向其进行报告的工作区关联的自动化帐户，并通过更改作用域配置来载入虚拟机。

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM 操作系统版本或分发版不受支持

**原因：** 并非所有 Linux 分发版或所有 Windows 版本都支持此解决方案。

**解决方案：** 指的是解决方案的[受支持客户端列表](automation-update-management.md#clients)。

### <a name="classic-vms-cannot-be-enabled"></a>无法启用经典 VM

**原因**：使用经典部署模型的虚拟机不受支持。

**解决方案**：将虚拟机迁移到资源管理器部署模型。 若要了解如何执行此操作，请参阅[迁移经典部署模型资源](../virtual-machines/windows/migration-classic-resource-manager-overview.md)。

### <a name="vm-is-stopped-deallocated"></a>VM 已停止。 （已解除分配）

**原因**：虚拟机未处于**正在运行**状态。

**解决方案**：为了将 VM 载入到解决方案，VM 必须正在运行。 单击“启动 VM”内联链接来启动 VM 且不离开页面。

## <a name="next-steps"></a>后续步骤

现在，已经为虚拟机启用了解决方案，请访问“更新管理”概述一文来了解如何查看计算机的更新评估。

> [!div class="nextstepaction"]
> [更新管理 - 查看更新评估](./automation-update-management.md#viewing-update-assessments)

关于解决方案以及如何使用它们的其他教程：

* [教程 - 管理 VM 的更新](automation-tutorial-update-management.md)

* [教程 - 识别 VM 上的软件](automation-tutorial-installed-software.md)

* [教程 - 对 VM 上的更改进行故障排除](automation-tutorial-troubleshoot-changes.md)
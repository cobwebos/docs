---
title: Azure Automation State Configuration 概述
description: 对 Azure Automation State Configuration (DSC) 及其术语和已知问题的概述
keywords: powershell dsc, 所需状态配置, powershell dsc azure
services: automation
ms.service: automation
ms.component: dsc
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2d46803f81b369f8f24a6f0e3c7f32955503e4a
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40006055"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration 概述

Azure Automation State Configuration 是一种 Azure 服务，允许编写、管理和编译 PowerShell Desired State Configuration (DSC) [配置](/powershell/dsc/configurations)，导入 [DSC 资源](/powershell/dsc/resources)，并将配置分配给目标节点，所有操作均在云中进行。

## <a name="why-use-azure-automation-state-configuration"></a>为何使用 Azure Automation State Configuration

与在 Azure 之外使用 DSC 相比，Azure Automation State Configuration 具有多项优势。

### <a name="built-in-pull-server"></a>内置拉取服务器

Azure Automation State Configuration 提供了类似于 [Windows 功能 DSC 服务](/powershell/dsc/pullserver)的 DSC 拉取服务器，这样目标节点可自动接收配置，符合所需状态，并报告回其符合性。 Azure 自动化中的内置拉取服务器消除了设置和维护你自己的拉取服务器的需要。 Azure 自动化的目标可以是云中或本地的虚拟机，或物理 Windows 或 Linux 计算机。

### <a name="management-of-all-your-dsc-artifacts"></a>管理所有 DSC 项目

Azure Automation State Configuration 向 [PowerShell Desired State Configuration](/powershell/dsc/overview) 提供的管理层与 Azure 自动化为 PowerShell 脚本提供的相同。

从 Azure 门户，或从 PowerShell，你可以管理所有的 DSC 配置、资源和目标节点。

![Azure 自动化边栏选项卡的屏幕截图](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>将报表数据导入 Log Analytics

使用 Azure Automation State Configuration 进行管理的节点将详细的报表状态数据发送到内置拉取服务器。 可以将 Azure Automation State Configuration 配置为将此数据发送到 Log Analytics 工作区。 若要了解如何将 State Configuration 状态数据发送到 Log Analytics 工作区，请参阅[将 Azure Automation State Configuration 报表数据转发到 Log Analytics](automation-dsc-diagnostics.md)。

## <a name="introduction-video"></a>简介视频

更喜欢观看视频而不喜欢阅读文档？ 请观看下述在 2015 年 5 月发布的视频，这是首次发布 Azure Automation State Configuration 的时间。

> [!NOTE]
> 尽管这段视频中所述的概念和生命周期都是正确的，但 Azure Automation State Configuration 自从这段视频录制以来已有很大的发展。 它现已正式发布，在 Azure 门户中具有更丰富的 UI，并支持更多功能。

[!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>后续步骤

- 若要开始使用，请参阅 [Azure Automation State Configuration 入门](automation-dsc-getting-started.md)
- 要了解如何登记节点，请参阅[登记由 Azure Automation State Configuration 管理的计算机](automation-dsc-onboarding.md)
- 若要了解如何编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure Automation State Configuration 中编译配置](automation-dsc-compile.md)
- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 有关定价信息，请参阅 [Azure Automation State Configuration 定价](https://azure.microsoft.com/pricing/details/automation/)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)
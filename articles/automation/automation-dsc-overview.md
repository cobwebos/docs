---
title: "Azure 自动化 DSC 概述 | Microsoft 文档"
description: "对 Azure 自动化所需状态配置 (DSC) 及其术语和已知问题的概述"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell dsc, 所需状态配置, powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 23a6e09d410616b135506df778d6fdd5ed68bb41
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017

---

# <a name="azure-automation-dsc-overview"></a>Azure 自动化 DSC 概述

Azure Automation DSC 是一种 Azure 服务，允许编写、管理和编译 PowerShell Desired State Configuration (DSC) [配置](https://msdn.microsoft.com/powershell/dsc/configurations)，导入 [DSC 资源](https://msdn.microsoft.com/powershell/dsc/resources)，并将配置分配给目标节点，所有操作均在云中进行。

## <a name="why-use-azure-automation-dsc"></a>为什么使用 Azure Automation DSC

通过使用除 Azure 之外的 DSC，Azure Automation DSC 可提供数项优势。

### <a name="built-in-pull-server"></a>内置拉取服务器

Azure 自动化提供了 [DSC 拉取服务器](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver)，这样目标节点可自动接收配置，符合所需状态，并报告回其符合性。
Azure 自动化中的内置拉取服务器消除了设置和维护你自己的拉取服务器的需要。
Azure 自动化的目标可以是云中或本地的虚拟机，或物理 Windows 或 Linux 计算机。

### <a name="management-of-all-your-dsc-artifacts"></a>管理所有 DSC 项目

Azure Automation DSC 向 [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) 提供的管理层与 Azure 自动化为 PowerShell 脚本提供的相同。

从 Azure 门户，或从 PowerShell，你可以管理所有的 DSC 配置、资源和目标节点。

![Azure 自动化边栏选项卡的屏幕截图](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>将报表数据导入 Log Analytics

使用 Azure Automation DSC 进行管理的节点将详细的报表状态数据发送到内置拉取服务器。
可以将 Azure Automation DSC 配置为将此数据发送到 Microsoft Operations Management Suite (OMS) Log Analytics 工作区。
要了解如何将 DSC 状态数据发送到 Log Analytics 工作区，请参阅[将 Azure Automation DSC 报表数据转发到 OMS Log Analytics](automation-dsc-diagnostics.md)。

## <a name="introduction-video"></a>简介视频

你更喜欢观看视频而不喜欢阅读文档？ 请观看以下在 2015 年 5 月发布的视频，这是首次发布 Azure 自动化 DSC 的时间。

>[!NOTE]
>尽管这段视频中所述的概念和生命周期都是正确的，但 Azure Automation DSC 自从这段视频录制以来已有很大的发展。
>它现已正式发布，在 Azure 门户中具有更丰富的 UI，并支持更多功能。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>后续步骤

* 要了解如何登记使用 Azure Automation DSC 管理的节点，请参阅[登记由 Azure 自动化 DSC 管理的计算机](automation-dsc-onboarding.md)
* 若要开始使用 Azure Automation DSC，请参阅 [Azure Automation DSC 入门](automation-dsc-getting-started.md)
* 要了解编译 DSC 配置，以便将它们分配给目标节点，请参阅[在 Azure 自动化 DSC 中编译配置](automation-dsc-compile.md)
* 有关 Azure Automation DSC 的 PowerShell cmdlet 参考，请参阅 [Azure Automation DSC cmdlet](/powershell/module/azurerm.automation/#automation)
* 有关定价信息，请参阅 [Azure Automation DSC 定价](https://azure.microsoft.com/pricing/details/automation/)。
* 要查看在持续部署管道中使用 Azure Automation DSC 的示例，请参阅 [使用 Automation DSC 和 Chocolatey 持续部署到 IaaS 虚拟机](automation-dsc-cd-chocolatey.md)

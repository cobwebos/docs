---
title: "使用 Azure 自动化管理 Azure RemoteApp | Microsoft Docs"
description: "了解如何使用 Azure 自动化服务来管理 Azure RemoteApp。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 589f01ef-f9c1-4e7b-a040-1b46862d3544
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3e21af65ecb6f0ce70048846a9786271dd4cc985


---
# <a name="managing-azure-remoteapp-using-azure-automation"></a>使用 Azure 自动化管理 Azure RemoteApp
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

本指南将介绍 Azure 自动化服务，以及如何使用它来简化 Azure RemoteApp 的管理。

## <a name="what-is-azure-automation"></a>什么是 Azure 自动化？
[Azure 自动化](../automation/automation-intro.md)是用于通过流程自动化简化云管理的一项 Azure 服务。 使用 Azure 自动化可以自动完成那些人工操作、经常重复、长时间运行且易出错的任务，从而改善组织的可靠性、效率和价值生成时间。

Azure 自动化提供了具有高可靠性和高可用性的工作流执行引擎，该引擎可以根据你的需求进行扩展。 在 Azure 自动化中，流程可以手动、通过第三方系统或按计划的间隔启动，使任务能够完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT 和开发运营人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-remoteapp"></a>Azure 自动化如何帮助管理 Azure RemoteApp？
可以使用 [Azure PowerShell 工具](https://msdn.microsoft.com/library/azure/jj156055.aspx)中提供的 PowerShell cmdlet 在 Azure 自动化中管理 RemoteApp。 Azure 自动化现成地提供了这些 RemoteApp PowerShell cmdlet，因此，你可以在该服务中执行所有 RemoteApp 管理任务。 你还可以将 Azure 自动化中的这些 cmdlet 与其他 Azure 服务的 cmdlet 搭配使用，以自动完成跨 Azure 服务和第三方系统的复杂任务。

## <a name="next-steps"></a>后续步骤
在了解 Azure 自动化 以及如何使用它来管理 Azure RemoteApp 的基础知识后，请使用以下链接了解有关 Azure 自动化的更多信息。

* 请参阅 Azure 自动化[入门教程](../automation/automation-first-runbook-graphical.md)




<!--HONumber=Nov16_HO3-->



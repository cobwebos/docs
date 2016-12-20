---
title: "使用 Azure 自动化管理 Azure API Management"
description: "了解如何使用 Azure 自动化服务来管理 Azure API Management。"
services: api-management, automation
documentationcenter: 
author: csand-msft
manager: eamono
editor: 
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: csand
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3e0e91cf3652b71af49242206057854500f78b2e


---
# <a name="managing-azure-api-management-using-azure-automation"></a>使用 Azure 自动化管理 Azure API Management
本指南将介绍 Azure 自动化服务，以及如何使用它来简化 Azure API Management 的管理。

## <a name="what-is-azure-automation"></a>什么是 Azure 自动化？
[Azure 自动化](https://azure.microsoft.com/services/automation/)是用于通过流程自动化简化云管理的一项 Azure 服务。 使用 Azure 自动化可以自动完成那些人工操作、重复、长时间运行且易出错的任务，从而改善组织的可靠性、效率和价值生成时间。

Azure 自动化提供了具有高可靠性和高可用性的工作流执行引擎，该引擎可以根据你的需求进行扩展。 在 Azure 自动化中，流程可以手动、通过第三方系统或按计划的间隔启动，使任务能够完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT 和开发运营人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Azure 自动化如何帮助管理 Azure API Management？
可使用[适用于 Azure API 管理 API 的 Windows PowerShell cmdlet](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/) 在 Azure 自动化中管理 API 管理。 在 Azure 自动化中，可以编写 PowerShell 工作流脚本来使用 cmdlet 执行多种 API 管理任务。 你还可以将 Azure 自动化中的这些 cmdlet 与其他 Azure 服务的 cmdlet 搭配使用，以自动完成跨 Azure 服务和第三方系统的复杂任务。

以下是利用自动化使用 API 管理的一些示例：

* [Azure API 管理 - 使用 PowerShell 进行备份和还原](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>后续步骤
在了解 Azure 自动化以及如何使用它来管理 Azure API Management 的基础知识后，请使用以下链接了解更多信息。

* 请参阅 Azure 自动化[入门教程](../automation/automation-first-runbook-graphical.md)。




<!--HONumber=Nov16_HO3-->



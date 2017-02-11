---
title: "使用 Azure 自动化管理 Azure Web 应用 | Microsoft Docs"
description: "了解如何使用 Azure 自动化服务来管理 Azure Web 应用程序。"
services: app-service\web, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: c960a44f-f941-401d-afba-a4bc0f0394eb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 4edd2696c9a5709ded6e2a3e352090775335f0d2
ms.openlocfilehash: 4fcfa2e7ec2e8257407026ed4cca0e15fd0b5bb6


---
# <a name="managing-azure-web-app-using-azure-automation"></a>使用 Azure 自动化管理 Azure Web 应用程序
本指南将介绍 Azure 自动化服务，以及如何使用它来简化 Azure Web 应用程序的管理。

## <a name="what-is-azure-automation"></a>什么是 Azure 自动化？
[Azure 自动化](../automation/automation-intro.md)是用于通过流程自动化简化云管理的一项 Azure 服务。 使用 Azure 自动化可以自动完成那些人工操作、重复、长时间运行且易出错的任务，从而改善组织的可靠性、效率和价值生成时间。

Azure 自动化提供了具有高可靠性和高可用性的工作流执行引擎，该引擎可以根据你的需求进行扩展。 在 Azure 自动化中，流程可以手动、通过第三方系统或按计划的间隔启动，使任务能够完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT 和开发运营人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Azure 自动化如何帮助管理 Azure Web 应用程序？
可以使用 [Azure PowerShell 模块](/powershell/azureps-cmdlets-docs)中提供的 PowerShell cmdlet 在 Azure 自动化中管理 Web 应用。 可以[在 Azure 自动化中安装这些 Web 应用 PowerShell cmdlet](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/)，以便可以在服务中执行所有 Web 应用管理任务。 你还可以将 Azure 自动化中的这些 cmdlet 与其他 Azure 服务的 cmdlet 搭配使用，以自动完成跨 Azure 服务和第三方系统的复杂任务。

下面是使用自动化管理应用程序服务的一些示例：

* [用于管理 Web 应用的脚本](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>后续步骤
在了解 Azure 自动化 以及如何使用它来管理 Azure Web 应用程序的基础知识后，请使用以下链接了解有关 Azure 自动化的更多信息。

* 请参阅 Azure 自动化[入门教程](../automation/automation-first-runbook-graphical.md)




<!--HONumber=Feb17_HO2-->



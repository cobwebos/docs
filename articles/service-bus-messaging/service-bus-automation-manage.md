---
title: "使用 Azure 自动化管理 Azure 服务总线 | Microsoft 文档"
description: "了解如何使用 Azure 自动化服务来管理 Azure 服务总线。"
services: service-bus-messaging, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 2a06e94b-92ef-4b5d-a2b7-fe827063df82
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 4817cda757b9f85f6225237f79293860c990ca88


---
# <a name="managing-azure-service-bus-using-azure-automation"></a>使用 Azure 自动化管理 Azure Service Bus
本指南将介绍 Azure 自动化服务，以及如何使用它来简化 Azure Service Bus 的管理。

## <a name="what-is-azure-automation"></a>什么是 Azure 自动化？
[Azure 自动化](../automation/automation-intro.md)是用于通过流程自动化和所需的状态配置简化云管理的一项 Azure 服务。 使用 Azure 自动化可以自动完成那些人工操作、重复、长时间运行且易出错的任务，从而改善组织的可靠性、效率和价值生成时间。

Azure 自动化提供了具有高可靠性和高可用性的工作流执行引擎，该引擎可以根据你的需求进行扩展。 在 Azure 自动化中，流程可以手动、通过第三方系统或按计划的间隔启动，使任务能够完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT 和开发运营人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Azure 自动化如何帮助管理 Azure Service Bus？
可以使用[服务总线 REST API](https://docs.microsoft.com/rest/api/servicebus/)，通过 Azure 自动化管理服务总线。 在 Azure 自动化中，你可以运行 PowerShell 脚本来使用 REST API 执行许多服务总线任务。 你还可以将 Azure 自动化中的这些 REST API 调用与其他 Azure 服务的 cmdlet 搭配使用，以自动完成跨 Azure 服务和第三方系统的复杂任务。

下面是使用 PowerShell 管理 Azure 服务总线的一些示例：

* [Custom PowerShell cmdlets to manage Azure Service Bus queues](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)（自定义 PowerShell cmdlet 以管理 Azure 服务总线队列）
* [How to create Service Bus queues, topics and subscriptions using a PowerShell script](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)（如何使用 PowerShell 脚本创建服务总线队列、主题和订阅）
* [Create Azure Service Bus Namespaces using PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)（使用 PowerShell 创建 Azure 服务总线命名空间）

若要下载可在自动化 runbook 中用于 Azure 服务总线的 PowerShell 模块，可访问 [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0)（PowerShell 库）。

## <a name="next-steps"></a>后续步骤
在了解 Azure 自动化 以及如何使用它来管理 Azure Service Bus 的基础知识后，请使用以下链接了解有关 Azure 自动化的更多信息。

* 请参阅 Azure 自动化[入门教程](../automation/automation-first-runbook-graphical.md)
* 了解如何[使用 PowerShell 管理服务总线](service-bus-powershell-how-to-provision.md)




<!--HONumber=Jan17_HO2-->



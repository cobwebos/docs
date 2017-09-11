---
title: "使用 Azure 自动化管理 Azure 存储"
description: "了解如何使用 Azure 自动化服务来方便管理 Azure 存储。"
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.contentlocale: zh-cn
ms.lasthandoff: 08/22/2017

---
# <a name="managing-azure-storage-using-azure-automation"></a>使用 Azure 自动化管理 Azure 存储
本指南介绍 Azure 自动化服务，以及如何使用它来简化 Azure 存储 Blob、表和队列的管理。

## <a name="what-is-azure-automation"></a>什么是 Azure 自动化？
[Azure 自动化](https://azure.microsoft.com/services/automation/)是用于通过流程自动化简化云管理的一项 Azure 服务。 对于长时间运行、需人工操作、易出错和经常重复的任务，使用 Azure 自动化可自动提高其可靠性、效率并减少组织实现价值所需的时间。

Azure 自动化能够提供高度可靠且长期可用的工作流执行引擎，它可以在组织的发展过程中根据需求进行扩展。 利用 Azure 自动化，流程可以手动、通过第三方系统或按计划时间间隔启动，从而使任务完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT/开发运营人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Azure 自动化如何帮助管理 Azure 存储？
可以使用 [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx) 中提供的 PowerShell cmdlet 在 Azure 自动化中管理 Azure 存储。 Azure 自动化现成地提供了这些 Storage PowerShell cmdlet，因此，可以在该服务中执行所有 Blob、表和队列管理任务。 还可以将 Azure 自动化中的 cmdlet 与其他 Azure 服务的 cmdlet 搭配使用，以自动完成跨 Azure 服务和第三方系统的复杂任务。

[Azure 自动化 Runbook 库](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/)包含产品团队和社区提供的各种 Runbook，有助于开始自动管理 Azure 存储、其他 Azure 服务和第三方系统。 库中 Runbook 的功能包括：

* [使用自动化服务删除寿命达到特定天数的 Azure 存储 Blob](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [从 Azure 存储下载 Blob](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [为单个 Azure VM 或云服务中的所有 VM 备份所有磁盘](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>后续步骤
在了解 Azure 自动化 以及如何使用它来管理 Azure 存储 Blob、表和队列的基础知识后，请使用以下链接了解有关 Azure 自动化的更多信息。

请参阅 Azure 自动化教程[在 Azure 自动化中创建或导入 Runbook](../../automation/automation-creating-importing-runbook.md)。



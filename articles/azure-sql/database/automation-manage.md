---
title: 通过 Azure 自动化管理数据库
description: 了解如何使用 Azure 自动化服务大规模管理 Azure SQL 数据库。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 3d785edb82e62363dd96d9fd67447f1be7ffe8f5
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982593"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>使用 Azure 自动化管理 Azure SQL 数据库中的数据库

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本指南将向你介绍 Azure 自动化服务，以及如何使用它来简化 Azure SQL 数据库中的数据库管理。

## <a name="about-azure-automation"></a>关于 Azure 自动化

[Azure 自动化](https://azure.microsoft.com/services/automation/)是用于通过流程自动化简化云管理的一项 Azure 服务。 使用 Azure 自动化可以自动完成需要长时间运行、人工操作、易出错且经常重复的任务，从而改善组织的可靠性、效率和价值生成时间。 有关入门的信息，请参阅[Azure 自动化简介](../../automation/automation-intro.md)

Azure 自动化提供具有高可靠性和高可用性的工作流执行引擎，它可随着组织的发展而扩展以满足需求。 在 Azure 自动化中，流程可以手动、通过第三方系统或按计划的间隔启动，使任务能够完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT/开发运营人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Azure Automation 如何帮助管理数据库

使用 Azure 自动化，你可以使用[Azure PowerShell 工具](/powershell/azure/overview)中提供的[PowerShell CMDLET](https://docs.microsoft.com/powershell/module/servicemanagement/azure/#sql)管理 azure SQL 数据库中的数据库。 Azure Automation 现成提供了这些 Azure SQL 数据库 PowerShell cmdlet，因此，你可以在该服务中执行所有 SQL 数据库管理任务。 还可以将 Azure 自动化中的 cmdlet 与其他 Azure 服务的 cmdlet 搭配使用，以自动完成跨 Azure 服务和第三方系统的复杂任务。

Azure 自动化还可以通过使用 PowerShell 发出 SQL 命令，与 SQL Server 直接通信。

适用于[Azure 自动化](../../automation/automation-runbook-gallery.md)的 runbook 和模块库提供了来自 Microsoft 和社区的各种 runbook，你可以将其导入 Azure 自动化。 若要使用 Runbook，可以从库下载 Runbook，也可以直接从库或 Azure 门户中的自动化帐户导入 Runbook。

## <a name="next-steps"></a>后续步骤

现在，你已了解有关 Azure 自动化以及如何使用它来管理 Azure SQL 数据库的基础知识，请单击下面的链接以了解有关 Azure 自动化的详细信息。

- [Azure 自动化概述](../../automation/automation-intro.md)
- [我的第一个 Runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)

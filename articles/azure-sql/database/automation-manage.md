---
title: 使用 Azure 自动化来管理数据库
description: 了解如何使用 Azure 自动化服务来管理大规模的 Azure SQL 数据库。
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: faf5b1108e28b352a0b8622feed8bdd99264ff16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327572"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>使用 Azure 自动化管理 Azure SQL 数据库中的数据库

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

本指南介绍 Azure 自动化服务，以及如何使用它来简化 Azure SQL 数据库中数据库的管理。

## <a name="about-azure-automation"></a>关于 Azure 自动化

[Azure 自动化](https://azure.microsoft.com/services/automation/)是用于通过流程自动化简化云管理的一项 Azure 服务。 使用 Azure 自动化可以自动完成那些长时间运行、人工操作、易出错和经常重复的任务，从而改善组织的可靠性、效率和价值生成时间。 有关入门的信息，请参阅 [Azure 自动化简介](../../automation/automation-intro.md)

Azure 自动化提供了高度可靠且高度可用的工作流执行引擎，该引擎可以随着组织的发展根据需求进行扩展。 在 Azure 自动化中，流程可以手动、通过第三方系统或按计划的间隔启动，使任务能够完全根据需求进行。

通过将云管理任务改为由 Azure 自动化自动运行，可以降低运营开销，解放 IT/DevOps 人员，让他们将精力集中在增加企业价值的工作上。

## <a name="how-azure-automation-can-help-manage-your-databases"></a>如何借助 Azure 自动化管理数据库

通过 Azure 自动化，可以使用 [Azure PowerShell 工具](/powershell/azure/)中提供的 [PowerShell cmdlet](/powershell/module/servicemanagement/azure.service/#sql) 来管理 Azure SQL 数据库中的数据库。 Azure 自动化现成地提供了这些 Azure SQL 数据库 PowerShell cmdlet，因此，可以在该服务中执行所有 SQL 数据库管理任务。 还可以将 Azure 自动化中的这些 cmdlet 与其他 Azure 服务的 cmdlet 搭配使用，以便跨 Azure 服务和第三方系统自动完成复杂的任务。

Azure 自动化还可以通过使用 PowerShell 发出 SQL 命令，与 SQL 服务器直接通信。

适用于 [Azure 自动化](../../automation/automation-runbook-gallery.md) 的 runbook 和模块库提供了来自 Microsoft 和社区的各种 runbook，你可以将其导入 Azure 自动化。 若要使用 Runbook，可以从库下载 Runbook，也可以直接从库或 Azure 门户中的自动化帐户导入 Runbook。

## <a name="next-steps"></a>后续步骤

在了解 Azure 自动化 以及如何使用它来管理 Azure SQL 数据库的基础知识后，请使用以下链接了解有关 Azure 自动化的更多信息。

- [Azure 自动化概述](../../automation/automation-intro.md)
- [我的第一个 Runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)

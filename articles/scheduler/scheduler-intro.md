---
title: "什么是 Azure 计划程序？ | Microsoft Docs"
description: "Azure 计划程序允许以声明方式描述要在云中运行的操作。 然后，它自动计划并运行这些操作。"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-scheduler"></a>什么是 Azure 计划程序？
Azure 计划程序允许以声明方式描述要在云中运行的操作。 然后，它自动计划并运行这些操作。  计划程序使用 [Azure 门户](scheduler-get-started-portal.md)、代码、[REST API](https://msdn.microsoft.com/library/mt629143.aspx) 或 Azure PowerShell 执行此操作。

计划程序创建、维护并调用计划的工作。  计划程序不托管任何工作负荷或运行任何代码。 它仅调用别处（在 Azure 中、本地）托管的代码或与其他提供程序一起托管的代码。 它可以通过 HTTP、HTTPS、存储队列、服务总线队列或服务总线主题进行调用。

计划程序计划 [作业](scheduler-concepts-terms.md)、保留用户可以查看的作业执行结果历史记录，并确切可靠地计划要运行的工作负荷。 Azure WebJobs（Azure 应用服务中的 Web 应用功能的一部分）及其他 Azure 计划​​功能在后台使用计划程序。 [计划程序 REST API](https://msdn.microsoft.com/library/mt629143.aspx) 可帮助管理这些操作的通信。 因此，计划程序轻松地支持 [复杂的计划以及高级重复执行](scheduler-advanced-complexity.md) 。

有几种方案适合使用计划程序。 例如：

* *重复执行应用程序操作：*定期从 Twitter 收集数据并将数据收集到源。
* *日常维护：*每天删改日志、执行备份和其他维护任务。 例如，管理员可以选择在随后的 9 个月中，每天凌晨 1 点 备份数据库。

计划程序允许在门户中使用脚本以编程方式创建、更新、删除、查看和管理作业和 [作业集合](scheduler-concepts-terms.md)。

## <a name="see-also"></a>另请参阅
 [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)

 [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)

 [Azure 计划程序中的计划和计费](scheduler-plans-billing.md)

 [如何使用 Azure 计划程序生成复杂的计划和高级循环](scheduler-advanced-complexity.md)

 [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)

 [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)

 [Azure 计划程序的高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)

 [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)


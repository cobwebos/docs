---
title: "Azure 计划程序是什么？ |Microsoft 文档"
description: "Azure 计划程序允许你以声明方式描述在云中运行的操作。 然后，再计划，并自动运行这些操作。"
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
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="what-is-azure-scheduler"></a>Azure 计划程序是什么？
Azure 计划程序允许你以声明方式描述在云中运行的操作。 然后，再计划，并自动运行这些操作。  计划程序通过使用这一点[Azure 门户](scheduler-get-started-portal.md)，代码中， [REST API](https://msdn.microsoft.com/library/mt629143.aspx)，或 Azure PowerShell。

计划程序创建、 维护，并调用计划的工作。  计划程序不托管任何工作负荷，也不运行任何代码。 它仅*时，将调用*别处托管的代码-在 Azure 中，在本地或与其他提供程序。 它调用通过 HTTP、 HTTPS、 存储队列、 service bus 队列或服务总线主题。

计划程序计划[作业](scheduler-concepts-terms.md)，保留的作业执行结果历史记录，其中一个可以查看，并确切可靠地计划要运行的工作负荷。 Azure web 作业 （在 Azure App Service Web Apps 功能的一部分） 及其他 Azure 计划功能在后台使用计划程序。 [计划程序 REST API](https://msdn.microsoft.com/library/mt629143.aspx)可帮助管理这些操作的通信。 在这种情况下，计划程序支持[复杂的计划和高级重复执行](scheduler-advanced-complexity.md)轻松。

有几种方案适合使用计划程序。 例如：

* *重复性应用程序操作：*定期从 Twitter 收集数据，到馈送。
* *日常维护：*每天删改日志、 执行备份和其他维护任务。 例如，管理员可以选择在凌晨 1:00 备份数据库 下一步的 9 个月的每一天。

计划程序允许你创建、 更新、 删除、 查看和管理作业和[作业集合](scheduler-concepts-terms.md)以编程方式，通过使用脚本，以及在门户。

## <a name="see-also"></a>另请参阅
 [Azure 计划程序的概念、 术语和实体层次结构](scheduler-concepts-terms.md)

 [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)

 [计划和 Azure 计划程序中的计费](scheduler-plans-billing.md)

 [如何生成复杂的计划和高级重复执行使用 Azure 计划程序](scheduler-advanced-complexity.md)

 [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)

 [Azure 计划程序 PowerShell cmdlet 参考](scheduler-powershell-reference.md)

 [Azure 计划程序高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 计划程序的限制、 默认值和错误代码](scheduler-limits-defaults-errors.md)

 [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)


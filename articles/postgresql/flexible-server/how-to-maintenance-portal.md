---
title: Azure Database for PostgreSQL 灵活的服务器 (预览版) -计划的维护-Azure 门户
description: 了解如何从 Azure 门户为 Azure Database for PostgreSQL 灵活的服务器配置计划的维护设置。
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: be6040b8b84a4b86746d62bd2f1c07f0ffea0a3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336286"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--flexible-server"></a>管理 Azure Database for PostgreSQL –灵活服务器的计划维护设置
 
可以为 Azure 订阅中的每个可变服务器指定维护选项。 选项包括即将发生的和已完成的维护事件的维护计划和通知设置。

> [!IMPORTANT]
> Azure Database for PostgreSQL-灵活的服务器处于预览阶段。

## <a name="prerequisites"></a>先决条件
若要完成本操作指南，需要：
- [Azure Database for PostgreSQL 灵活的服务器](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>指定维护计划选项
 
1. 在 "PostgreSQL 服务器" 页上的 " **设置** " 标题下，选择 " **维护** " 以打开计划的维护选项。
2. 默认 (系统管理) 计划是一周中的某一天，而在晚上11点和7am-7pm 本地服务器时间之间的维护开始时间为60分钟。 如果要自定义此计划，请选择 " **自定义计划**"。 然后，您可以选择一周的首选日期和一个60分钟的时间段来维护开始时间。
 
## <a name="notifications-about-scheduled-maintenance-events"></a>有关计划的维护事件的通知
 
你可以使用 Azure 服务运行状况来查看有关即将发生的 [通知](../../service-health/service-notifications.md) 并在你的灵活的服务器上执行计划的维护。 还可以在 Azure 服务运行状况中 [设置](../../service-health/resource-health-alert-monitor-guide.md) 警报，以获取有关维护事件的通知。
 
## <a name="next-steps"></a>后续步骤  
 
* 了解 [Azure Database for PostgreSQL-灵活服务器中的计划内维护](concepts-maintenance.md)
* 了解 [Azure 服务运行状况](../../service-health/overview.md)

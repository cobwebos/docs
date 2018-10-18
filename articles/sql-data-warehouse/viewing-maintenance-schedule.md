---
title: Azure 维护计划（预览版）| Microsoft Docs
description: 维护计划可让客户围绕 Azure SQL 数据仓库服务用于推出新功能、升级和修补程序的必要计划性维护事件进行规划。
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228057"
---
# <a name="viewing-a-maintenance-schedule"></a>查看维护计划 

## <a name="portal"></a>门户

默认情况下，所有新创建的 Azure SQL 数据仓库实例会在部署期间应用 8 小时系统定义的主维护时段和辅助维护时段，只要部署完成即可进行编辑。 在未事先通知的情况下，不会在指定的维护时段外进行维护。
完成以下步骤，以查看已应用于门户中数据仓库的维护计划。

完成以下步骤，以查看已应用于门户中数据仓库的维护计划。
1.  登录到 [Azure 门户](https://portal.azure.com/)。
2.  选择要查看的数据仓库。 
3.  所选的 Azure SQL 数据仓库将在概述边栏选项卡中打开。 应用于所选数据仓库的维护计划将显示在“维护计划(预览版)”的下方。

![概述边栏选项卡](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>后续步骤
[了解有关使用 Azure Monitor 创建、查看和管理警报的详细信息](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage)。
[了解有关用于日志警报规则的 Webhook 操作的详细信息](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook)。
[了解有关 Azure 服务运行状况的详细信息](https://docs.microsoft.com/azure/service-health/service-health-overview)



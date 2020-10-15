---
title: 'Azure 对等互连服务：如何测量连接遥测数据 '
description: 在本教程中了解如何测量连接遥测数据。
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service.
ms.openlocfilehash: abbe69ebbaed56ed416f85fafa7b77a1740fabe7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84870548"
---
# <a name="tutorial-measure-peering-service-connection-telemetry"></a>教程：度量对等互连服务连接遥测数据

 在本教程中，你将了解如何测量对等互连服务连接的遥测数据。
 
 连接遥测数据就客户位置与 Microsoft 网络之间的连接收集了相关见解。 在本文中，你将了解如何查看特定 Azure 对等互连服务连接的延迟报表。 

若要测量对等互连服务连接的遥测数据，必须在 Azure 门户中注册对等互连服务连接。 若要了解如何注册连接，请参阅[注册对等互连服务连接 - Azure 门户](azure-portal.md)。


## <a name="view-a-latency-report"></a>查看延迟报表

若要查看特定对等互连服务连接的延迟报表，请按照以下步骤操作。

1. 在左窗格中选择“所有资源”，然后选择对等互连服务连接。 接下来，在“前缀”下选择“打开” 。 

   ![选择对等互连服务连接](./media/peering-service-measure/peering-service-measure-menu.png)

2. 此时将显示一个延迟报表页面，上面有与该对等互连服务连接相关联的所有前缀。 

      ![延迟报表页面](./media/peering-service-measure/peering-service-latency-report.png)

3. 默认情况下，本页面上显示的报表每小时更新一次。 若要查看不同时间线的报表，请从“显示以下最近时间的数据”中选择相应的选项。 

4. 若要查看特定前缀的事件，请在左窗格中选择前缀名称并选择“前缀事件”。 这将显示所捕获的事件。


   ![前缀事件](./media/peering-service-measure/peering-service-prefix-event.png)

 此处显示了“前缀事件”列表中捕获的一些可能的事件。

| **前缀事件** | **事件类型**|**原因**|
|-----------|---------|---------|
| PrefixAnnouncementEvent |信息|已收到前缀公告|
| PrefixWithdrawalEvent|警告| 已收到前缀撤销 |
| PrefixBackupRouteAnnouncementEvent |信息|已收到前缀备份路由公告 |
| PrefixBackupRouteWithdrawalEvent|警告|已收到前缀备份路由撤销 |
| PrefixActivePath |信息| 当前前缀活动路由   |
| PrefixBackupPath | 信息|当前前缀备份路由   |
| PrefixOriginAsChangeEvent|严重| 以不同源自治系统编号接收的准确前缀（适用于活动路由）| 
| PrefixBackupRouteOriginAsChangeEvent  | 错误|以不同源自治系统编号接收的的前缀（适用于备份路由）  |

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接，请参阅[对等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
---
title: Azure 应用服务 - 同步网络配置 | Microsoft Docs
description: 本文讨论了如何同步 Azure 应用服务托管计划的网络配置。
ms.service: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 57c4dd523a5dffc48a2d2d403d2a440a8d6cde95
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257882"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan"></a>Azure 应用服务托管计划的同步网络配置

虽然[已将应用与 Azure 虚拟网络集成](../app-service/web-sites-integrate-with-vnet.md)，仍可能出现无法建立与托管实例的连接的情况。 可尝试刷新服务计划的网络配置。 

## <a name="sync-network-configuration-for-app-service-hosting-plan"></a>应用服务托管计划的同步网络配置

为此，请执行以下步骤：  

1. 转到 Web 应用应用服务计划。
 
   ![应用服务计划](./media/sql-database-managed-instance-sync-networking/app-service-plan.png)

2. 单击“网络”，然后单击“单击此处进行管理”。
 
   ![管理服务计划](./media/sql-database-managed-instance-sync-networking/manage-plan.png)

3. 选择“VNet”，并单击“同步网络”。 
 
   ![同步网络](./media/sql-database-managed-instance-sync-networking/sync.png)

4. 请等待同步完成。
  
   ![同步完成](./media/sql-database-managed-instance-sync-networking/sync-done.png)

现在可尝试重新建立与托管实例的连接。

## <a name="next-steps"></a>后续步骤

- 有关配置托管实例的 VNet 的信息，请参阅[托管实例 VNet 配置](sql-database-managed-instance-vnet-configuration.md)。

---
title: Azure 应用服务 - 同步网络配置 | Microsoft Docs
description: 本文讨论了如何同步 Azure 应用服务托管计划的网络配置。
ms.service: sql-database
author: srdjan-bozovic
manager: craigg
ms.custom: managed instance
ms.topic: conceptual
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: ea8f4aae0324e201def6b9b6cd33b0e79042ebbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647963"
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

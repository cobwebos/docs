---
title: Azure App Service-同步网络配置
description: 本文讨论了如何同步 Azure 应用服务托管计划的网络配置。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: e7263d6a7716caf9f53e8496c6fb02b6d17b5509
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687896"
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

- 有关为托管实例配置 VNet 的信息，请参阅[托管实例 VNet 体系结构](sql-database-managed-instance-connectivity-architecture.md)和[如何配置现有 VNet](sql-database-managed-instance-configure-vnet-subnet.md)。

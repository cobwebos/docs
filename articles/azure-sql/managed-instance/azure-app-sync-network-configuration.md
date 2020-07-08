---
title: Azure App Service 的同步网络配置
titleSuffix: Azure SQL Managed Instance
description: 本文介绍如何通过 Azure SQL 托管实例将 Azure App Service 托管计划的网络配置同步。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 12/13/2018
ms.openlocfilehash: a0192f204fdb0797d98947a174bf6d669034b666
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84695447"
---
# <a name="sync-networking-configuration-for-azure-app-service-hosting-plan-with-azure-sql-managed-instance"></a>Azure SQL 托管实例的 Azure App Service 托管计划的同步网络配置
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

尽管将[应用与 Azure 虚拟网络集成](../../app-service/web-sites-integrate-with-vnet.md)，但却无法建立与 SQL 托管实例的连接，但可能会发生这种情况。 刷新或同步服务计划的网络配置可以解决此问题。 

## <a name="sync-network-configuration"></a>同步网络配置 

为此，请执行以下步骤：  

1. 转到 Web 应用应用服务计划。

   ![应用服务计划的屏幕截图](./media/azure-app-sync-network-configuration/app-service-plan.png)

2. 选择 "**网络**"，然后选择 **"单击此处进行管理"**。

   ![管理服务计划的屏幕截图](./media/azure-app-sync-network-configuration/manage-plan.png)

3. 选择“VNet”，并单击“同步网络”********。

   ![同步网络的屏幕截图](./media/azure-app-sync-network-configuration/sync.png)

4. 请等待同步完成。
  
   ![已完成同步的屏幕截图](./media/azure-app-sync-network-configuration/sync-done.png)

现在，你可以尝试重新建立与 SQL 托管实例的连接。

## <a name="next-steps"></a>后续步骤

- 有关为 SQL 托管实例配置 VNet 的信息，请参阅[sql 托管实例 VNet 体系结构](connectivity-architecture-overview.md)和[如何配置现有 VNet](vnet-existing-add-subnet.md)。

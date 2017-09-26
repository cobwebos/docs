---
title: "在 Azure 中创建 MySQL 数据库并连接到它"
description: "了解如何使用 Azure 门户创建 MySQL 数据库，并通过 Azure 中的 PHP Web 应用连接到该数据库。"
documentationcenter: php
services: app-service\web
author: cephalin
manager: erikre
editor: 
tags: mysql
ms.assetid: 55465a9a-7e65-4fd9-8a65-dd83ee41f3e5
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;cephalin
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: c072cb3a7d376d1e3c2b9f741f5410106e701256
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---
# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>在 Azure 中创建 MySQL 数据库并连接到它
本教程会演示如何在 [Azure 门户](https://portal.azure.com)中创建 MySQL 数据库（提供程序是 [ClearDB](http://www.cleardb.com/)），以及如何从 [Azure 应用服务](app-service/app-service-web-overview.md) 中运行的 PHP Web 应用连接到该数据库。

> [!NOTE]
> 还可以创建 MySQL 数据库作为<a href="https://portal.azure.com/#create/WordPress.WordPress" target="_blank">应用商店应用模板</a>的一部分。
>
>

## <a name="create-a-mysql-database-in-azure-portal"></a>在 Azure 门户中创建 MySQL 数据库
若要在 Azure 门户中创建 MySQL 数据库，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 从左侧菜单中，单击“新建” > “数据 + 存储” > “MySQL 数据库”。

    ![在 Azure 门户中创建 MySQL 数据库 - 开始](./media/store-php-create-mysql-database/create-db-1-start.png)
3. 在“新建 MySQL 数据库”[边栏选项卡](azure-portal-overview.md)中，按如下所示配置新的 MySQL 数据库（*边栏选项卡*：水平打开的门户页）：

   * **数据库名称**：键入可唯一标识的名称
   * **订阅**：选择要使用的订阅
   * **数据库类型**：对于低成本或免费层选择“共享”，或者选择“专用”以获取专用资源。
   * **资源组**：将 MySQL 数据库添加到现有[资源组](azure-resource-manager/resource-group-overview.md)，或将其放入新资源组。 同一组中的资源可以轻松地一起管理。
   * 位置：选择离你近的位置。 添加到现有资源组时，会被锁定到该资源组的位置。
   * **定价层**：单击“定价层”，并选择一个定价选项（**Mercury** 层是免费层），然后单击“选择”。
   * **法律条款**：单击“法律条款”，查看购买详细信息，并单击“购买”。
   * **固定到仪表板**：如果想要直接从仪表板访问它，请选择此项。 如果尚不熟悉门户导航，这会非常有用。

     下面的屏幕截图只是举例说明如何配置 MySQL 数据库。  
     ![在 Azure 门户中创建 MySQL 数据库 - 配置](./media/store-php-create-mysql-database/create-db-2-configure.png)
4. 完成配置后，请单击“创建”。

    ![在 Azure 门户中创建 MySQL 数据库 - 创建](./media/store-php-create-mysql-database/create-db-3-create.png)

    会看到弹出通知，告知你部署已启动。

    ![在 Azure 门户中创建 MySQL 数据库 - 正在进行](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    部署成功后，将收到另一个弹出通知。 该门户还会自动打开 MySQL 数据库边栏选项卡。

<a name="connect"></a>

## <a name="connect-to-your-mysql-database"></a>连接到 MySQL 数据库
若要查看新的 MySQL 数据库的连接信息，只需在 Web 应用的边栏选项卡中单击“属性”。

![在 Azure 中创建 MySQL 数据库 - MySQL 数据库边栏选项卡](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

现在可以在任何 Web 应用中使用该连接信息。 [此处](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql)提供了一个示例，演示如何通过简单的 PHP 应用使用连接信息。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [PHP 开发人员中心](/develop/php/)。


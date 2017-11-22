---
title: "使用 Azure 门户创建 Azure 数据库迁移服务实例 | Microsoft Docs"
description: "使用 Azure 门户创建 Azure 数据库迁移服务的实例"
services: database-migration
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/08/2017
ms.openlocfilehash: 4213a0ae73c38dcad403140e3d45e28f2d338020
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="create-a-database-migration-service-instance-using-the-azure-portal"></a>使用 Azure 门户创建数据库迁移服务实例
此快速入门中，使用 Azure 门户创建 Azure 数据库迁移服务的实例。  创建服务后，即可使用它将数据从本地 SQL Server 迁移到 Azure SQL 数据库。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户
打开 Web 浏览器，导航到 [Microsoft Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-azure-database-migration-service"></a>创建 Azure 数据库迁移服务
1. 单击“+”创建新服务。  数据库迁移服务仍是预览版。  

1. 在应用商店中搜索“迁移”，选择“数据库迁移服务(预览版)”，然后单击“创建”。

    ![创建迁移服务](media/quickstart-create-data-migration-service-portal/dms-create-service.png)

    - 选择易记忆且唯一的“服务名称”以标识 Azure 数据库迁移服务实例。
    - 选择要在其中创建数据库迁移服务的 Azure“订阅”。
    - 创建一个名称唯一的新“网络”。
    - 选择距离源或目标服务器最近的“位置”。
    - 为“定价层”选择“基础: 1 vCore”。

1. 单击“创建” 。

稍后，Azure 数据库迁移服务将创建并可供使用。  将看见图像中显示的数据库迁移服务。

![迁移服务已创建](media/quickstart-create-data-migration-service-portal/dms-service-created.png)

## <a name="clean-up-resources"></a>清理资源
可以通过删除 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)来清除在此快速入门中创建的资源。  若要删除资源组，请导航到所创建的数据库迁移服务，单击“资源组”名称，然后选择“删除资源组”。  此操作将删除资源组本身及其全部资产。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [将本地 SQL Server 迁移到 Azure SQL DB](tutorial-sql-server-to-azure-sql.md)
---
title: 将 SQL 数据库提供给 Azure Stack 用户使用 | Microsoft Docs
description: 有关安装 SQL Server 资源提供程序并创建产品/服务，使 Azure Stack 用户能够创建 SQL 数据库的教程。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: b9ba2bb89bb0d7e16a28a165cf14530a7a10f71b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234744"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>教程： 将 SQL 数据库提供给你 Azure 堆栈用户

Azure Stack 云管理员可以创建产品/服务，使用户（租户）能够创建 SQL 数据库，以配合其云原生应用、网站和工作负荷使用。 通过向用户提供这些基于云的按需自定义数据库的访问权限，可以节省用户的时间和资源。 若要设置此功能，需要：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品/服务

## <a name="deploy-the-sql-server-resource-provider"></a>部署 SQL Server 资源提供程序

在部署过程中将详细介绍[Azure 堆栈文章上的使用 SQL 数据库](azure-stack-sql-resource-provider-deploy.md)，和以下主要步骤组成：

1. [部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md)。
2. [验证部署](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)。
3. 通过连接到宿主 SQL 服务器来提供容量。 有关详细信息，请参阅[添加宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>创建产品

1.  [设置配额](azure-stack-setting-quotas.md)并将其命名为 *SQLServerQuota*。 在“命名空间”字段中选择“Microsoft.SQLAdapter”。
2.  [创建计划](azure-stack-create-plan.md)。 将计划命名为 *TestSQLServerPlan*，并选择“Microsoft.SQLAdapter”服务和“SQLServerQuota”配额。

    > [!NOTE]
    > 若要让用户创建其他应用，可能需要在计划中包含其他服务。 例如，Azure 函数需要**Microsoft.Storage**服务在计划中，而 Wordpress 需要**Microsoft.MySQLAdapter**。

3.  [创建产品/服务](azure-stack-create-offer.md)，将其命名为 **TestSQLServerOffer**，然后选择“TestSQLServerPlan”计划。

## <a name="test-the-offer"></a>测试产品/服务

部署 SQL Server 资源提供程序并创建产品/服务后，可以用户身份登录并订阅该产品/服务，然后创建数据库。

### <a name="subscribe-to-the-offer"></a>订阅产品/服务

1. 以租户身份登录到 Azure Stack 门户 (https://portal.local.azurestack.external)。
2. 选择**获取订阅**，然后输入**TestSQLServerSubscription**下**显示名称**。
3. 选择**选择产品** > **TestSQLServerOffer** > **创建**。
4. 选择**更多的服务** > **订阅** > **TestSQLServerSubscription** > **资源提供程序**。
5. 选择**注册**旁边**Microsoft.SQLAdapter**提供程序。

### <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 选择**+**  > **数据 + 存储** > **SQL 数据库**。
2. 保留默认值或为以下字段中使用这些示例：
    - **数据库名称**：SQLdb
    - **最大大小(MB)**：100
    - **订阅**：TestSQLOffer
    - **资源组**：SQL-RG
3. 选择**登录设置**，对于数据库中，输入凭据，然后选择**确定**。
4. 选择**SKU** > 选择你创建了 SQL 宿主服务器的 SQL SKU >，然后选择**确定**。
5. 选择**创建**。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品/服务

转到下一教程，了解如何执行以下操作：

> [!div class="nextstepaction"]
> [将 Web、移动和 API 应用提供给用户使用]( azure-stack-tutorial-app-service.md)
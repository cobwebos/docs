---
title: 将 SQL 数据库提供给 Azure Stack 用户使用 | Microsoft Docs
description: 有关安装 SQL Server 资源提供程序并创建产品，使 Azure Stack 用户能够创建 SQL 数据库的教程。
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
ms.date: 03/22/2017
ms.author: jeffgilb
ms.reviewer: ''
ms.custom: mvc
ms.openlocfilehash: f8d2dd65d9d427872fe78508ed0bcc61e644fdb0
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>将 SQL 数据库提供给 Azure Stack 用户使用
Azure Stack 云管理员可以创建产品，使用户（租户）能够创建 SQL 数据库，以配合其云本机应用程序、网站和工作负荷使用。 通过向用户提供这些基于云的按需自定义数据库的访问权限，可以节省用户的时间和资源。 若要设置此功能，需要：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品

## <a name="deploy-the-sql-server-resource-provider"></a>部署 SQL Server 资源提供程序

[在 Azure Stack 中使用 SQL 数据库](azure-stack-sql-resource-provider-deploy.md)一文中详细介绍了部署过程，该过程包括以下主要步骤：

1. [部署 SQL 资源提供程序]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider)。
2. [验证部署]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)。
3. 通过连接到宿主 SQL 服务器来提供容量。

## <a name="create-an-offer"></a>创建产品

1.  [设置配额](azure-stack-setting-quotas.md)并将其命名为 *SQLServerQuota*。 在“命名空间”字段中选择“Microsoft.SQLAdapter”。
2.  [创建计划](azure-stack-create-plan.md)。 将计划命名为 *TestSQLServerPlan*，并选择“Microsoft.SQLAdapter”服务和“SQLServerQuota”配额。

    > [!NOTE]
    > 若要让用户创建其他应用，可能需要在计划中包含其他服务。 例如，Azure Functions 要求计划中必须包含 **Microsoft.Storage** 服务，而 Wordpress 则要求包含 **Microsoft.MySQLAdapter**。
    > 
    >

3.  [创建产品](azure-stack-create-offer.md)，将其命名为 **TestSQLServerOffer**，然后选择“TestSQLServerPlan”计划。

## <a name="test-the-offer"></a>测试产品

部署 SQL Server 资源提供程序并创建产品后，可以用户身份登录并订阅该产品，然后创建数据库。

### <a name="subscribe-to-the-offer"></a>订阅产品
1. 登录到 Azure 堆栈门户 (https://portal.local.azurestack.external)作为租户。
2. 单击“获取订阅”，然后在“显示名称”下键入 **TestSQLServerSubscription**。
3. 单击“选择产品” > “TestSQLServerOffer” > “创建”。
4. 单击“更多服务” > “订阅” > “TestSQLServerSubscription” > “资源提供程序”。
5. 单击“Microsoft.SQLAdapter”提供程序旁边的“注册”。

### <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 单击“+” > “数据 + 存储” > “SQL 数据库”。
2. 将字段保留默认值，或者可以参考以下示例：
    - **数据库名称**：SQLdb
    - **最大大小(MB)**：100
    - **订阅**：TestSQLOffer
    - **资源组**：SQL-RG
3. 单击“登录设置”，输入数据库的凭据，然后单击“确定”。
4. 单击“SKU”，选择为 SQL 宿主服务器创建的 SQL SKU，然后单击“确定”。
5. 单击“创建”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品

转到下一教程，了解如何执行以下操作：

> [!div class="nextstepaction"]
> [将 Web、移动和 API 应用提供给用户使用]( azure-stack-tutorial-app-service.md)


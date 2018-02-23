---
title: "让 Azure 堆栈用户能够使用 SQL 数据库 |Microsoft 文档"
description: "安装 SQL Server 资源提供程序和创建教程提供了让 Azure 堆栈用户创建 SQL 数据库。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: brenduns
ms.reviewer: 
ms.custom: mvc
ms.openlocfilehash: e9fd74fa44bb9482ee2285f4305085ee6ff2fb73
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="make-sql-databases-available-to-your-azure-stack-users"></a>让 Azure 堆栈用户能够使用 SQL 数据库

作为 Azure 堆栈云管理员，你可以创建提供使你的用户 （租户） 创建它们可以使用其云本机应用程序、 网站和工作负荷的 SQL 数据库。 通过向你的用户提供这些自定义、 按需、 基于云的数据库，你可以将它们保存时间和资源。 若要对此进行设置，你将：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品/服务

## <a name="deploy-the-sql-server-resource-provider"></a>部署 SQL Server 资源提供程序

在部署过程中将详细介绍[Azure 堆栈文章上的使用 SQL 数据库](azure-stack-sql-resource-provider-deploy.md)，并且包含以下主要步骤：

1. [部署 SQL 资源提供程序]( azure-stack-sql-resource-provider-deploy.md#deploy-the-resource-provider)。
2. [验证部署]( azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)。
3. 通过连接到托管的 SQL server 提供的容量。

## <a name="create-an-offer"></a>创建产品

1.  [设置配额](azure-stack-setting-quotas.md)并将其命名*SQLServerQuota*。 选择**Microsoft.SQLAdapter**为**Namespace**字段。
2.  [创建计划](azure-stack-create-plan.md)。 将其命名为*TestSQLServerPlan*，选择**Microsoft.SQLAdapter**服务，和**SQLServerQuota**配额。

    > [!NOTE]
    > 以允许用户创建其他应用，其他服务可能要求在计划中。 例如，Azure 函数需要计划包含**Microsoft.Storage**服务，而 Wordpress 需要**Microsoft.MySQLAdapter**。
    > 
    >

3.  [创建提议](azure-stack-create-offer.md)，将其命名为**TestSQLServerOffer**和选择**TestSQLServerPlan**计划。

## <a name="test-the-offer"></a>测试产品/服务

既然您已经部署了 SQL Server 资源提供程序和创建提议，你可以以用户身份登录，订阅产品，并创建一个数据库。

### <a name="subscribe-to-the-offer"></a>订阅产品
1. 以租户身份登录到 Azure 堆栈门户 (https://portal.local.azurestack.external)。
2. 单击**获取订阅**然后键入**TestSQLServerSubscription**下**显示名称**。
3. 单击**选择产品** > **TestSQLServerOffer** > **创建**。
4. 单击**更多的服务** > **订阅** > **TestSQLServerSubscription** > **资源提供程序**。
5. 单击**注册**旁边**Microsoft.SQLAdapter**提供程序。

### <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 单击 **+**   > **数据 + 存储** > **SQL 数据库**。
2. 将字段的默认值，或者可以使用这些示例：
    - **数据库名称**: SQLdb
    - **以 mb 为单位的最大大小**: 100
    - **订阅**: TestSQLOffer
    - **资源组**: SQL RG
3. 单击**登录设置**，对于数据库中，输入凭据，然后单击**确定**。
4. 单击**SKU** > 选择你创建了 SQL 宿主服务器的 SQL SKU >**确定**。
5. 单击“创建”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品/服务

前进到下一步的教程，若要了解如何：

> [!div class="nextstepaction"]
> [向你的用户提供 web、 移动和 API 应用]( azure-stack-tutorial-app-service.md)


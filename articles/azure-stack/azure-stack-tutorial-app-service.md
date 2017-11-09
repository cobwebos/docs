---
title: "使 web、 移动和 API apps 适用于 Azure 堆栈用户 |Microsoft 文档"
description: "安装 App Service 资源提供程序和创建教程提供，为 Azure 堆栈用户提供能够创建 web、 移动和 API 应用。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 7/03/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: 2d011e933cb063eef88a372fccc49d2b9de19717
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="make-web-mobile-and-api-apps-available-to-your-azure-stack-users"></a>向 Azure 堆栈用户提供 web、 移动和 API 应用

作为 Azure 堆栈云管理员，你可以创建提供使你的用户 （租户） 创建 Azure 函数和 web、 移动和 API 的应用程序。 通过向你的用户提供访问按需、 基于云的应用，你可以将它们保存时间和资源。 若要对此进行设置，你将：

> [!div class="checklist"]
> * 部署应用程序服务资源提供程序
> * 创建产品
> * 测试产品/服务

## <a name="deploy-the-app-service-resource-provider"></a>部署应用程序服务资源提供程序

1. [准备 Azure 堆栈开发工具包主机](azure-stack-app-service-before-you-get-started.md)。 这包括部署 SQL Server 资源提供程序，这是创建一些应用程序需要。
2. [下载安装程序和帮助程序脚本](azure-stack-app-service-deploy.md)。
3. [运行用于创建所需的证书的帮助程序脚本](azure-stack-app-service-deploy.md)。
4. [安装 App Service 资源提供程序](azure-stack-app-service-deploy.md)(它将需要几个小时才能安装和所有辅助角色才会显示)。
5. [验证安装](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation)。

## <a name="create-an-offer"></a>创建产品

例如，你可以创建提议，使用户可以创建 DNN web 内容的管理系统。 它需要已启用通过安装 SQL Server 资源提供程序的 SQL Server 服务。

1.  [设置配额](azure-stack-setting-quotas.md)并将其命名*AppServiceQuota*。 选择**Microsoft.Web**为**Namespace**字段。
2.  [创建计划](azure-stack-create-plan.md)。 将其命名为*TestAppServicePlan*，选择**Microsoft.SQL**服务，和**AppService 配额**配额。

    > [!NOTE]
    > 以允许用户创建其他应用，其他服务可能要求在计划中。 例如，Azure 函数需要计划包含**Microsoft.Storage**服务，而 Wordpress 需要**Microsoft.MySQL**。
    > 
    >

3.  [创建提议](azure-stack-create-offer.md)，将其命名为**TestAppServiceOffer**和选择**TestAppServicePlan**计划。

## <a name="test-the-offer"></a>测试产品/服务

现在，部署 App Service 资源提供程序并创建提议后，你可以以用户身份登录、 订阅产品，和创建的应用程序。 对于此示例中，我们将创建一个 DNN 平台的内容管理系统。 你必须先创建 SQL 数据库，然后选择 DNN web 应用。

### <a name="subscribe-to-the-offer"></a>订阅产品
1. 以租户身份登录到 Azure 堆栈门户 (https://portal.local.azurestack.external)。
2. 单击**获取订阅**> 类型**TestAppServiceSubscription**下**显示名称** > **选择产品** >  **TestAppServiceOffer** > **创建**。

### <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 单击 **+**   > **数据 + 存储** > **SQL 数据库**。
2. 将字段的默认值，如下所示保留除外：
    - **数据库名称**: DNNdb
    - **以 mb 为单位的最大大小**: 100
    - **订阅**: TestAppServiceOffer
    - **资源组**: DNN RG
3. 单击**登录设置**，对于数据库中，输入凭据，然后单击**确定**。 在这些步骤的更高版本中，你将使用这些凭据。
4. 单击**SKU** > 选择你创建了 SQL 宿主服务器的 SQL SKU >**确定**。
5. 单击“创建” 。

### <a name="create-a-dnn-app"></a>创建 DNN 应用    

1. 单击 **+**   > **查看所有** > **DNN 平台预览版** > **创建**。
2. 类型*DNNapp*下**应用名称**和选择**TestAppServiceOffer**下**订阅**。
3. 单击**配置所需设置** > **新建**> 类型**App Service 计划**名称。
4. 单击**定价层** > **F1 免费** > **选择** > **确定**。
5. 单击**数据库**和输入前面创建的 SQL 数据库的信息。
6. 单击“创建” 。

本教程介绍了如何：

> [!div class="checklist"]
> * 部署应用程序服务资源提供程序
> * 创建产品
> * 测试产品/服务

前进到下一步的教程，若要了解如何：

> [!div class="nextstepaction"]
> [将应用部署到 Azure 和 Azure 堆栈](user/azure-stack-solution-pipeline.md)

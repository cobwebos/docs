---
title: 将 Web 和 API 应用提供给 Azure Stack 用户使用 | Microsoft Docs
description: 有关安装应用服务资源提供程序并创建产品/服务，使 Azure Stack 用户能够创建 Web 和 API 应用的教程。
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
ms.openlocfilehash: 0171dba639e480a04cdd1c7f23d546d01121fb42
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247392"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>教程： 向 web 应用和 API 应用提供你的 Azure 堆栈用户

Azure Stack 云管理员可以创建产品/服务，使用户（租户）能够创建 Azure Functions、Web 和 API 应用程序。 通过为你的用户访问提供对这些按需、 基于云的应用，你可以将它们保存时间和资源。

若要设置此功能，需要：

> [!div class="checklist"]
> * 部署应用服务资源提供程序
> * 创建产品
> * 测试产品/服务

## <a name="deploy-the-app-service-resource-provider"></a>部署应用服务资源提供程序

1. [准备 Azure Stack 开发工具包主机](azure-stack-app-service-before-you-get-started.md)。 这包括部署 SQL Server 资源提供程序（创建某些应用的先决条件）。
2. [下载安装程序和帮助器脚本](azure-stack-app-service-deploy.md)。
3. [运行帮助器脚本以创建所需的证书](azure-stack-app-service-deploy.md)。
4. [安装 App Service 资源提供程序](azure-stack-app-service-deploy.md)(它将需要几个小时才能安装和所有辅助角色才会出现。)
5. [验证安装](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation)。

## <a name="create-an-offer"></a>创建产品

例如，可以创建一个产品/服务来让用户创建 DNN Web 内容管理系统。 这需要用到已通过安装 SQL Server 资源提供程序启用的 SQL Server 服务。

1.  [设置配额](azure-stack-setting-quotas.md)并将其命名为 *AppServiceQuota*。 在“命名空间”字段中选择“Microsoft.Web”。
2.  [创建计划](azure-stack-create-plan.md)。 将其命名为*TestAppServicePlan*，选择**Microsoft.SQL**服务和**AppService 配额**配额。

    > [!NOTE]
    > 若要让用户创建其他应用，可能需要在计划中包含其他服务。 例如，Azure 函数需要**Microsoft.Storage**服务在计划中，而 Wordpress 需要**Microsoft.MySQL**。

3.  [创建产品/服务](azure-stack-create-offer.md)，将其命名为 **TestAppServiceOffer**，然后选择“TestAppServicePlan”计划。

## <a name="test-the-offer"></a>测试产品/服务

部署应用服务资源提供程序并创建产品/服务后，可以用户身份登录并订阅该产品/服务，然后创建应用。

本示例将创建一个 DNN 平台内容管理系统。 首先，创建 SQL 数据库，然后选择 DNN web 应用。

### <a name="subscribe-to-the-offer"></a>订阅产品/服务

1. 以租户身份登录到 Azure Stack 门户 (https://portal.local.azurestack.external)。
2. 选择**获取订阅**>，输入**TestAppServiceSubscription**下**显示名称** > **选择产品** >  **TestAppServiceOffer** > **创建**。

### <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 选择**+**  > **数据 + 存储** > **SQL 数据库**。
2. 保留默认值，以下字段除外：

    - **数据库名称**：DNNdb
    - **最大大小(MB)**：100
    - **订阅**：TestAppServiceOffer
    - **资源组**：DNN-RG

3. 选择**登录设置**，对于数据库中，输入凭据，然后选择**确定**。 你将在本教程后面使用这些凭据。
4. 下**SKU** > 选择你创建了 SQL 宿主服务器的 SQL SKU >，然后选择**确定**。
5. 选择**创建**。

### <a name="create-a-dnn-app"></a>创建 DNN 应用

1. 选择**+**  > **查看所有** > **DNN 平台预览版** > **创建**.
2. 输入*DNNapp*下**应用名称**和选择**TestAppServiceOffer**下**订阅**。
3. 选择**配置所需设置** > **新建**> 输入**App Service 计划**名称。
4. 选择**定价层** > **F1 免费** > **选择** > **确定**。
5. 选择**数据库**和输入前面创建的 SQL 数据库的凭据。
6. 选择**创建**。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 部署应用服务资源提供程序
> * 创建产品
> * 测试产品/服务

转到下一教程，了解如何执行以下操作：

> [!div class="nextstepaction"]
> [将应用部署到 Azure 和 Azure Stack](user/azure-stack-solution-pipeline.md)

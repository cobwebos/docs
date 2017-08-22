---
title: "5 分钟内在 Azure 门户中部署 Umbraco Web 应用 |Microsoft Docs"
description: "了解如何通过部署一个示例 ASP.NET 应用，轻松地在应用服务中运行 Web 应用。 将立即看到成果。"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: 6b1dede903083d1771733330a069b6ab533d9f00
ms.contentlocale: zh-cn
ms.lasthandoff: 04/06/2017

---
# <a name="deploy-an-umbraco-web-app-in-the-azure-portal-in-five-minutes"></a>5 分钟内在 Azure 门户中部署 Umbraco Web 应用

本教程介绍如何在数分钟内将 [Umbraco](https://our.umbraco.org/) Web 应用部署到 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md)。

![Umbraco 应用](./media/app-service-web-get-started-dotnet-portal/defaultpage.png)

## <a name="prerequisites"></a>先决条件
需要一个 Microsoft Azure 帐户。 如果没有帐户，可以[注册免费试用帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或者[激活 Visual Studio 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

> [!NOTE]
> 无需 Azure 帐户即可 [试用应用服务](https://azure.microsoft.com/try/app-service/) 。 创建入门级应用并使用长达一小时 — 无需信用卡，也无需做出承诺。
> 
> 

## <a name="deploy-the-aspnet-app"></a>部署 ASP.NET 应用
1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 打开 [https://portal.azure.com/#create/umbracoorg.UmbracoCMS](https://portal.azure.com/#create/umbracoorg.UmbracoCMS)。

    此链接是立即在 Azure 门户中配置新的 Umbraco 应用的快捷方式。

3. 在“应用名称”中，键入 Web 应用名称。 如果该名称在 `azurewebsites.net` 域中是唯一的，则会在框中看到绿色的复选标记。
   
5. 在“资源组”中，单击“新建”创建新的[资源组](../azure-resource-manager/resource-group-overview.md)，然后为其命名。

7. 单击“应用服务计划/位置” > “新建”。 按以下步骤配置[应用服务计划](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)：

    - 在“应用服务计划”中，键入所需名称。
    - 在“位置”中，选择用于托管计划的位置。
    - 单击“定价层”，然后选择“F1 免费”或适合你的其他层，然后单击“选择”。
    - 单击 **“确定”**。

    Umbraco CMS 配置现在应与以下屏幕截图中的内容相似：

    ![正在进行配置 - Azure 应用服务中的第一个 Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-in-progress.png)

12. 单击“SQL 数据库” > “创建一个新数据库”。 按以下步骤配置 SQL 数据库：

    - 在“名称”中，键入名称，例如 **myDB**。
    - 单击“定价层”，然后选择“F 免费”或适合你的其他层，然后单击“选择”。
    - 单击“目标服务器” > “创建一个新服务器”。 按以下步骤配置数据库服务器：

        - 在“服务器名称”中，键入服务器名称。 如果该名称在 `.database.windows.net` 域中是唯一的，则会在框中看到绿色的复选标记。
        - 在“服务器管理员登录名”中，键入所需的管理员用户名。
        - 在“密码”和“确认密码”中，键入所需的密码。
        - 在“位置”中，选择用于 Web 应用的同一位置。
        - 请确保选中“允许 Azure 服务访问服务器”。
        - 单击“选择”。
    
    - 单击“选择”。

13. 单击“Web 应用设置”，指定数据库用户名和密码，然后单击“确定”。

    Umbraco CMS 配置现在应与以下屏幕截图中的内容相似：

    ![配置完成 - Azure 应用服务中的第一个 Umbraco](./media/app-service-web-get-started-dotnet-portal/configure-complete.png)

14. 单击“创建” 。
    
    Azure 现在基于你的配置创建 Umbraco 应用。 将看到“部署已启动...”通知。

    ![部署成功 - Azure 应用服务中的第一个 Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-started.png)
   
## <a name="launch-and-manage-your-umrbaco-web-app"></a>启动和管理 Umrbaco Web 应用

Azure 完成应用部署时，你将看到另一条通知。

![部署成功 - Azure 应用服务中的第一个 Umbraco](./media/app-service-web-get-started-dotnet-portal/deployment-succeeded.png)

1. 单击该通知。 如果错过了这条通知，可始终通过单击通知铃铛获取它（![通知铃铛 - Azure 应用服务中的第一个 Umbraco](./media/app-service-web-get-started-dotnet-portal/notification.png)）。

    现在应可看到 Web 应用的管理[边栏选项卡](../azure-resource-manager/resource-group-portal.md#manage-resources)（边栏选项卡：水平打开的一个门户页面）。

3. 在“概述”页面顶部，单击“浏览”。
   
    ![浏览 - Azure 应用服务中的第一个 Umbraco](./media/app-service-web-get-started-dotnet-portal/browse.png)

    现在，可看到 Umbraco“欢迎”页面。 配置 Umbraco 安装并开始使用！

    ![Umbraco 配置 - Azure 应用服务中的第一个 Umbraco](./media/app-service-web-get-started-dotnet-portal/umbraco-config.png)
    
## <a name="next-steps"></a>后续步骤
* [使用 Visual Studio 将 ASP.NET Web 应用部署到 Azure 应用服务](app-service-web-get-started-dotnet.md) - 了解如何使用任意一种包含的应用程序模板从 Visual Studio 创建新的 Azure Web 应用。
* [将代码部署到 Azure 应用服务](web-sites-deploy.md) - 了解如何从 FTP 或从源代码管理存储库进行部署。
* [在第一个 Web 应用中添加功能](app-service-web-get-started-2.md) - 使 Azure 应用上升到更高的层次。 对用户进行身份验证。 按需缩放。 设置一些性能警报。 所有这些操作只需按几下鼠标即可完成。


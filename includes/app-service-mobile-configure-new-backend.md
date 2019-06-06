---
title: include 文件
description: include 文件
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7c994f85d90e94d514bb4e4f91a6644ed45432c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66455135"
---
1. 下载客户端为以下平台的 SDK 快速入门：
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > 如果使用 iOS 项目需要先下载"azuresdk-iOS-\*.zip"从[最新的 GitHub 版本](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)。 解压缩并添加`MicrosoftAzureMobile.framework`文件到项目的根。
    >

2. 你将需要添加数据库连接或连接到现有的连接。 首先，确定是否将创建一个数据存储区或使用现有。

    - **创建新的数据存储**:如果您将创建一个数据存储区，请使用以下快速入门：

        [快速入门：开始使用 Azure SQL 数据库中的单一数据库](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **现有数据源**:如果你想要使用现有的数据库连接，请执行下面的说明

        1. SQL 数据库连接字符串格式- `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** 名称的服务器，它可以找到你的数据库的概述页中，通常是"server_name.database.windows.net"窗体中。
            **{port}** 通常 1433年。
            **{your_catalogue}** 的数据库的名称。
            **{your_username}** 要访问您的数据库用户的名称。
            **{your_password}** 密码才能访问你的数据库。

            [了解有关 SQL 连接字符串格式的详细信息](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. 添加到连接字符串您**移动应用**在应用服务中，你可以管理连接字符串为应用程序通过使用**配置**菜单中的选项。

            若要添加的连接字符串：

            1. 单击**应用程序设置**选项卡。

            2. 单击 **[+] 新的连接字符串**。

            3. 将需要提供**名称**，**值**并**类型**连接字符串。

            4. 类型**名称**作为 `MS_TableConnectionString`

            5. 值应为在之前步骤中形成的连接字符串。

            6. 如果你将连接字符串添加到 SQL Azure 数据库选择**SQLAzure**下**类型**。

3. Azure 移动应用具有.NET 和 Node.js 后端 Sdk。

   - **Node.js 后端**
    
     如果您将使用 Node.js 快速入门应用程序，请按照下面的说明。

     1. 在 Azure 门户中，转到**简易表**，你将看到此屏幕。
      
        ![节点简易表](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. 请确保在已添加 SQL 连接字符串**配置**选项卡。然后选中的框**我确认此操作会覆盖所有站点内容**然后单击**创建 TodoItem 表**按钮。
     
        ![节点简易表配置](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. 在中**简易表**，单击 **+ 添加**按钮。
    
        ![节点简易表添加按钮](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. 创建`TodoItem`使用匿名访问的表。
      
        ![节点简易表中添加表](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET 后端**
    
        如果您将使用.NET 快速入门应用程序，请按照下面的说明。

        1. 下载 Azure 移动应用.NET 服务器项目从[快速入门 azure 移动应用的存储库](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)。

        2. 生成.NET 服务器项目在 Visual Studio 中本地。

        3. 在 Visual Studio 中，打开解决方案资源管理器，右键单击`ZUMOAPPNAMEService`项目中，单击**发布**，你将看到`Publish to App Service`窗口。 如果使用 Mac 上，查看将应用部署的其他方法[此处](https://docs.microsoft.com/azure/app-service/deploy-local-git)。
        
           ![Visual studio 发布](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. 选择**应用服务**为发布目标，然后单击**选择现有**，然后单击**发布**在窗口底部的按钮。

        5. 您将需要登录到 Visual Studio 与 Azure 订阅第一次。 选择`Subscription`， `Resource Group`，然后选择您的应用程序的名称。 准备就绪后，单击**确定**，这将应用服务后端到本地部署有.NET 服务器项目。 部署完成后，你将重定向到`http://{zumoappname}.azurewebsites.net/`在浏览器中。
        
           ![后端已启动](./media/app-service-mobile-configure-new-backend/backend-is-up.png)

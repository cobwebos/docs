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
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440224"
---
1. 下载以下平台的客户端 SDK 快速入门：
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > 如果使用 iOS 项目，则需从[最新 GitHub 版本](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest)中下载“azuresdk-iOS-\*.zip”。 将 `MicrosoftAzureMobile.framework` 文件解压缩并添加到项目的根目录中。
    >

2. 需要添加数据库连接，或者连接到现有的连接。 首先，确定是要创建数据存储，还是使用现有的数据存储。

    - **创建新的数据存储**：若要创建数据存储，请使用以下快速入门：

        [快速入门：开始使用 Azure SQL 数据库中的单一数据库](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **现有数据源**：若要使用现有的数据库连接，请按以下说明操作

        1. SQL 数据库连接字符串格式 - `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** ：服务器的名称，此项可以在数据库的概览页中找到，通常采用“server_name.database.windows.net”格式。
            **{port}** ：通常为 1433。
            **{your_catalogue}** ：数据库的名称。
            **{your_username}** ：用于访问数据库的用户名。
            **{your_password}** ：用于访问数据库的密码。

            [详细了解 SQL 连接字符串格式](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. 向**移动应用**添加连接字符串。可以在应用服务中管理应用程序的连接字符串，方法是使用菜单中的“配置”选项。 

            若要添加连接字符串，请执行以下操作：

            1. 单击“应用程序设置”  选项卡。

            2. 单击“[+] 新建连接字符串”。 

            3. 需为连接字符串提供“名称”、“值”和“类型”。   

            4. 键入 `MS_TableConnectionString` 作为“名称” 

            5. “值”应该是在前面的步骤中生成的连接字符串。

            6. 如果向 SQL Azure 数据库添加连接字符串，请在“类型”下选择“SQLAzure”。  

3. Azure 移动应用有适用于 .NET 和 Node.js 后端的 SDK。

   - **Node.js 后端**
    
     若要使用 Node.js 快速入门应用，请按以下说明操作。

     1. 在 Azure 门户中转到“简易表”即可看到以下屏幕。 
      
        ![Node 简易表](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. 确保已在“配置”选项卡中添加 SQL 连接字符串。  然后勾选“我已了解此操作会覆盖所有站点内容”框，并单击“创建 TodoItem 表”按钮   。
     
        ![Node 简易表的“配置”](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. 在“简易表”中单击“+ 添加”按钮。  
    
        ![Node 简易表的“添加”按钮](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. 创建允许匿名访问的 `TodoItem` 表。
      
        ![Node 简易表的“添加表”](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **.NET 后端**
    
        若要使用 .NET 快速入门应用，请按以下说明操作。

        1. 从 [azure-mobile-apps-quickstarts 存储库](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart)下载 Azure 移动应用 .NET 服务器项目。

        2. 在 Visual Studio 中以本地方式生成 .NET 服务器项目。

        3. 在 Visual Studio 中打开解决方案资源管理器，右键单击 `ZUMOAPPNAMEService` 项目，单击“发布”，  此时会出现“`Publish to App Service`”窗口。 如果在 Mac 上工作，请单击[此处](https://docs.microsoft.com/azure/app-service/deploy-local-git)了解部署应用的其他方式。
        
           ![Visual Studio 发布](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. 选择“应用服务”  作为发布目标，接着单击“选择现有”，然后单击窗口底部的“发布”按钮。  

        5. 首先需通过 Azure 订阅登录 Visual Studio。 选择 `Subscription`、`Resource Group`，然后选择应用的名称。 准备就绪后，单击“确定”，这样就会将本地的 .NET 服务器项目部署到应用服务后端。  部署完成后，系统会在浏览器中将你重定向到 `http://{zumoappname}.azurewebsites.net/`。                   
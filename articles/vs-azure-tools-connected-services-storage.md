---
title: "在 Visual Studio 中使用连接服务添加 Azure 存储 | Microsoft Docs"
description: "使用 Visual Studio 的“添加连接服务”对话框将 Azure 存储添加到您的应用"
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 521ec044-ad4b-4828-8864-01decde2e758
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c7ca2909de28936f17ab7ac03815d693032b04f


---
# <a name="adding-azure-storage-by-using-visual-studio-connected-services"></a>使用 Visual Studio 连接服务添加 Azure 存储
## <a name="overview"></a>概述
通过 Visual Studio 2015，可以使用“添加连接服务”对话框将任何 C# 云服务、.NET 后端移动服务、ASP.NET 网站或服务、ASP.NET 5 服务或 Azure WebJob 服务连接到 Azure 存储空间。 连接服务功能可添加所有需要的引用和连接代码，并相应地修改您的配置文件。 该对话框还可以将用户导航到相关文档以告知用户用来启动 blob 存储、队列和表的后续步骤是什么。

## <a name="supported-project-types"></a>支持的项目类型
可以在以下项目类型中使用“连接服务”对话框连接到 Azure 存储。

* ASP.NET Web 项目
* ASP.NET 5 项目
* .NET 云服务 Web 角色和辅助角色项目
* .NET 移动服务项目
* Azure WebJob 项目

## <a name="connect-to-azure-storage-using-the-connected-services-dialog"></a>使用“连接服务”对话框连接到 Azure 存储
1. 确保具有 Azure 帐户。 如果没有 Azure 帐户，可以注册[免费试用版](http://go.microsoft.com/fwlink/?LinkId=518146)。 一旦你有了 Azure 帐户，就可以创建存储帐户、创建移动服务和配置 Azure Active Directory。
2. 在 Visual Studio 中打开您的项目、在解决方案资源管理器中打开“引用”节点的上下文菜单，然后选择“添加连接服务”。
   
    ![添加连接服务](./media/vs-azure-tools-connected-services-storage/IC796702.png)
3. 在“添加连接服务”对话框中，选择“Azure 存储”，然后选择“配置”按钮。 如果尚未登录到 Azure，系统可能会提示你登录。
   
    ![“添加连接服务”对话框 - 存储](./media/vs-azure-tools-connected-services-storage/IC796703.png)
4. 在“Azure 存储”对话框中，选择一个现有的存储帐户，然后选择“添加”。
   
    如果需要创建新的存储帐户，请转到下一步。 否则，请跳到步骤 6。
   
    ![“Azure 存储”对话框](./media/vs-azure-tools-connected-services-storage/IC796704.png)
5. 新建存储帐户： 
   
   1. 单击“Azure 存储”对话框底部的“新建存储帐户”按钮。
   2. 填写“创建存储帐户”对话框，然后选择“创建”按钮。
      
       ![“Azure 存储”对话框](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)
      
       当回到“Azure 存储”对话框时，新的存储会显示在列表中。
   3. 在列表中选择新存储，然后单击“添加”。
6. 该存储连接服务将显示在 WebJob 项目的“服务引用”节点下。
   
    ![Web 作业项目中的 Azure 存储](./media/vs-azure-tools-connected-services-storage/IC796705.png)
7. 查看显示的入门页，了解您的项目的修改情况。 每次您添加连接服务时，浏览器中都会显示“入门”页。 您可以查看建议的后续步骤和代码示例，或切换到“完成的操作”页以查看您的项目添加了哪些引用，以及代码和配置文件的修改情况。

## <a name="how-your-project-is-modified"></a>您的项目的修改情况
完成该对话框后，Visual Studio 将添加引用并修改特定配置文件。 具体更改情况取决于项目类型。 

* 对于 ASP.NET 项目，请参阅[完成的操作 – ASP.NET 项目](http://go.microsoft.com/fwlink/p/?LinkId=513126)。 
* 对于 ASP.NET 5 项目，请参阅[完成的操作 – ASP.NET 5 项目](http://go.microsoft.com/fwlink/p/?LinkId=513124)。 
* 有关云服务项目（Web 角色和辅助角色），请参阅[完成的操作 – 云服务项目](http://go.microsoft.com/fwlink/p/?LinkId=516965)。 
* 对于 WebJob 项目，请参阅[完成的操作 – WebJob 项目](storage/vs-storage-webjobs-what-happened.md)。

## <a name="next-steps"></a>后续步骤
1. 使用入门代码示例作为指导，创建需要的存储类型，然后开始编写代码来访问存储帐户！
2. 提出问题并获得帮助
   
   * [MSDN 论坛：Azure 存储](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
   * [Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage/)
   * [azure.microsoft.com 上的存储](https://azure.microsoft.com/services/storage/)
   * [azure.microsoft.com 上的存储文档](https://azure.microsoft.com/documentation/services/storage/)




<!--HONumber=Nov16_HO3-->



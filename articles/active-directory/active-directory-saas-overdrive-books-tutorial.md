---
title: "教程：Azure Active Directory 与 Overdrive Books 集成 | Microsoft 文档"
description: "了解如何使用 Overdrive Books 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e68cede7-1130-4813-bd55-22a9a6fc6bf5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 66b3bbe84f966c3f8ec782fbf7c3bb20a8a832d0


---
# <a name="tutorial-azure-active-directory-integration-with-overdrive-books"></a>教程：Azure Active Directory 与 Overdrive Books 的集成
本教程的目的是介绍 Azure 与 OverDrive 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用 OverDrive 单一登录的订阅

完成本教程以后，分配到 OverDrive 的 Azure AD 用户就可以通过单一登录登录到 OverDrive 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 OverDrive 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-overdrive-books-tutorial/IC784462.png "Scenario")

## <a name="enabling-the-application-integration-for-overdrive"></a>为 OverDrive 启用应用程序集成
本部分的目的是概述如何为 OverDrive 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-overdrive-perform-the-following-steps"></a>若要为 OverDrive 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-overdrive-books-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-overdrive-books-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-overdrive-books-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-overdrive-books-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“OverDrive”。
   
   ![应用程序库](./media/active-directory-saas-overdrive-books-tutorial/IC784463.png "Application Gallery")
7. 在结果窗格中，选择“OverDrive”，然后单击“完成”添加该应用程序。
   
   ![OverDrive](./media/active-directory-saas-overdrive-books-tutorial/IC799950.png "OverDrive")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 OverDrive 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“OverDrive”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![启用单一登录](./media/active-directory-saas-overdrive-books-tutorial/IC784465.png "Enable single sign-on")
2. 在“你希望用户如何登录 OverDrive”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-overdrive-books-tutorial/IC784466.png "Configure single sign-on")
3. 在“配置应用 URL”页的“OverDrive 登录 URL”文本框中，使用“*http://mslibrarytest.libraryreserve.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-overdrive-books-tutorial/IC784467.png "Configure App URL")
4. 在“配置 OverDrive 的单一登录”页上下载元数据文件，然后将其发送到 OverDrive 支持团队。
   
   ![配置单一登录](./media/active-directory-saas-overdrive-books-tutorial/IC784468.png "Configure single sign-on")
   
   > [!NOTE]
   > OverDrive 支持团队会为用户配置单一登录，并在配置完成后向用户发送通知。
   > 
   > 
5. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-overdrive-books-tutorial/IC784469.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

没有相关操作项，因此无法通过配置将用户预配到 OverDrive。  
当已分配的用户尝试登录到 OverDrive 时，系统会根据需要自动创建 OverDrive 帐户。

> [!NOTE]
> 可以使用任何其他 OverDrive 用户帐户创建工具或 OverDrive 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-overdrive-perform-the-following-steps"></a>若要将用户分配到 OverDrive，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“OverDrive”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-overdrive-books-tutorial/IC784470.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-overdrive-books-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



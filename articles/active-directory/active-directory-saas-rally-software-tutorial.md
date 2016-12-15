---
title: "教程：Azure Active Directory 与 Rally Software 集成 | Microsoft 文档"
description: "了解如何使用 Rally Software 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c71cf608f162b0af04a436892dde9df3f80b8c02


---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>教程：Azure Active Directory 与 Rally Software 集成
本教程的目的是说明 Azure 与 Rally Software 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Rally Software 租户

在本教程中概述的方案由以下构建基块组成：

1. 为 Rally Software 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-rally-software-tutorial/IC769525.png "Scenario")

## <a name="enabling-the-application-integration-for-rally-software"></a>为 Rally Software 启用应用程序集成
本部分的目的是概述如何为 Rally Software 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-rally-software-perform-the-following-steps"></a>若要为 Rally Software 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-rally-software-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-rally-software-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-rally-software-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-rally-software-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“rally”。
   
   ![应用程序库](./media/active-directory-saas-rally-software-tutorial/IC769526.png "Application gallery")
7. 在结果窗格中，选择“Rally Software”，然后单击“完成”以添加该应用程序。
   
   ![Rally Software](./media/active-directory-saas-rally-software-tutorial/IC769527.png "Rally Software")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Rally Software 进行身份验证。  
在此过程中，需要将证书上载到 Rally Software。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Rally Software”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-rally-software-tutorial/IC749323.png "Configure single sign-on")
2. 在“你希望用户如何登录 Rally”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![Microsoft Azure AD 单一登录](./media/active-directory-saas-rally-software-tutorial/IC769528.png "Microsoft Azure AD Single Sign-On")
3. 在“配置应用 URL”页上的“Rally Software 租户 URL”文本框中，使用模式“*https://\<tenant-name\>.rally.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-rally-software-tutorial/IC769529.png "Configure App URL")
4. 在“配置 Rally 的单一登录”页上，单击“下载元数据”，然后将其保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-rally-software-tutorial/IC769530.png "Configure single sign-on")
5. 登录到 **Rally Software** 租户。
6. 在顶部工具栏中，单击“设置”，然后选择“订阅”。
   
   ![订阅](./media/active-directory-saas-rally-software-tutorial/IC769531.png "Subscription")
7. 单击右侧顶部的工具栏中的“操作”按钮，然后选择“编辑订阅”。
8. 在“订阅”对话框页上，执行以下步骤，然后单击“保存并关闭”：
   
   ![身份验证](./media/active-directory-saas-rally-software-tutorial/IC769542.png "Authentication")
   
   1. 从“身份验证”下拉列表中选择“Rally 或 SSO 身份验证”
   2. 在 Azure 经典门户的“配置 Rally Software 的单一登录”对话框页上，复制“标识提供者 ID”值，然后将其粘贴到“标识提供者 URL”文本框中
   3. 在 Azure 经典门户的“配置 Rally Software 的单一登录”对话框页上，复制“远程注销 URL”值。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-rally-software-tutorial/IC769547.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

要使 AAD 用户能够登录，必须使用其 Azure Active Directory 用户名将其预配到 Rally Software 应用程序中。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 登录到 Rally Software 租户。
2. 转到“设置”\>“用户”，然后单击“+ 新增”。
   
   ![用户](./media/active-directory-saas-rally-software-tutorial/IC781039.png "Users")
3. 在“新用户”文本框中键入名称，然后单击“添加详细信息”。
4. 在“创建用户”部分中，执行以下步骤：
   
   ![创建用户](./media/active-directory-saas-rally-software-tutorial/IC781040.png "Create User")
   
   1. 在“用户名”文本框中，键入要预配的 Azure AD 用户的名称。
   2. 在“电子邮件地址”文本框中，键入要预配的 Azure AD 用户的电子邮件地址。
   3. 单击“保存并关闭”。

> [!NOTE]
> 可以使用任何其他 Rally Software 用户帐户创建工具或 Rally Software 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-rally-software-perform-the-following-steps"></a>若要将用户分配到 Rally Software，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“Rally Software”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-rally-software-tutorial/IC769548.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-rally-software-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



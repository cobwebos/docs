---
title: "教程：Azure Active Directory 与 Wikispaces 的集成 | Microsoft Docs"
description: "了解如何使用 Wikispaces 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 665b95aa-f7f5-4406-9e2a-6fc299a1599c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e7761d07beebbbc68db7d3e30a26153bd8f9655


---
# <a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>教程：Azure Active Directory 与 Wikispaces 的集成
本教程的目的是说明 Azure 与 Wikispaces 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Wikispaces 单一登录的订阅

完成本教程后，已分配到 Wikispaces 的 Azure AD 用户可在 Wikispaces 公司站点（服务提供商发起的登录）单一登录到应用程序或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)。

在本教程中概述的方案由以下构建基块组成：

1. 启用 Wikispaces 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

## <a name="enabling-the-application-integration-for-wikispaces"></a>启用 Wikispaces 的应用程序集成
本部分旨在概述如何启用 Wikispaces 的应用程序集成。

### <a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>若要启用 Wikispaces 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入“Wikispaces”。
   
   ![应用程序库](./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Application Gallery")
7. 在“结果”窗格中，选择“Wikispaces”，然后单击“完成”以添加该应用程序。
   
   ![Wikispaces](./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分的目的是概述如何使用基于 SAML 协议的联合身份验证，使用户通过其 Azure AD 中的帐户对 Wikispaces 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Wikispaces”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 Wikispaces”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“Wikispaces 登录 URL”文本框中，使用“http://company.wikispaces.net”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configure App URL")
4. 在“配置 Wikispaces 的单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configure Single Sign-On")
5. 将元数据文件发送给 Wikispaces 支持团队。
   
   > [!NOTE]
   > 单一登录配置必须由 Wikispaces 支持团队执行。 配置完成后会立即收到通知。
   > 
   > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录到 Wikispaces，必须将其预配到 Wikispaces 中。  
对于 Wikispaces，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 **Wikispaces** 公司站点。
2. 转到“成员”。
   
   ![成员](./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Members")
3. 单击“邀请他人”。
   
   ![邀请他人](./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invite People")
4. 在“邀请他人”部分中，执行以下步骤：
   
   ![邀请他人](./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invite People")
   
   1. 在相关文本框中键入要预配的有效 AAD 帐户的“用户名或电子邮件地址”。
   2. 单击“发送”。  
      
      > [!NOTE]
      > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
      > 
      > 

> [!NOTE]
> 可以使用 Wikispaces 提供的任何其他 Wikispaces 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>若要将用户分配到 Wikispaces，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Wikispaces”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



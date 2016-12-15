---
title: "教程：Azure Active Directory 与 Greenhouse 的集成 | Microsoft Docs"
description: "了解如何使用 Greenhouse 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 78ec1766-4f79-4f16-9a66-d5584c4b6151
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 55a9a01414d930b40f60119318f5c023172141e1


---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>教程：Azure Active Directory 与 Greenhouse 集成
本教程的目的是说明 Azure 与 Greenhouse 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Greenhouse 单一登录订阅

完成本教程后，已向 Greenhouse 分配的 Azure AD 用户将能够在 Greenhouse 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Greenhouse 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-greenhouse-tutorial/IC790783.png "Scenario")

## <a name="enabling-the-application-integration-for-greenhouse"></a>为 Greenhouse 启用应用程序集成
本部分的目的是概述如何为 Greenhouse 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-greenhouse-perform-the-following-steps"></a>若要支持 Greenhouse 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-greenhouse-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-greenhouse-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-greenhouse-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-greenhouse-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“Greenhouse”。
   
   ![应用程序库](./media/active-directory-saas-greenhouse-tutorial/IC790784.png "Application Gallery")
7. 在结果窗格中，选择“Greenhouse”，然后单击“完成”以添加该应用程序。
   
   ![Greenhouse](./media/active-directory-saas-greenhouse-tutorial/IC790785.png "Greenhouse")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

此部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Greenhouse 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Greenhouse** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-greenhouse-tutorial/IC790786.png "Configure single sign-on")
2. 在“你希望用户如何登录 Greenhouse”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-greenhouse-tutorial/IC790787.png "Configure single sign-on")
3. 在“配置应用 URL”页上，在“登录 URL”文本框中，使用“*https://company.greenhouse.io*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-greenhouse-tutorial/IC790788.png "Configure App URL")
4. 在“配置 Greenhouse 的单一登录”**页面上，单击“下载元数据”**，然后将元数据文件本地保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-greenhouse-tutorial/IC790789.png "Configure single sign-on")
5. 将该元数据文件转发给 Greenhouse 支持团队。
   
   > [!NOTE]
   > 单一登录必须由 Greenhouse 支持团队来启用。
   > 
   > 
6. 在 Azure 经典门户中，选择单一登录配置确认，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-greenhouse-tutorial/IC790790.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 Greenhouse，必须将其预配到 Greenhouse。  
对于 Greenhouse，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 **Greenhouse** 公司站点。
2. 在顶部菜单中，单击“配置”，然后单击“用户”。
   
   ![用户](./media/active-directory-saas-greenhouse-tutorial/IC790791.png "Users")
3. 单击“新建用户”。
   
   ![新建用户](./media/active-directory-saas-greenhouse-tutorial/IC790792.png "New User")
4. 在“添加新用户”部分中，执行以下步骤：
   
   ![添加新用户](./media/active-directory-saas-greenhouse-tutorial/IC790793.png "Add New User")
   
   1. 在“输入用户电子邮件”文本框中，键入要预配的有效 Azure Active Directory 帐户的电子邮件地址。
   2. 单击“保存” 。
      
      > [!NOTE]
      > Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
      > 
      > 

> [!NOTE]
> 可以使用 Greenhouse 提供的任何其他 Greenhouse 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-greenhouse-perform-the-following-steps"></a>若要将用户分配到 Greenhouse，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Greenhouse”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-greenhouse-tutorial/IC790794.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-greenhouse-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



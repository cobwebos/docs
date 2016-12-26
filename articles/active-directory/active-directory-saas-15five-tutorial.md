---
title: "教程：Azure Active Directory 与 15Five 的集成 | Microsoft Docs"
description: "了解如何使用 15Five 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2fb301c2-7d7a-4046-8ee1-7dc9e7684806
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: ce98338e6b21eb35a17f0183f409dd54d1123bb9


---
# <a name="tutorial-azure-active-directory-integration-with-15five"></a>教程：Azure Active Directory 与 15Five 的集成
本教程旨在说明 Azure 与 15Five 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 15Five 单一登录的订阅

完成本教程后，分配到 15Five 的 Azure AD 用户就可以单一登录到 15Five 公司站点的应用程序（服务提供商启动的登录），或者参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 15Five 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-15five-tutorial/IC784667.png "Scenario")

## <a name="enabling-the-application-integration-for-15five"></a>为 15Five 启用应用程序集成
本部分旨在概述如何为 15Five 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-15five-perform-the-following-steps"></a>若要为 15Five 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-15five-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-15five-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-15five-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-15five-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入 **15Five**。
   
   ![应用程序库](./media/active-directory-saas-15five-tutorial/IC784668.png "Application Gallery")
7. 在结果窗格中，选择“15Five”，然后单击“完成”以添加该应用程序。
   
   ![15Five](./media/active-directory-saas-15five-tutorial/IC784669.png "15Five")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分旨在概述如何让用户使用基于 SAML 协议的联合身份验证通过其在 Azure AD 中的帐户对 15Five 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“15Five”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-15five-tutorial/IC784670.png "Configure single sign-on")
2. 在“你希望用户如何登录 15Five”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-15five-tutorial/IC784671.png "Configure single sign-on")
3. 在“配置应用 URL”页上的“15Five 登录 URL”文本框中，使用“*https://company.15Five.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-15five-tutorial/IC784672.png "Configure App URL")
4. 在“配置 15Five 的单一登录”页上，单击“下载元数据”，然后将元数据文件转发给 15Five 支持团队。
   
   ![配置单一登录](./media/active-directory-saas-15five-tutorial/IC784673.png "Configure single sign-on")
   
   > [!NOTE]
   > 单一登录需要由 15Five 支持团队启用。
   > 
   > 
5. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-15five-tutorial/IC784674.png "Configure single sign-on")
   

## <a name="configuring-user-provisioning"></a>配置用户设置

为使 Azure AD 用户能够登录到 15Five，必须将其预配到 15Five 中。  
就 15Five 来说，需要手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录到 **15Five** 公司站点。
2. 转到“管理公司”。
   
   ![管理公司](./media/active-directory-saas-15five-tutorial/IC784675.png "Manage Company")
3. 转到“人员\>添加人员”。
   
   ![人员](./media/active-directory-saas-15five-tutorial/IC784676.png "People")
4. 在“添加新人员”部分中，执行以下步骤：
   
   ![添加新人员](./media/active-directory-saas-15five-tutorial/IC784677.png "Add New Person")
   
   1. 在相关文本框中键入要预配的有效 Azure Active Directory 帐户的“名字”、“姓氏”、“职位”、“电子邮件地址”。
   2. 单击“Done”（完成） 。
   
   > [!NOTE]
   > Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
   > 
   > 



## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-15five-perform-the-following-steps"></a>若要将用户分配到 15Five，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“15Five”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-15five-tutorial/IC784678.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-15five-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



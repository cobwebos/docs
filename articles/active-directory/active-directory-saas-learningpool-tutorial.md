---
title: "教程：Azure Active Directory 与 Learningpool 的集成 | Microsoft Docs"
description: "了解如何将 Learningpool 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 51e8695f-31e1-4d09-8eb3-13241999d99f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 52fec971875bb797b5de679918528c5c472e4182


---
# <a name="tutorial-azure-active-directory-integration-with-learningpool"></a>教程：Azure Active Directory 与 Learningpool 的集成
本教程的目的是展示 Azure 与 Learningpool 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 一个启用了 Learningpool 单一登录的订阅

完成本教程后，分配到 Learningpool 的 Azure AD 用户将能够在你的 Learningpool 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 Learningpool 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-learningpool-tutorial/IC791166.png "Scenario")

## <a name="enabling-the-application-integration-for-learningpool"></a>为 Learningpool 启用应用程序集成
本部分的目的是概述如何为 Learningpool 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-learningpool-perform-the-following-steps"></a>若要为 Learningpool 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户中，在左侧导航窗格上，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-learningpool-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-learningpool-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-learningpool-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Learningpool”。
   
   ![应用程序库](./media/active-directory-saas-learningpool-tutorial/IC795073.png "Application Gallery")
7. 在结果窗格中，选择“Learningpool”，然后单击“完成”以添加应用程序。
   
   ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Learningpool 证明自己的身份。

Learningpool 应用程序需要特定格式的 SAML 断言，这要求向 **SAML 令牌属性**配置添加自定义属性映射。  
以下屏幕截图显示了一个示例。

![SAML 令牌属性](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML Token Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Learningpool** 应用程序集成页上，在顶部菜单中单击“属性”，以打开“SAML 令牌属性”对话框。
   
   ![属性](./media/active-directory-saas-learningpool-tutorial/IC795075.png "Attributes")
2. 若要添加所需的属性映射，请执行以下步骤：
   
   ### 
   | 属性名称 | 属性值 |
   | --- | --- |
   |  | |
   
   | urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname |
   | --- | --- |
   |  urn:oid:2.5.4.42 |User.givenname |
   | urn:oid:0.9.2342.19200300.100.1.3 |User.mail |
   | urn:oid:2.5.4.4 |User.surname |
   
   1. 对于上表中的每个数据行，单击“添加用户属性”。
   2. 在“属性名称”文本框中，键入为该行显示的属性名称。
   3. 在“属性值”列表中，选择为该行显示的属性值。
   4. 单击“完成”。
3. 单击“应用更改”。
4. 在浏览器中，单击“后退”以再次打开“快速启动”对话框。
5. 单击“配置单一登录”以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Configure Singel Sign-On")
6. 在“你希望用户如何登录到 Learningpool”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Configure Single Sign-On")
7. 在“配置应用 URL”页上，在“Learningpool 登录 URL”文本框中，键入用户用来登录到 Learningpool 应用程序的 URL（例如：https://parliament.preview.learningpool.com/auth/shibboleth/index.php），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-learningpool-tutorial/IC795078.png "Configure App URL")
8. 在“在 Learningpool 处配置单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将证书文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Configure Single Sign-On")
9. 将该元数据文件转发给 Learningpool 支持团队。
   
   > [!NOTE]
   > 单一登录必须由 Learningpool 支持团队来启用。
   > 
   > 
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Configure Single Sign-On")
    
    ## <a name="configuring-user-provisioning"></a>配置用户预配

为了使 Azure AD 用户能够登录到 Learningpool，必须将其预配到 Learningpool 中。

没有操作项可供你用来配置 Learningpool 的用户预配。  
需要由 Learningpool 支持团队来创建用户。

> [!NOTE]
> 可以使用 Learningpool 提供的任何其他 Learningpool 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配用户的方式向要允许其使用应用程序的 Azure AD 用户授予应用程序访问权限。

### <a name="to-assign-users-to-learningpool-perform-the-following-steps"></a>若要将用户分配到 Learningpool，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在 **Learningpool** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-learningpool-tutorial/IC795081.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-learningpool-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



---
title: "教程：Azure Active Directory 与 Clever 的集成 | Microsoft 文档"
description: "了解如何将 Clever 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fc748c70afede4be52e6c49db72932588be779ce


---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>教程：Azure Active Directory 与 Clever 的集成
本教程的目标是展示 Azure 与 Clever 的集成。 在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Clever 租户

完成本教程后，分配到 Clever 的 Azure AD 用户将能够在你的 Clever 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Clever 启用应用程序集成
2. 配置单一登录
3. 配置用户预配
4. 分配用户

![方案](./media/active-directory-saas-clever-tutorial/IC798977.png "Scenario")

## <a name="enabling-the-application-integration-for-clever"></a>为 Clever 启用应用程序集成
本部分的目的是概述如何为 Clever 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-clever-perform-the-following-steps"></a>若要为 Clever 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-clever-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-clever-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-clever-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Clever”。
   
   ![应用程序库](./media/active-directory-saas-clever-tutorial/IC798978.png "Application Gallery")
7. 在结果窗格中，选择“Clever”，然后单击“完成”以添加该应用程序。
   
   ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Clever 证明自己的身份。  
Clever 应用程序需要特定格式的 SAML 断言，这要求向 **SAML 令牌属性**配置添加自定义属性映射。  
以下屏幕截图显示一个示例。

![属性](./media/active-directory-saas-clever-tutorial/IC798980.png "Attributes")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Clever** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-clever-tutorial/IC784682.png "Configure Single Sign-On")
2. 在“你希望用户如何登录到 Clever”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-clever-tutorial/IC798981.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上，在“Clever 登录 URL”文本框中，键入用户用来登录 Clever 应用程序的 URL（例如：*https://clever.com/in/azsandbox*），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-clever-tutorial/IC798982.png "Configure App URL")
4. 在“在 Clever 处配置单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-clever-tutorial/IC798983.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Clever 公司站点。
6. 在工具栏中，单击“即时登录”。
   
   ![即时登录](./media/active-directory-saas-clever-tutorial/IC798984.png "Instant Login")
7. 在“即时登录”页上，执行以下步骤：
   
   ![即时登录](./media/active-directory-saas-clever-tutorial/IC798985.png "Instant Login")
   
   1. 键入“登录 URL”。  
      
      > [!NOTE]
      > “登录 URL”是一个自定义值。
      > 可以从 Clever 支持团队那里获取实际值。
      > 
      > 
   2. 对于“标识系统”，选择“ADFS”。
   3. 单击“保存”。
8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-clever-tutorial/IC798986.png "Configure Single Sign-On")
9. 在顶部菜单中单击“属性”，打开“SAML 令牌属性”对话框。
   
   ![属性](./media/active-directory-saas-clever-tutorial/IC795920.png "Attributes")
10. 若要添加所需的属性映射，请执行以下步骤：
    
    ![SAML 令牌属性](./media/active-directory-saas-clever-tutorial/IC795921.png "saml token attributes")
    
    | 属性名称 | 属性值 |
    | --- | --- |
    | clever.student.credentials.district\_username |User.userprincipalname |
    
    1. 对于上表中的每个数据行，单击“添加用户属性”。
    2. 在“属性名称”文本框中，键入为该行显示的属性名称。
    3. 在“属性值”文本框中，选择为该行显示的属性值。
    4. 单击“完成”。
11. 单击“应用更改”。

## <a name="configuring-user-provisioning"></a>配置用户预配
为了使 Azure AD 用户能够登录到 Clever，必须将其预配到 Clever 中。  
对于 Clever，预配是一项手动任务，需要由 Clever 支持团队来执行。

> [!NOTE]
> 可以使用 Clever 提供的任何其他 Clever 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-clever-perform-the-following-steps"></a>若要将用户分配到 Clever，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Clever** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-clever-tutorial/IC798987.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-clever-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



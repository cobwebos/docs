---
title: "教程：Azure Active Directory 与 Benefitsolver 集成 | Microsoft Docs"
description: "了解如何将 Benefitsolver 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: cf4529b1-3fb6-4475-82b7-2ceedcb70b3c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eb7dce1c0664e34c4946bb08f5b412e1e01c715d


---
# <a name="tutorial-azure-active-directory-integration-with-benefitsolver"></a>教程：Azure Active Directory 与 Benefitsolver 集成
本教程旨在展示 Azure 与 Benefitsolver 集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用的订阅上的 Benefitsolver 单一登录

完成本教程后，分配到 Benefitsolver 的 Azure AD 用户将能够通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 支持 Benefitsolver 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "方案")

## <a name="enabling-the-application-integration-for-benefitsolver"></a>支持 Benefitsolver 的应用程序集成
本部分旨在概述如何为 Benefitsolver 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-benefitsolver-perform-the-following-steps"></a>若要支持 Benefitsolver 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索框”中，键入“Benefitsolver”。
   
   ![应用程序库](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "应用程序库")
7. 在“结果”窗格中，选择“Benefitsolver”，然后单击“完成”，添加该应用程序。
   
   ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 Benefitsolver 进行身份验证。  
Benefitsolver 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 **SAML 令牌属性**配置。  
以下屏幕截图显示一个示例。

![属性](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "属性")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Benefitsolver”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "配置单一登录")
2. 在“你希望用户如何登录 Benefitsolver”页上，选择“Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "配置单一登录")
3. 在“配置应用设置”页上，执行以下步骤：
   
   ![配置应用设置](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "配置应用设置")
   
   1. 在“登录 URL”文本框中，键入“http://azure.benefitsolver.com”。
   2. 在“回复 URL”文本框中，键入“https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml”。  
   3. 单击“下一步”。
4. 在“配置 Benefitsolver 的单一登录”页面上，单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "配置单一登录")
5. 将下载的元数据文件发送到 Benefitsolver 支持团队。
   
   > [!NOTE]
   > 你的 Benefitsolver 支持团队需要真正配置 SSO。
   > 在为订阅启用了 SSO 后，你将收到通知。
   > 
   > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "配置单一登录")
7. 在顶部菜单中，单击“属性”，打开“SAML 令牌属性”对话框。
   
   ![属性](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "属性")
8. 若要添加所需的属性映射，请执行以下步骤：
   
   ![属性](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "属性")
   
   | 属性名称 | 属性值 |
   | --- | --- |
   | ClientID |需要从 Benefitsolver 支持团队获取此值。 |
   | ClientKey |需要从 Benefitsolver 支持团队获取此值。 |
   | LogoutURL |需要从 Benefitsolver 支持团队获取此值。 |
   | EmployeeID |需要从 Benefitsolver 支持团队获取此值。 |
   
   1. 对于上表中的每个数据行，单击“添加用户属性”。
   2. 在“属性名称”文本框中，键入为该行显示的属性名称。
   3. 在“属性值”文本框中，选择为该行显示的属性值。
   4. 单击“完成”。
9. 单击“应用更改”。

## <a name="configuring-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录 Benefitsolver，必须对其进行预配才能使其登录 Benefitsolver。  
就 Benefitsolver 来说，员工数据通过 HRIS 系统中的统计文件填充到应用程序中（通常是每个晚上进行）。  

> [!NOTE]
> 可使用其他任何 Benefitsolver 用户帐户创建工具或 Benefitsolver 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-benefitsolver-perform-the-following-steps"></a>若要将用户分配到 Benefitsolver，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Benefitsolver”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO5-->



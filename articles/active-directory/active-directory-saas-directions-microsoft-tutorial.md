---
title: "教程：Azure Active Directory 与 Directions on Microsoft 的集成 | Microsoft 文档"
description: "了解如何将 Directions on Microsoft 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e0c8986f-2acd-418d-a306-437abc44b640
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e1f768efb550dd0f232d64e93314bef858e5454a


---
# <a name="tutorial-azure-active-directory-integration-with-directions-on-microsoft"></a>教程：Azure Active Directory 与 Directions on Microsoft 的集成
本教程的目标是展示 Azure Active Directory 与 Directions on Microsoft 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Directions on Microsoft 订阅

如果尚没有联合 Directions on Microsoft 订阅，请通过电子邮件向“*service@DirectionsOnMicrosoft.com*”发送请求。

完成本教程后，分配到 Directions on Microsoft 的 Azure Active Directory 用户将能够以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Directions on Microsoft 启用应用程序集成
2. 配置单一登录
3. 配置用户预配
4. 分配用户

![方案](./media/active-directory-saas-directions-microsoft-tutorial/IC786877.png "Scenario")

## <a name="enabling-the-application-integration-for-directions-on-microsoft"></a>为 Directions on Microsoft 启用应用程序集成
本部分的目的是概述如何为 Directions on Microsoft 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-directions-on-microsoft-perform-the-following-steps"></a>若要为 Directions on Microsoft 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-directions-microsoft-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-directions-microsoft-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-directions-microsoft-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-directions-microsoft-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Directions on Microsoft”。
   
   ![应用程序库](./media/active-directory-saas-directions-microsoft-tutorial/IC786878.png "Application Gallery")
7. 在结果窗格中，选择“Directions on Microsoft”，然后单击“完成”以添加该应用程序。
   
   ![方案](./media/active-directory-saas-directions-microsoft-tutorial/IC793922.png "Scenario")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Directions on Microsoft 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Directions on Microsoft** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![启用单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786879.png "Enable Single Sign-On")
2. 在“你希望用户如何登录到 Directions on Microsoft”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![Microsoft Azure AD 单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786880.png "Microsoft Azure AD Singel Sign-On")
3. 在“配置应用 URL”页上，在“登录 URL”文本框中，键入 **https://www.directionsonmicrosoft.com/user/login**，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-directions-microsoft-tutorial/IC786881.png "Configure App URL")
4. 在“在 Directions on Microsoft 处配置单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786882.png "Configure Single Sign-On")
5. 将元数据文件发送给 Directions on Microsoft 支持团队 (*service@DirectionsOnMicrosoft.com*)。 若要使 Directions on Microsoft 支持团队能够找到你的联合网站成员资格，请在电子邮件中提供你的公司信息。
   
   > [!NOTE]
   > Directions on Microsoft 的单一登录需要由 Directions on Microsoft 支持团队来启用。
   > 当启用单一登录后，你将收到通知。
   > 
   > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-directions-microsoft-tutorial/IC786883.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户预配

没有操作项可供你用来配置 Directions on Microsoft 的用户预配。  
当已分配的用户尝试使用访问面板登录到 Directions on Microsoft 时，Directions on Microsoft 会检查该用户是否存在。 如果尚无用户帐户可用，Directions on Microsoft 将自动创建该帐户。

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-directions-on-microsoft-perform-the-following-steps"></a>若要将用户分配到 Directions on Microsoft，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Directions on Microsoft** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-directions-microsoft-tutorial/IC786884.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-directions-microsoft-tutorial/IC767830.png "Yes")




<!--HONumber=Nov16_HO3-->



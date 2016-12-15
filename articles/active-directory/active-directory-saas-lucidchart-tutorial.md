---
title: "教程：Azure Active Directory 与 Lucidchart 集成 | Microsoft Docs"
description: "了解如何将 Lucidchart 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 1068d364-11f3-43b5-bd6d-26f00ecd5baa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8386497b457f63ba0b62f50301ce3948ce060b97


---
# <a name="tutorial-azure-active-directory-integration-with-lucidchart"></a>教程：Azure Active Directory 与 Lucidchart 的集成
本教程的目的是展示 Azure 与 Lucidchart 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 一个启用了Lucidchart 单一登录的订阅

完成本教程后，分配到 Lucidchart 的 Azure AD 用户将能够在你的 Lucidchart 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 Lucidchart 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")

## <a name="enabling-the-application-integration-for-lucidchart"></a>为 Lucidchart 启用应用程序集成
本部分的目的是概述如何为 Lucidchart 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-lucidchart-perform-the-following-steps"></a>若要为 Lucidchart 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户中，在左侧导航窗格上，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Lucidchart”。
   
   ![应用程序库](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Application Gallery")
7. 在结果窗格中，选择“Lucidchart”，然后单击“完成”以添加该应用程序。
   
   ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Lucidchart 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Lucidchart** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")
2. 在“你希望用户如何登录到 Lucidchart”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上，在“Lucidchart 登录 URL”文本框中，键入用户用来登录 Lucidchart 应用程序的 URL（例如：“*https://chart2.office.lucidchart.com/saml/sso/azure*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")
4. 在“在 Lucidchart 处配置单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将数据文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Lucidchart 公司站点。
6. 在顶部菜单中，单击“团队”。
   
   ![团队](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")
7. 单击“应用程序”\>“管理 SAML”。
   
   ![管理 SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML")
8. 在“SAML 身份验证设置”对话框页上，执行以下步骤：
   
   1. 选择“启用 SAML 身份验证”，然后单击“可选”。
      ![SAML 身份验证设置](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings")
   2. 在“域”文本框中，键入你的域，然后单击“更改证书”。
      ![更改证书](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate")
   3. 打开下载的元数据文件，复制内容，然后将其粘贴到“上载元数据”文本框中。
      ![上载元数据](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata")
   4. 选择“自动将新用户添加到团队”，然后单击“保存更改”。
      ![保存更改](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")
9. 选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户预配

没有操作项可供你用来配置 Lucidchart 的用户预配。  
当已分配的用户尝试使用访问面板登录到 Lucidchart 时，Lucidchart 会检查该用户是否存在。  
如果尚无用户帐户可用，Lucidchart 将自动创建该帐户。

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配用户的方式向要允许其使用应用程序的 Azure AD 用户授予应用程序访问权限。

### <a name="to-assign-users-to-lucidchart-perform-the-following-steps"></a>若要将用户分配到 Lucidchart，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在 **Lucidchart** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



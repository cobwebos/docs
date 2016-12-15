---
title: "教程：Azure Active Directory 与 Intacct 集成 | Microsoft Docs"
description: "了解如何使用 Intacct 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 92518e02-a62c-4b1b-a8e9-2803eb2b49ac
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 5d0c072a57ca3afb89dffc88895f004d66b28cb8


---
# <a name="tutorial-azure-active-directory-integration-with-intacct"></a>教程：Azure Active Directory 与 Intacct 集成
本教程的目的是说明 Azure 与 Intacct 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Intacct 租户

完成本教程后，已向 Intacct 分配的 Azure AD 用户将能够在 Intacct 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Intacct 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")

## <a name="enabling-the-application-integration-for-intacct"></a>为 Intacct 启用应用程序集成
本部分的目的是概述如何为 Intacct 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>若要为 Intacct 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-intacct-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-intacct-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“Intacct”。
   
   ![应用程序库](./media/active-directory-saas-intacct-tutorial/IC790031.png "Application Gallery")
7. 在结果窗格中，选择“Intacct”，然后单击“完成”以添加该应用程序。
   
   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Intacct 进行身份验证。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户的“Intacct”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 Intacct”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上，在“Intacct 登录 URL”文本框中，使用模式“*https://Intacct.com/company*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configure App URL")
4. 在“配置 Intacct 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Intacct 公司站点。
6. 单击“公司”选项卡，然后单击“公司信息”。
   
   ![公司](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")
7. 单击“安全性”选项卡，然后单击“编辑”。
   
   ![“安全”](./media/active-directory-saas-intacct-tutorial/IC790038.png "Security")
8. 在“单一登录(SSO)”部分中，执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign On")
   
   1. 选择“启用单一登录”。
   2. 对于“标识提供者类型”，选择“SAML 2.0”。
   3. 在 Azure 经典门户的“配置 Intacct 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者 URL”文本框中。
   4. 在 Azure 经典门户的“配置 Intacct 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
   5. 基于下载的证书创建一个 **base-64 编码**的文件。
      
      > [!TIP]
      > 有关详细信息，请参阅[如何将二进制证书转化为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“证书”文本框中
   7. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 Intacct，必须将这些用户预配到 Intacct 中。  
对于 Intacct，预配是一项手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到 **Intacct** 租户。
2. 单击“公司”选项卡，然后单击“用户”。
   
   ![用户](./media/active-directory-saas-intacct-tutorial/IC790041.png "Users")
3. 单击“添加”选项卡
   
   ![添加](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")
4. 在“用户信息”部分中，执行以下步骤：
   
   ![用户信息](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")
   
   1. 在相关文本框中键入要预配的 Azure AD 帐户的**用户 ID**、**姓氏**、**名字**、**电子邮件地址**、**职务**和**电话**。
   2. 选择要预配的 Azure AD 帐户的**管理员特权** 。
   3. 单击“保存” 。
      
      > [!NOTE]
      > AAD 帐户持有者将收到一封电子邮件，并打开用于在激活帐户前确认其帐户的链接。
      > 
      > 

> [!NOTE]
> 可以使用 Intacct 提供的任何其他 Intacct 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-intacct-perform-the-following-steps"></a>若要将用户分配到 Intacct，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Intacct”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-intacct-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



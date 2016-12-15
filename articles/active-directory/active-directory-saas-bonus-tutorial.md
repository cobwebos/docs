---
title: "教程：Azure Active Directory 与 Bonus.ly 集成 | Microsoft Docs"
description: "了解如何使用 Bonus.ly 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 25a4e33ab85139bd3f871f46cef1d12a7b9cd06b


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>教程：Azure Active Directory 与 Bonus.ly 集成
本教程的目的是说明 Azure 与 Bonus.ly 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Bonus.ly 中的一个测试租户

在本教程中概述的方案由以下构建基块组成：

1. 为 Bonus.ly 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-bonus-tutorial/IC773679.png "Scenario")

## <a name="enabling-the-application-integration-for-bonusly"></a>为 Bonus.ly 启用应用程序集成
本部分旨在概述如何为 Bonus.ly 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-bonusly-perform-the-following-steps"></a>若要支持 Bonus.ly 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![启用单一登录](./media/active-directory-saas-bonus-tutorial/IC773680.png "Enable single sign-on")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-bonus-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-bonus-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-bonus-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“Bonus.ly”。
   
   ![应用程序库](./media/active-directory-saas-bonus-tutorial/IC773681.png "Application gallery")
7. 在“结果”窗格中，选择“Bonus.ly”，然后单击“完成”以添加该应用程序。
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Bonus.ly 证明自己的身份。  
配置 Bonus.ly 的单一登录要求检索证书的指纹值。  
如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Bonus.ly”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-bonus-tutorial/IC749323.png "Configure single sign-on")
2. 在“你希望用户如何登录 Bonus.ly”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-bonus-tutorial/IC773683.png "Configure single sign-on")
3. 在“配置应用 URL”页上的“Bonus.ly 租户 URL”文本框中，使用“*https://\<tenant-name\>.Bonus.ly*”模式键入 URL，然后单击“下一步”： 
   
   ![配置应用 URL](./media/active-directory-saas-bonus-tutorial/IC773684.png "Configure app URL")
4. 在“配置 Bonus.ly 的单一登录”页上，单击“下载证书”，然后将证书文件本地另存为 **c:\\Bonusly.cer**。
   
   ![配置单一登录](./media/active-directory-saas-bonus-tutorial/IC773685.png "Configure single sign-on")
5. 在另一浏览器窗口中，登录到 **Bonus.ly** 租户。
6. 在顶部工具栏中，单击“设置”，然后选择“集成和应用”。
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")
7. 在“单一登录”下选择“SAML”。
8. 在“SAML”对话框页上执行以下步骤：
   
   ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")
   
   1. 在 Azure 经典门户的“配置 Bonus.ly 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“IdP SSO 目标 URL”文本框中。
   2. 在 Azure 经典门户的“配置 Bonus.ly 的单一登录”对话框页上，复制“颁发者 ID”值，然后将其粘贴到“IdP 颁发者”文本框中。
   3. 在 Azure 经典门户的“配置 Bonus.ly 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“IdP 登录 URL”文本框中。
   4. 从导出的证书中复制“指纹”值，然后将其粘贴到“证书指纹”文本框中。
      
      > [!TIP]
      > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
      > 
      > 
9. 单击“保存”。
10. 在 Microsoft Azure 经典门户中，选择“配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-bonus-tutorial/IC773689.png "Configure single sign-on")
    
    ## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Bonus.ly，必须将其预配到 Bonus.ly 中。  
就 Bonus.ly 来说，预配任务需要手动完成。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 在 Web 浏览器窗口中登录到 Bonus.ly 租户。
2. 单击“设置”
   
   ![设置](./media/active-directory-saas-bonus-tutorial/IC781041.png "Settings")
3. 单击“用户和奖金”选项卡。
   
   ![用户和奖金](./media/active-directory-saas-bonus-tutorial/IC781042.png "Users and bonuses")
4. 单击“管理用户”。
   
   ![管理用户](./media/active-directory-saas-bonus-tutorial/IC781043.png "Manage Users")
5. 单击“添加用户”。
   
   ![添加用户](./media/active-directory-saas-bonus-tutorial/IC781044.png "Add User")
6. 在“添加用户”对话框中，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-bonus-tutorial/IC781045.png "Add User")
   
   1. 将要预配的有效 AAD 帐户的**电子邮件**、**名字**、**姓氏**键入到相关文本框中。
   2. 单击“保存” 。
   
   > [!NOTE]
   > AAD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
   > 
   > 

> [!NOTE]
> 可使用其他任何 Bonus.ly 用户帐户创建工具或 Bonus.ly 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-bonusly-perform-the-following-steps"></a>若要将用户分配到 Bonus.ly，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Bonus.ly”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-bonus-tutorial/IC773690.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-bonus-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



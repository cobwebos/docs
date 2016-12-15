---
title: "教程：Azure Active Directory 与 TeamSeer 集成 | Microsoft 文档"
description: "了解如何使用 TeamSeer 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6ec4806f-fe0f-4ed7-8cfa-32d1c840433f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d978744bfecddc43c0bf5e803f1fa6bc281ee79b


---
# <a name="tutorial-azure-active-directory-integration-with-teamseer"></a>教程：Azure Active Directory 与 TeamSeer 集成
本教程的目的是说明 Azure 与 TeamSeer 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* TeamSeer 租户

完成本教程后，已向 TeamSeer 分配的 Azure AD 用户将能够在 TeamSeer 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 TeamSeer 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-teamseer-tutorial/IC789618.png "Scenario")

## <a name="enabling-the-application-integration-for-teamseer"></a>为 TeamSeer 启用应用程序集成
本部分的目的是概述如何为 TeamSeer 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-teamseer-perform-the-following-steps"></a>若要为 TeamSeer 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-teamseer-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-teamseer-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-teamseer-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-teamseer-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“TeamSeer”。
   
   ![应用程序库](./media/active-directory-saas-teamseer-tutorial/IC789619.png "Application Gallery")
7. 在结果窗格中，选择“TeamSeer”，然后单击“完成”以添加该应用程序。
   
   ![TeamSeer](./media/active-directory-saas-teamseer-tutorial/IC789620.png "TeamSeer")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 TeamSeer 进行身份验证。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“TeamSeer”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-teamseer-tutorial/IC789621.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 TeamSeer”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-teamseer-tutorial/IC789628.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“TeamSeer 登录 URL”文本框中，使用模式“*http://www.teamseer.com/companyid*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-teamseer-tutorial/IC789629.png "Configure App URL")
4. 在“配置 TeamSeer 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-teamseer-tutorial/IC789630.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 TeamSeer 公司站点。
6. 转到“HR 管理员”。
   
   ![HR 管理员](./media/active-directory-saas-teamseer-tutorial/IC789634.png "HR Admin")
7. 单击“设置”。
   
   ![设置](./media/active-directory-saas-teamseer-tutorial/IC789635.png "Setup")
8. 单击“设置 SAML 提供程序详细信息”。
   
   ![SAML 设置](./media/active-directory-saas-teamseer-tutorial/IC789636.png "SAML Settings")
9. 在“SAML 提供程序详细信息”部分中，执行以下步骤：
   
   ![SAML 设置](./media/active-directory-saas-teamseer-tutorial/IC789637.png "SAML Settings")
   
   1. 在 Azure 经典门户的“配置 TeamSeer 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“URL”文本框中。
   2. 根据下载的证书创建 **base-64 编码**文件。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   3. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“IdP 公共证书”文本框中。
10. 若要完成 SAML 提供程序配置，请执行以下步骤：
    
    ![SAML 设置](./media/active-directory-saas-teamseer-tutorial/IC789638.png "SAML Settings")
    
    1. 在“测试电子邮件地址”中，键入测试用户的电子邮件地址。
    2. 在“颁发者”文本框中，键入服务提供商的颁发者 URL。
    3. 单击“保存” 。
11. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-teamseer-tutorial/IC789639.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 TeamSeer，必须将这些用户预配到 TeamSeer 中。  
对于 TeamSeer，预配是一项手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 **TeamSeer** 公司站点。
2. 执行以下步骤：
   
   ![HR 管理员](./media/active-directory-saas-teamseer-tutorial/IC789640.png "HR Admin")
   
   1. 转到“HR 管理员”\>“用户”。
   2. 单击“运行新建用户向导”。
3. 在“用户详细信息”部分中，执行以下步骤：
   
   ![用户详细信息](./media/active-directory-saas-teamseer-tutorial/IC789641.png "User Details")
   
   1. 在相关文本框中键入要预配的有效 AAD 帐户的“名称”、“姓氏”和“用户名(电子邮件地址)”。
   2. 单击“资源组名称” 的 Azure 数据工厂。
4. 按照屏幕上的说明添加新用户，然后单击“完成”。

> [!NOTE]
> 可以使用任何其他 TeamSeer 用户帐户创建工具或 TeamSeer 提供的 API 来预配 Azure AD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-teamseer-perform-the-following-steps"></a>若要将用户分配到 TeamSeer，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“TeamSeer”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-teamseer-tutorial/IC789642.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-teamseer-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



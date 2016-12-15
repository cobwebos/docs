---
title: "教程：Azure Active Directory 与 Zscaler Beta 集成 | Microsoft Docs"
description: "了解如何使用 Zscaler Beta 与 Azure Active Directory 以启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 56b846ae-a1e7-45ae-a79d-992a87f075ba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 54dd9ad56ac6cbb842597737a2c1841fac903c01


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>教程：Azure Active Directory 与 Zscaler Beta 集成
本教程旨在介绍 Azure 与 ZScaler Beta 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 ZScaler Beta 单一登录的订阅

完成本教程后，已分配到 ZScaler Beta 的 Azure AD 用户可在 ZScaler Beta 公司站点（服务提供商发起的登录）单一登录到应用程序或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)。

在本教程中概述的方案由以下构建基块组成：

1. 启用 ZScaler Beta 的应用程序集成
2. 配置单一登录
3. 配置代理设置
4. 配置用户设置
5. 分配用户

![方案](./media/active-directory-saas-zscaler-beta-tutorial/IC800223.png "Scenario")

## <a name="enabling-the-application-integration-for-zscaler-beta"></a>启用 ZScaler Beta 的应用程序集成
本部分旨在概述如何启用 ZScaler Beta 的应用程序集成。

### <a name="to-enable-the-application-integration-for-zscaler-beta-perform-the-following-steps"></a>若要启用 ZScaler Beta 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-zscaler-beta-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-zscaler-beta-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-zscaler-beta-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-zscaler-beta-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入“ZScaler Beta”。
   
   ![应用程序库](./media/active-directory-saas-zscaler-beta-tutorial/IC800224.png "Application Gallery")
7. 在“结果”窗格中，选择“ZScaler Beta”，然后单击“完成”以添加该应用程序。
   
   ![ZScaler One](./media/active-directory-saas-zscaler-beta-tutorial/IC800216.png "ZScaler One")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分旨在概述如何使用基于 SAML 协议的联合身份验证，使用户通过其 Azure AD 中的帐户对 ZScaler Beta 进行身份验证。  
在此过程中，需要将 base-64 编码的证书上传到 ZScaler Beta 租户。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户的“ZScaler Beta”应用程序集成页上，单击“配置单一登录”打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-zscaler-beta-tutorial/IC800225.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 ZScaler Beta”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-zscaler-beta-tutorial/IC800226.png "Configure Single Sign-On")
3. 在“配置应用 URL”页的“ZScaler Beta 登录 URL”文本框中，键入用户登录到 ZScaler Beta 应用程序时使用的 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-zscaler-beta-tutorial/IC800227.png "Configure App URL")
   
   > [!NOTE]
   > 如果需要，可以从 ZScaler Beta 支持团队获取你的环境的实际值。
   > 
   > 
4. 在“配置 ZScaler Beta 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-zscaler-beta-tutorial/IC800228.png "Configure Single Sign-On")
5. 在其他 Web 浏览器窗口中，以管理员身份登录到 ZScaler Beta 公司站点。
6. 在顶部菜单中，单击“管理”。
   
   ![管理](./media/active-directory-saas-zscaler-beta-tutorial/IC800206.png "Administration")
7. 在“管理管理员和角色”下，单击“管理用户和身份验证”。
   
   ![管理用户和身份验证](./media/active-directory-saas-zscaler-beta-tutorial/IC800207.png "Manage Users & Authentication")
8. 在“选择组织的身份验证选项”部分中，执行以下步骤：
   
   ![身份验证](./media/active-directory-saas-zscaler-beta-tutorial/IC800208.png "Authentication")
   
   1. 选择“使用 SAML 单一登录进行身份验证”。
   2. 单击“配置 SAML 单一登录参数”。
9. 在“配置 SAML 单一登录参数”对话框页上，执行以下步骤，然后单击“完成”：
   
   ![单一登录](./media/active-directory-saas-zscaler-beta-tutorial/IC800209.png "Single Sign-On")
   
   1. 在 Azure 经典门户的“配置 ZScaler Beta 的单一登录”对话框页上，复制“身份验证请求 URL”值，然后将其粘贴到“用户发送的用于身份验证的 SAML 门户 URL”文本框中。
   2. 在“包含登录名的属性”文本框中，键入“NameID”。
   3. 若要上传已下载的证书，请单击“Zscaler pem”。
   4. 选择“启用 SAML 自动预配”。
10. 在“配置用户身份验证”对话框页上，执行以下步骤：
    
    ![管理](./media/active-directory-saas-zscaler-beta-tutorial/IC800210.png "Administration")
    
    1. 单击“保存” 。
    2. 单击“立即激活”。
11. 在 Azure 经典门户中的“配置 ZScaler Beta 的单一登录”对话框页上，选择“单一登录配置确认”，然后单击“完成”。
    
    ![配置单一登录](./media/active-directory-saas-zscaler-beta-tutorial/IC800229.png "Configure Single Sign-On")

## <a name="configuring-proxy-settings"></a>配置代理设置
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>在 Internet Explorer 中配置代理设置
1. 启动 **Internet Explorer**。
2. 从“工具”菜单中选择“Internet 选项”，打开“Internet 选项”对话框。
   
   ![，](./media/active-directory-saas-zscaler-beta-tutorial/IC769492.png "Internet Options")
3. 单击“连接”选项卡。
   
   ![连接](./media/active-directory-saas-zscaler-beta-tutorial/IC769493.png "Connections")
4. 单击“LAN 设置”，打开“LAN 设置”对话框。
5. 在“代理服务器”部分中，执行以下步骤：
   
   ![代理服务器](./media/active-directory-saas-zscaler-beta-tutorial/IC769494.png "Proxy server")
   
   1. 选择“为 LAN 使用代理服务器”。
   2. 在“地址”文本框中，键入 **gateway.zscalerBeta.net**。
   3. 在“端口”文本框中，键入 **80**。
   4. 选择“对本地地址不使用代理服务器”。
   5. 单击“确定”，关闭“局域网(LAN)设置”对话框。
6. 单击“确定”，关闭“Internet 选项”对话框。

## <a name="configuring-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录到ZScaler Beta，必须将其预配到 ZScaler Beta 中。  
对于 ZScaler Beta，需要手动执行预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 登录到 **Zscaler** 租户。
2. 单击“管理”。
   
   ![管理](./media/active-directory-saas-zscaler-beta-tutorial/IC781035.png "Administration")
3. 单击“用户管理”。
   
   ![添加](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Add")
4. 在“用户”选项卡上，单击“添加”。
   
   ![添加](./media/active-directory-saas-zscaler-beta-tutorial/IC781037.png "Add")
5. 在“添加用户”部分中，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-zscaler-beta-tutorial/IC781038.png "Add User")
   
   1. 键入“UserID”、“用户显示名称”、“密码”并单击“确认密码”，然后选择想要预配的有效 AAD 帐户的“组”和“部门”。
   2. 单击“保存” 。

> [!NOTE]
> 可以使用 ZScaler Beta 提供的任何其他 ZScaler Beta 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-zscaler-beta-perform-the-following-steps"></a>若要将用户分配到 ZScaler Beta，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“ZScaler Beta”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-zscaler-beta-tutorial/IC800230.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-zscaler-beta-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->



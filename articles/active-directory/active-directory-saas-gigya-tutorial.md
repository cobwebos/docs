---
title: "教程：Azure Active Directory 与 Gigya 集成 | Microsoft Docs"
description: "了解如何使用 Docs 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 91f8a95bdab98f079b748b5391e9b611378c6e79
ms.openlocfilehash: 040cfb1c9f5a0b6a12c62ca6706576c4173c0636


---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>教程：Azure Active Directory 与 Gigya 集成
本教程的目的是说明 Azure 与 Gigya 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Gigya 单一登录的订阅

完成本教程后，分配到 Gigya 的 Azure AD 用户将能够在 Gigya 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Gigya 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![配置单一登录](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-gigya"></a>为 Gigya 启用应用程序集成
本部分的目的是概述如何为 Gigya 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>若要为 Gigya 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-gigya-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-gigya-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-gigya-tutorial/IC749322.png "Add an application from gallerry")

6. 在“搜索”框中，键入“Gigya”。
   
    ![应用程序库](./media/active-directory-saas-gigya-tutorial/IC789513.png "Application Gallery")

7. 在结果窗格中，选择“Gigya”，然后单击“完成”以添加该应用程序。
   
    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Gigya 进行身份验证。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在“Gigya”应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configure Single Sign-On")

2. 在“你希望用户如何登录到 Gigya”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configure Single Sign-On")

3. 在“配置应用 URL”页上，在“Gigya 登录 URL”文本框中，使用“*http://company.gigya.com*”模式键入 URL，然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configure App URL")

4. 在“配置 Gigya 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
    ![配置单一登录](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configure Single Sign-On")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Gigya 公司站点。

6. 转到“设置”\>“SAML 登录”，然后单击“添加”按钮。
   
    ![SAML 登录](./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML Login")

7. 在“SAML 登录”部分执行以下步骤：
   
    ![SAML 配置](./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML Configuration")
   
    a. 在“名称”文本框中，键入配置名称。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 Azure 经典门户的“配置 Gigya 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中。
   
    c. 在 Azure 经典门户的“配置 Gigya 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“单一登录服务 URL”文本框中。
   
    d.单击“下一步”。 在 Azure 经典门户的“配置 Gigya 的单一登录”对话框页面上，复制“名称标识符格式”值，然后将其粘贴到“名称 ID 格式”文本框中。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 根据下载的证书创建 **base-64 编码**文件。
      
    > [!TIP]
    > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    f. 在记事本中打开 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“X.509 证书”文本框中。
   
    g. 单击“保存设置”。

8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录 Gigya，必须对其进行预配才能使其登录 Gigya。  
对于 Gigya，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 **Gigya** 公司站点。
2. 转到“管理员”\>“管理用户”，然后单击“邀请用户”。
   
    ![管理用户](./media/active-directory-saas-gigya-tutorial/IC789535.png "Manage Users")

3. 在“邀请用户”对话框中，执行以下步骤：
   
    ![邀请用户](./media/active-directory-saas-gigya-tutorial/IC789536.png "Invite Users")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“电子邮件”文本框中，键入要预配的有效 Azure Active Directory 帐户的电子邮件别名。
    
    b.保留“数据库类型”设置，即设置为“共享”。 单击“邀请用户”。
      
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，该邮件包含在激活帐户前要确认帐户的链接。
    > 
    > 

 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-gigya-perform-the-following-steps"></a>若要将用户分配到 Gigya，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Gigya”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-gigya-tutorial/IC789537.png "Assign Users")

3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
    ![是](./media/active-directory-saas-gigya-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO4-->



---
title: "教程：Azure Active Directory 与 Zoho Mail 集成 | Microsoft Docs"
description: "了解如何配合使用 Zoho Mail 和 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2016
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f454e7e218764e00cc19ca67b0edade213834b75
ms.openlocfilehash: 394777b77cbf2fe9fa779c270eec9dded21bdfae


---
# <a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>教程：Azure Active Directory 与 Zoho Mail 集成
本教程旨在说明 Azure 与 Zoho Mail 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 有效的 Azure 订阅
* Zoho Mail 租户

完成本教程后，分配到 Zoho Mail 的 Azure AD 用户将能够在 Zoho Mail 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 实现 Zoho Mail 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scenario")

## <a name="enabling-the-application-integration-for-zoho-mail"></a>实现 Zoho Mail 的应用程序集成
本部分旨在概述如何实现 Zoho Mail 的应用程序集成。

### <a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>若要为 Zoho Mail 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Add an application from gallerry")

6. 在“搜索框”中，键入“Zoho Mail”。
   
    ![应用程序库](./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Application Gallery")

7. 在结果窗格中，选择“Zoho Mail”，然后单击“完成”以添加该应用程序。
   
    ![Zoho Mail](./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Zoho Mail")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户通过其在 Azure AD 中的帐户使用基于 SAML 协议的联合身份验证向 Zoho Mail 进行身份验证。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户的“Zoho Mail”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configure Single Sign-On")

2. 在“你希望用户如何登录 Zoho Mail”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configure Single Sign-On")

3. 在“配置应用 URL”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configure App URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Zoho Mail 登录 URL”文本框中，使用以下模式键入 URL：`http://<company name>.ZohoMail.com`
   
    b. 单击“下一步”。

4. 在“配置 Zoho Mail 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。
   
    ![配置单一登录](./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configure Single Sign-On")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zoho Mail 公司站点。

6. 转到“控制面板”。
   
    ![控制面板](./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Control Panel")

7. 单击“SAML 身份验证”选项卡。
   
    ![SAML 身份验证](./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "SAML Authentication")

8. 在“SAML 身份验证详细信息”部分，执行以下步骤：
   
    ![SAML 身份验证详细信息](./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "SAML Authentication Details")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 Azure 经典门户的“配置 Zoho Mail 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
   
    b. 在 Azure 经典门户的“配置 Zoho Mail 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
   
    c. 在 Azure 经典门户的“配置 Zoho Mail 的单一登录”对话框页面上，复制“更改密码 URL”值，然后将其粘贴到“更改密码 URL”文本框。
   
    d.单击“下一步”。 根据下载的证书创建 **base-64 编码**文件。  
      
    > [!TIP]
    > 有关详细信息，请参阅[如何将二进制证书转化为文本文件](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 在记事本中打开 base-64 编码的证书，将内容复制到剪贴板，然后将其粘贴到“公钥”文本框中。
   
    f. 选择“RSA”作为**算法**。
   
    g. 单击 **“确定”**。

9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 Zoho Mail，必须将这些用户预配到 Zoho Mail 中。  
需要在 Zoho Mail 中手动进行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 **Zoho Mail** 公司站点。

2. 转到“控制面板”\>“邮件和文档”。

3. 转到“用户详细信息\>添加用户”。
   
    ![添加用户](./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Add User")

4. 在“添加用户”对话框中，执行以下步骤：
   
    ![添加用户](./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Add User")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在相关文本框中，键入要预配的有效 Azure Active Directory 帐户的“名字”、“姓氏”、“电子邮件 ID”和“密码”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击 **“确定”**。  
      
    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，内附一个激活帐户前进行确认的链接。
    > 
    > 

> [!NOTE]
> 可使用 Zoho Mail 提供的任意其他 Zoho Mail 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>若要将用户分配给 Zoho Mail，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“Zoho Mail”应用程序集成页面上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Assign Users")

3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
    ![是](./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO1-->



---
title: "教程：Azure Active Directory 与 Dropbox for Business 的集成 | Microsoft Docs"
description: "了解如何使用 Dropbox for Business 与 Azure Active Directory 启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/17/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7a0a300f505d9012471679ac27373944f07fdba3
ms.openlocfilehash: e328f8ecfad5f481462a7b9c0c4a16e966e33217


---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>教程：Azure Active Directory 与 Dropbox for Business 的集成
本教程旨在说明 Azure 与 Dropbox for Business 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Dropbox for Business 中的测试租户

完成本教程后，已分配到 Dropbox for Business 的 Azure AD 用户能够在 Dropbox for Business 公司站点（服务提供商启动的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Dropbox for Business 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario")

## <a name="enabling-the-application-integration-for-dropbox-for-business"></a>为 Dropbox for Business 启用应用程序集成
本部分旨在概述如何为 Dropbox for Business 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>若要为 Dropbox for Business 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入 **Dropbox for Business**。
   
    ![应用程序库](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Application gallery")
7. 在结果窗格中，选择“Dropbox for Business”，然后单击“完成”以添加该应用程序。
   
    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分旨在概述如何让用户使用基于 SAML 协议的联合身份验证通过其在 Azure AD 中的帐户向 Dropbox for Business 进行身份验证。

在此过程中，需要将 base-64 编码的证书上传到 Dropbox for Business 租户。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的 **Dropbox for Business** 应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configure single sign-on")
2. 在“你希望用户如何登录 Dropbox for Business”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configure single sign-on")
3. 在“配置应用 URL”页上，执行以下步骤：
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 登录到 Dropbox for business 租户。 
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configure single sign-on")
   
    b.保留“数据库类型”设置，即设置为“共享”。 在左侧导航窗格中，单击“管理控制台”。 
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configure single sign-on")
   
    c. 在“管理控制台”上，单击左侧导航窗格中的“身份验证”。 
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configure single sign-on")
   
    d.单击“下一步”。 在“单一登录”部分，选择“启用单一登录”，然后单击“更多”，展开此部分。  
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configure single sign-on")
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 复制位于“用户可输入其电子邮件地址进行登录或直接转到”旁的 URL。 
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configure single sign-on")
   
    f. 在 Azure 经典门户中的“**DropBox for Business 登录** URL”文本框中，粘贴该 URL。 
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configure single sign-on")  
4. 在“配置 Dropbox for Business 的单一登录”页上，单击“下载证书”，然后将该证书文件保存到计算机上。  
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configure single sign-on")
5. 在 Dropbox for Business 租户上“身份验证”页的“单一登录”部分中，执行以下步骤： 
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configure single sign-on")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“必需”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 Azure 经典门户的“配置 Dropbox for Business 的单一登录”对话框页面上，复制“登录页面 URL”值，然后将其粘贴到“登录 URL”文本框中。

    c. 基于下载的证书创建一个 **Base-64 编码**的文件。 

    > [!TIP] 
    > 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

    d.单击“下一步”。 单击“选择证书”，然后浏览到“base-64 编码的证书文件”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“保存更改”，完成 DropBox for Business 租户上的配置。

1. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。 
   
    ![配置单一登录](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>配置用户设置
本部分旨在概述如何对 Dropbox for Business 启用 Active Directory 用户帐户的用户预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 在 Azure 经典门户中的“Dropbox for Business”应用程序集成页上，单击“配置用户预配”，打开“配置用户预配”对话框。
2. 在“对 Dropbox for Business 启用用户预配”页上，单击“启用用户预配”，打开“登录 Dropbox 以链接 Azure AD”对话框。  
   
    ![用户预配](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "User provisioning")
3. 在“登录 Dropbox 以链接 Azure AD”对话框中，登录到 Dropbox for Business 租户。 
   
    ![用户预配](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "User provisioning")
4. 单击“允许”，授予 Azure AD 访问 Dropbox 的权限。 
   
    ![用户预配](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "User provisioning")
5. 若要完成配置，请单击“完成”按钮。  
   
    ![用户预配](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "User provisioning")

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>若要将用户分配到 Dropbox for Business，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Dropbox for Business** 应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Yes")

现在应等待 10 分钟，然后验证该帐户是否已同步到 Dropbox for Business。

作为第一个验证步骤，可在 Azure 经典门户中的 **Dropbox for Business** 应用程序集成页上单击“仪表板”，检查预配状态。

![分配用户](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assign users")

会出现相关状态提示已成功完成用户预配周期。

![分配用户](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assign users")

如果要测试单一登录设置，请打开访问面板。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->



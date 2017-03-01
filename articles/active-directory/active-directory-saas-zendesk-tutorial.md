---
title: "教程：Azure Active Directory 与 Zendesk 集成 | Microsoft Docs"
description: "了解如何使用 Zendesk 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c4983024e5f5902620c0be7b422bc3d1eaf4a36f
ms.openlocfilehash: ac22518bb36cc9d4c7a581dab8aca22ffde986e5


---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>教程：Azure Active Directory 与 Zendesk 集成
本教程旨在说明 Azure 与 Zendesk 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Zendesk 租户

完成本教程后，分配到 Zendesk 的 Azure AD 用户将能够在 Zendesk 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Zendesk 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-zendesk-tutorial/IC773083.png "Scenario")

## <a name="enabling-the-application-integration-for-zendesk"></a>为 Zendesk 启用应用程序集成
本部分旨在概述如何为 Zendesk 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-zendesk-perform-the-following-steps"></a>若要为 Zendesk 启用应用程序集成，请执行以下步骤：
1. 在 Azure 管理门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-zendesk-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-zendesk-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-zendesk-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-zendesk-tutorial/IC749322.png "Add an application from gallerry")

6. 在“搜索框”中，键入“Zendesk”。
   
    ![应用程序库](./media/active-directory-saas-zendesk-tutorial/IC773084.png "Application Gallery")

7. 在结果窗格中，选择“Zendesk”，然后单击“完成”以添加该应用程序。
   
    ![Zendesk](./media/active-directory-saas-zendesk-tutorial/IC773085.png "Zendesk")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分旨在概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 Zendesk 进行身份验证。  
配置 Zendesk 的单一登录需要检索证书的指纹值。  
如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure AD 门户中的“Zendesk”应用程序集成页上，单击“配置单一登录”以打开“配置单一登录”对话框。
   
    ![单一登录](./media/active-directory-saas-zendesk-tutorial/IC773086.png "Single sign-on")

2. 在“你希望用户如何登录 Zendesk”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/IC773087.png "Configure single sign-on")

3. 在“配置应用 URL”页上，执行以下步骤：
   
    ![配置应用 URL](./media/active-directory-saas-zendesk-tutorial/IC773088.png "Configure app URL")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“Zendesk 登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.zendesk.com`
   
    b. 单击“下一步”。

4. 在“配置 Zendesk 的单一登录”页上，单击“下载证书”，然后将证书文件保存在本地计算机上。
   
    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/IC777534.png "Configure single sign-on")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zendesk 公司站点。

6. 单击“管理”。

7. 在左侧导航窗格中，单击“设置”，然后单击“安全”。
   
    ![“安全”](./media/active-directory-saas-zendesk-tutorial/IC773089.png "Security")

8. 在“安全”页上，单击“管理员和代理”选项卡。

9. 选择“单一登录(SSO)和 SAML”，然后选择“SAML”。

10. 在 Azure AD 门户的“配置 Zendesk 的单一登录”页上，复制“SAML SSO URL”值，然后将其粘贴到“SAML SSO URL”文本框中。

11. 在 Azure AD 门户的“配置 Zendesk 的单一登录”页上，复制“远程注销 URL”值，然后将其粘贴到“远程注销 URL”文本框中。
    
    ![单一登录](./media/active-directory-saas-zendesk-tutorial/IC773090.png "Single sign-on")

12. 从导出的证书中复制“指纹”值，然后将其粘贴到“证书指纹”文本框中。
    
    > [!TIP]
    > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
    > 
    > 

13. 单击“保存” 。

14. 在 Azure AD 门户中，选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/IC773093.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>配置用户设置
为使 Azure AD 用户能够登录到“Zendesk”，必须将其预配到“Zendesk”。  
就“Zendesk”而言，预配属手动任务。

### <a name="to-provision-a-user-account-to-zendesk-perform-the-following-steps"></a>若要将用户帐户预配到 Zendesk，请执行以下步骤：
1. 登录到“Zendesk”租户。

2. 选择“客户列表”选项卡。

3. 选择“用户”选项卡，然后单击“添加”。
   
    ![添加用户](./media/active-directory-saas-zendesk-tutorial/IC773632.png "Add user")
4. 键入要预配的现有 Azure AD 帐户的电子邮件地址，然后单击“保存”。
   
    ![新建用户](./media/active-directory-saas-zendesk-tutorial/IC773633.png "New user")

> [!NOTE]
> 可以使用 Zendesk 提供的任何其他 Zendesk 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-zendesk-perform-the-following-steps"></a>若要将用户分配给 Zendesk，请执行以下步骤：
1. 在 Azure AD 门户中，创建一个测试帐户。

2. 在“Zendesk”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-zendesk-tutorial/IC773094.png "Assign users")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-zendesk-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO1-->



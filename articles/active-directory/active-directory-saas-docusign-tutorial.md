---
title: "教程：Azure Active Directory 与 DocuSign 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 DocuSign 之间配置单一登录。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ac53d0a2c1c0a257e86fe74e10c8781aa6305975
ms.openlocfilehash: 662e853ca31d426a71121d22733256556ca56fc6
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>教程：Azure Active Directory 与 DocuSign 的集成
本教程的目标是展示 Azure 与 DocuSign 的集成。
在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* DocuSign 中的租户

在本教程中概述的方案由以下构建基块组成：

1. [为 DocuSign 启用应用程序集成](#enabling-the-application-integration-for-docusign) 
2. [配置单一登录](#configuring-single-sign-on) 
3. [配置帐户预配](#configuring-account-provisioning) 
4. [分配用户](#assigning-users) 
   
![配置单一登录][0]

## <a name="enabling-the-application-integration-for-docusign"></a>为 DocuSign 启用应用程序集成
本部分的目的是概述如何为 DocuSign 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>若要为 DocuSign 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![配置单一登录][1]
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![配置单一登录][2]
4. 在页面底部单击“添加”。
   
    ![应用程序][3]
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![配置单一登录][4]
6. 在搜索框中，键入“DocuSign”。
   
    ![配置单一登录][5]
7. 在结果窗格中，选择“DocuSign”，然后单击“完成”以添加该应用程序。
   
    ![配置单一登录][6]

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 DocuSign 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **DocuSign** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
    ![配置单一登录][7]
2. 在“你希望用户如何登录到 DocuSign”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录][8]
3. 在“配置应用设置”页上，执行以下步骤：
   
    ![配置单一登录][61]
   
    a. 在“登录 URL”文本框中，键入 `https://account.docusign.com/*`。  
   
    b. 在“标识符”文本框中，键入 `https://account.docusign.com/*`。  
   
    c. 单击“下一步”。 

    > [!TIP] 
    > “登录 URL”和“标识符”值只是占位符。 本主题下文中的内容包含了有关如何为你的环境检索实际值的说明。


1. 在“在 DocuSign 处配置单一登录”页上，单击“下载证书”，然后将证书文件保存在计算机本地。
   
    ![配置单一登录][10]
2. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 **DocuSign 管理门户**。
3. 在左侧的导航菜单中，单击“域”。
   
    ![配置单一登录][51]
4. 在右窗格上，单击“声明域”。
   
    ![配置单一登录][52]
5. 在“声明域”对话框中，在“域名”文本框中，键入你的公司域，然后单击“声明”。 确保对域进行验证并且其状态为活动。
   
    ![配置单一登录][53]
6. 在左侧的菜单中，单击“标识提供者”。  
   
    ![配置单一登录][54]
7. 在右窗格上，单击“添加标识提供者”。 
   
    ![配置单一登录][55]
8. 在“标识提供者设置”页上，执行以下步骤：
   
    ![配置单一登录][56]

    a. 在“名称”文本框中，为配置键入一个唯一的名称。 请不要使用空格。

    b. 在 Azure 经典门户中，复制颁发者 URL，然后将其粘贴到“标识提供者颁发者”文本框中。

    c. 在 Azure 经典门户中，复制“远程登录 URL”，然后将其粘贴到“标识提供者登录 URL”文本框中。

    d. 在 Azure 经典门户中，复制“远程注销 URL”，然后将其粘贴到“标识提供者注销 URL”文本框中。

    e. 选择“对身份验证请求签名”。

    f. 对于“身份验证请求发送方式”，选择“POST”。

    g. 对于“注销请求发送方式”，选择“POST”。 


1. 在“自定义属性映射”部分中，选择要通过 Azure AD 声明映射的字段。 在此示例中，**emailaddress** 声明是通过 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 的值映射的。 这是 Azure AD 中用于电子邮件声明的默认声明名称。 
   
    > [!NOTE]
    > 请使用合适的“用户标识符”将用户从 Azure AD 映射到 Docusign 用户映射。 根据你的组织设置选择正确的字段并输入合适的值。
    > 
    > 
   
    ![配置单一登录][57]
2. 在“标识提供者证书”部分中，单击“添加证书”，然后上载已从 Azure 经典门户下载的证书。   
   
    ![配置单一登录][58]
3. 单击“保存”。
4. 在“标识提供者”部分中，单击“操作”，然后单击“终结点”。   
   
    ![配置单一登录][59]
5. 在 Azure 经典门户中，返回到“配置应用设置”页。 
6. 在 **DocuSign 管理门户**中，在“查看 SAML 2.0 终结点”部分中，执行以下步骤：
   
    ![配置单一登录][60]
   
    a. 在 Azure 经典门户中，复制“服务提供者颁发者 URL”，然后将其粘贴到“标识符”文本框中。
   
    b. 在 Azure 经典门户中，复制“服务提供者登录 URL”，然后将其粘贴到“登录 URL”文本框中。
   
    c.  单击“关闭”。  
7. 在 Azure 经典门户中，单击“下一步”。 
8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![应用程序][14]
9. 在“单一登录确认”页上，单击“完成”。
   
    ![应用程序][15]

## <a name="configuring-account-provisioning"></a>配置帐户预配
本部分的目的是概述如何对 DocuSign 启用 Active Directory 用户帐户的用户预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户预配，请执行以下步骤：
1. 在 **Azure 经典门户**中，在 **DocuSign** 应用程序集成页上，单击“配置帐户设置”，以打开“配置用户设置”对话框。
   
    ![配置帐户预配][30]
2. 在“设置和管理员凭据”页上，若要启用自动用户预配，请提供具有足够权限的 DocuSign 帐户的凭据，然后单击“下一步”。 
   
    ![配置帐户预配][31]
3. 在“测试连接”对话框中，单击“开始测试”，在测试成功后单击“下一步”。
   
    ![配置帐户预配][32]
4. 在“确认”页上，单击“完成”。
   
    ![配置帐户预配][33]

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>若要将用户分配到 DocuSign，请执行以下步骤：
1. 在 **Azure 经典门户**中，创建一个测试帐户。
2. 在 **DocuSign** 应用程序集成页上，单击“分配用户”。
   
    ![分配用户][40]
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![分配用户][41]

现在应等待 10 分钟，然后验证该帐户是否已同步到 DocuSign。

作为第一个验证步骤，你可以通过在 Azure 经典门户中的 DocuSign 应用程序集成页上单击“仪表板”来检查预配状态。

![分配用户][42]

成功完成的用户预配周期将由一个相关状态予以指示：

![分配用户][43]

如果要测试单一登录设置，请打开访问面板。

有关访问面板的详细信息，请参阅“访问面板简介”。

## <a name="additional-resources"></a>其他资源
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png


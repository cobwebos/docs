---
title: "教程：Azure Active Directory 与 Zendesk 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Zendesk 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 51c06d838c5ed6286dfb99ea25faaaf33bad5f3c
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>教程：Azure Active Directory 与 Zendesk 集成

本教程介绍了如何将 Zendesk 与 Azure Active Directory (Azure AD) 集成。

将 Zendesk 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Zendesk
- 可以让用户使用其 Azure AD 帐户自动登录到 Zendesk（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zendesk 的集成，需备齐以下项：

- 一个 Azure AD 订阅
- 已启用 Zendesk 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Zendesk
2. 配置和测试 Azure AD 单一登录


## <a name="adding-zendesk-from-the-gallery"></a>从库中添加 Zendesk
若要配置 Zendesk 与 Azure AD 的集成，需要从库中将 Zendesk 添加到托管 SaaS 应用列表。

若要从库中添加 Zendesk，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Zendesk”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_search.png)

5. 在结果面板中，选择“Zendesk”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 Zendesk 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Zendesk 用户。 换句话说，需要建立 Azure AD 用户与 Zendesk 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 Zendesk 中“用户名”的值来建立此关联关系。

若要配置和测试 Zendesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Zendesk 测试用户](#creating-a-zendesk-test-user) - 在 Zendesk 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Zendesk 应用程序中配置单一登录。

若要配置 Zendesk 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Zendesk 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. 在“Zendesk 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入值：`https://<subdomain>.zendesk.com`

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在“标识符”文本框中，使用以下模式键入值：`https://<subdomain>.zendesk.com`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际的登录 URL 和标识符 URL 更新这些值。 请联系 [Zendesk 支持团队](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)获取这些值。 

4. Zendesk 需要特定格式的 SAML 断言。 没有强制的 SAML 属性，但可以按照以下步骤选择添加“用户属性”部分的属性： 

     ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“查看并编辑所有其他属性”复选框。
     
    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes2.png)
   
    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 单击“添加属性”，打开“添加属性”对话框。
    
    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    c. 在“名称”文本框中，键入属性名称（如 emailaddress）。
    
    d. 在“值”列表中，选择属性值（如 user.mail）。
    
    e. 单击“确定”
 
    > [!NOTE] 
    > 可以使用扩展属性添加默认情况下不在 Azure AD 中的属性。 单击 [可在 SAML 中设置的用户属性](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)，获取 Zendesk 接受的 SAML 属性的完整列表。 

5. 在“SAML 签名证书”部分中，复制证书的指纹值。

    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png) 

6. 在“Zendesk 配置”部分中，单击“配置 Zendesk”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zendesk 公司站点。

8. 单击“管理”。

9. 在左侧导航窗格中，单击“设置”，然后单击“安全”。

10. 在“安全”页中，执行以下步骤： 
   
     ![安全](./media/active-directory-saas-zendesk-tutorial/ic773089.png "安全")

    ![单一登录](./media/active-directory-saas-zendesk-tutorial/ic773090.png "单一登录")

     a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“管理员和代理”选项卡。

     b.在“磁贴徽标”下面，选择“删除上传的徽标”。 选择“单一登录(SSO)和 SAML”，然后选择“SAML”。

     c. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的 SAML 单一登录服务 URL 值。 

     d. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的注销 URL 值。
        
     e. 在“证书指纹”文本框中，粘贴从 Azure 门户复制的证书指纹值。
     
     f. 单击“保存” 。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png) 

3. 在对话框顶部，单击“添加”以打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 

### <a name="creating-a-zendesk-test-user"></a>创建 Zendesk 测试用户

为使 Azure AD 用户能够登录到 Zendesk，必须将其预配到 Zendesk 中。  
根据应用中分配的角色，它是预期的行为：

 1. 在登录时会自动预配“最终用户”帐户。
 2. 在登录前需要在 Zendesk 中手动预配“代理”和“管理员”帐户。
 
**若要预配用户帐户，请执行以下步骤：**

1. 登录到“Zendesk”租户。

2. 选择“客户列表”选项卡。

3. 选择“用户”选项卡，然后单击“添加”。
   
    ![添加用户](./media/active-directory-saas-zendesk-tutorial/ic773632.png "添加用户")
4. 键入要预配的现有 Azure AD 帐户的电子邮件地址，然后单击“保存”。
   
    ![新用户](./media/active-directory-saas-zendesk-tutorial/ic773633.png "新用户")

> [!NOTE]
> 可以使用 Zendesk 提供的任何其他 Zendesk 用户帐户创建工具或 API 来预配 AAD 用户帐户。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Zendesk 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Zendesk，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Zendesk”。

    ![配置单一登录](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Zendesk”磁贴时，会自动登录到 Zendesk 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png


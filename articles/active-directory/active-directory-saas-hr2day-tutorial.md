---
title: "教程：Azure Active Directory 与 HR2day by Merces 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 HR2day by Merces 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 853d08c9-27b1-48d4-b8e7-3705140eb67f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 456ef3620c1d7bb6ab2bf09b094f977e46cf2ed6
ms.contentlocale: zh-cn
ms.lasthandoff: 06/29/2017


---
# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>教程：Azure Active Directory 与 HR2day by Merces 集成

本教程介绍了如何将 HR2day by Merces 与 Azure Active Directory (Azure AD) 集成。

将 HR2day by Merces 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 HR2day by Merces
- 可以让用户使用其 Azure AD 帐户自动登录到 HR2day by Merces（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 HR2day by Merces 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 HR2day by Merces 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加由 HR2day by Merces
2. 配置和测试 Azure AD 单一登录

## <a name="adding-hr2day-by-merces-from-the-gallery"></a>从库中添加由 HR2day by Merces
若要配置 HR2day by Merces 与 Azure AD 的集成，需要从库中将 HR2day by Merces 添加到托管 SaaS 应用列表。

**若要从库中添加 HR2day by Merces，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“HR2day by Merces”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_search.png)

5. 在结果面板中，选择“HR2day by Merces”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 HR2day by Merces 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 HR2day by Merces 用户。 换句话说，需要在 Azure AD 用户与 HR2day by Merces 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 HR2day by Merces 中“用户名”的值来建立此关联关系。

若要使用 HR2day by Merces 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 HR2day by Merces 测试用户](#creating-an-hr2day-by-merces-test-user) - 在 HR2day by Merces 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 HR2day by Merces 应用程序中配置单一登录。

**若要使用 HR2day by Merces 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 HR2day by Merces 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_samlbase.png)

3. 在“HR2day by Merces 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenantname>.force.com/<instancename>`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://hr2day.force.com/<companyname>`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [HR2day by Merces 客户端支持团队](mailto:servicedesk@merces.nl)获取这些值。 
 


4. 在“SAML 签名证书”部分中，单击“证书(Base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_certificate.png) 

5. 本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 HR2day by Merces 进行身份验证。

    HR2day by Merces 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示一个示例。 

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png)
    
    > [!NOTE] 
    在配置 SAML 断言前，需要联系 [HR2day by Merces 客户端支持团队](mailto:servicedesk@merces.nl)，并为租户请求唯一标识符属性值。 需要此值才能完成下一部分中的步骤。  

6. 在“单一登录”对话框的“用户属性”部分中，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
      | 属性名称    |   属性值 |  
    | ------------------- | -------------------- |    
    | ATTR_LOGINCLAIM | join([mail]、“102938475Z”、“@” |
    
      a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_attribute_05.png)

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 在“名称”文本框中，键入“ATTR_LOGINCLAIM”。

    c. 从“值”列表中，选择“Join()”。

    d. 从“String1”列表中，选择“user.mail”。

    e. 在“String2”列表中，键入 HR2day 团队提供的唯一标识符。

    f. 在“分隔符”文本框中，键入 **@**。
    
    g. 单击“确定” 。

7. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_general_400.png)

8. 在“HR2day by Merces 配置”部分中，单击“通过 Merces 配置 HR2day”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_configure.png) 

9. 若要为应用程序配置 SSO，请联系 [HR2day by Merces 客户端支持团队](mailTo:servicedesk@merces.nl)，将下载的证书 (Base64) 文件附加到电子邮件。 另请提供注销 URL、SAML 实体 ID 和 SAML 单一登录服务 URL，将其配置为可以进行 SSO 集成。

    > [!NOTE]
    > 请向 Merces 团队提及此集成需要使用以下模式设置实体 ID：**https://hr2day.force.com/INSTANCENAME**
    > 
    > 

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-hr2day-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hr2day-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-hr2day-by-merces-test-user"></a>创建 HR2day by Merces 测试用户

本部分的目的是在 HR2day by Merces 中创建名为“Britta Simon”的用户。 请与 [HR2day by Merces 客户端支持团队](mailto:servicedesk@merces.nl)协作，将用户添加到 HR2day 帐户中。 

> [!NOTE]
> 如果需要手动创建用户，则需要联系 [HR2day by Merces 客户端支持团队](mailto:servicedesk@merces.nl)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 HR2day by Merces 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 HR2day by Merces，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“HR2day by Merces”。

    ![配置单一登录](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2daybymerces_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分的目的是使用访问面板测试 Azure AD 单一登录配置。  

单击访问面板中的 HR2day by Merces 磁贴时，你应自动登录到 HR2day by Merces 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png



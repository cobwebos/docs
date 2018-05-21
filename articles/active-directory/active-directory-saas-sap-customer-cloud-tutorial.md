---
title: 教程：Azure Active Directory 与 SAP Cloud for Customer 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 SAP Cloud for Customer 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 18172fb634c369fb4add5fe7c55e5fec08df7670
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>教程：将 Azure Active Directory 与 SAP Cloud for Customer 集成

在本教程中，了解如何将 SAP Cloud for Customer 与 Azure Active Directory (Azure AD) 集成。

将 SAP Cloud for Customer 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 SAP Cloud for Customer
- 可以让用户使用其 Azure AD 帐户自动登录到 SAP Cloud for Customer（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP Cloud 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 SAP Cloud for Customer 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 SAP Cloud for Customer
2. 配置和测试 Azure AD 单一登录

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>从库中添加 SAP Cloud for Customer
要配置 SAP Cloud for Customer 与 Azure AD 的集成，需要从库中将 SAP Cloud for Customer 添加到托管 SaaS 应用列表。

**若要从库中添加 SAP Cloud for Customer，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“SAP Cloud for Customer”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. 在结果面板中，选择“SAP Cloud for Customer”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SAP Cloud for Customer 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SAP Cloud for Customer 用户。 换句话说，需要在 Azure AD 用户与 SAP Cloud for Customer 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 SAP Cloud for Customer 中“用户名”的值来建立此关联关系。

若要配置和测试 SAP Cloud for Customer 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 SAP Cloud for Customer 测试用户](#creating-a-sap-cloud-for-customer-test-user) - 在 SAP Cloud for Customer 中有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 SAP Cloud for Customer 应用程序中配置单一登录。

**若要配置 SAP Cloud for Customer 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 SAP Cloud for Customer 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. 在“SAP Cloud for Customer 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server name>.crm.ondemand.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [SAP Cloud for Customer 客户端支持团队](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)获取这些值。 

4. 在“用户属性”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. 在“用户标识符”列表中选择“ExtractMailPrefix()”。

    b. 从“邮件”列表中，选择要用于实现的用户属性。
    例如，如果想要使用 EmployeeID 作为唯一用户标识符并且已在 ExtensionAttribute2 中存储属性值，则选择 user.extensionattribute2。  

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_400.png)

7. 在“SAP Cloud for Customer 配置”部分，单击“配置 SAP Cloud for Customer”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. 若要配置 SSO，请执行以下步骤：
   
    a. 以管理员权限登录到 SAP Cloud for Customer 门户。
   
    b. 导航到“应用程序和用户管理常见任务”，单击“标识提供者”选项卡。
   
    c. 单击“新建标识提供者”，并选择从 Azure 门户下载的元数据 XML 文件。 导入元数据时，系统会自动上传所需的签名证书和加密证书。
   
    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Azure Active Directory 要求在 SAML 请求中包含“断言使用者服务 URL”元素，因此请选中“包括断言使用者服务 URL”复选框。
   
    e. 单击“激活单一登录”。
   
    f. 保存所做更改。
   
    g. 单击“我的系统”选项卡。
   
    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. 在“Azure AD 登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”。
   
    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. 指定员工是否可以手动在以下两者中选择：使用用户 ID 和密码登录，或者通过选择“手动选择标识提供者”使用 SSO 登录。
   
    j. 在“SSO URL”部分中，指定员工应该用于登录到系统的 URL。 
    在“发送给员工的 URL”列表中，可以选择下列选项：
   
    **非 SSO URL**
   
    系统仅将普通的系统 URL 发送给员工。 员工不能使用 SSO 登录，必须改用密码或证书。
   
    **SSO URL** 
   
    系统仅将 SSO URL 发送给员工。 员工可以使用 SSO 登录。 通过 IdP 重定向身份验证请求。
   
    **自动选择**
   
    如果 SSO 未处于活动状态，系统将向员工发送普通的系统 URL。 如果 SSO 处于活动状态，系统会检查员工是否有密码。 如果密码可用，系统会向员工发送 SSO URL 和非 SSO URL。 但是，如果员工没有密码，则仅向员工发送 SSO URL。
   
    k. 保存所做更改。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>创建 SAP Cloud for Customer 测试用户

在本部分中，会在 SAP Cloud for Customer 中创建一个名为“Britta Simon”的用户。 请与 [SAP Cloud for Customer 支持团队](https://www.sap.com/about/agreements.sap-cloud-services-customers.html)协作，将用户添加到 SAP Cloud for Customer 平台。 

> [!NOTE]
> 请确保 NameID 值应与 SAP Cloud for Customer 平台中的用户名字段匹配。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP Cloud for Customer 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 SAP Cloud for Customer，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“SAP Cloud for Customer”。

    ![配置单一登录](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“SAP Cloud for Customer”磁贴时，用户应自动登录到 SAP Cloud for Customer 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png


---
title: "教程： 与 123ContactForm Azure Active Directory 集成 |Microsoft 文档"
description: "了解如何配置单一登录 Azure Active Directory 和 123ContactForm 之间。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 3a99f0841c3e0d973168991f5dbee40e54c1d054
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>与 123ContactForm 的教程： Azure Active Directory 集成

在本教程中，你将学习如何与 Azure Active Directory (Azure AD) 集成 123ContactForm。

与 Azure AD 集成 123ContactForm 提供了以下好处：

- 你可以控制有权访问 123ContactForm Azure AD 中
- 你可以使用户可以自动获取登录到 123ContactForm （上单一登录） 使用其 Azure AD 帐户
- 你可以管理你的帐户在一个中心位置-Azure 门户

如果你想要了解有关与 Azure AD 的 SaaS 应用程序集成的详细信息，请参阅[应用程序访问与单一登录与 Azure Active Directory 是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必备条件

若要使用 123ContactForm 配置 Azure AD 集成，你需要以下各项：

- Azure AD 订阅
- 123ContactForm 上单一登录启用的订阅

> [!NOTE]
> 若要测试步骤在本教程中，我们不建议使用生产环境。

若要测试本教程中的步骤，你应遵循这些建议：

- 除非必要，否则，不要使用你的生产环境。
- 如果你没有 Azure AD 试用环境，你可以获取一个月试用版[此处](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案说明
在本教程中，你测试 Azure AD 单一登录在测试环境。 在本教程中概述的方案由两个主要构建基块组成：

1. 从库添加 123ContactForm
2. 配置和测试 Azure AD 单一登录

## <a name="adding-123contactform-from-the-gallery"></a>从库添加 123ContactForm
若要配置 123ContactForm 到 Azure AD 的集成，你需要将 123ContactForm 从库添加到你的托管的 SaaS 应用的列表。

**若要从库添加 123ContactForm，执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**，在左的导航面板中，单击**Azure Active Directory**图标。 

    ![Active Directory][1]

2. 导航到**企业应用程序**。 然后转到**所有应用程序**。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击**新的应用程序**对话框顶部的按钮。

    ![应用程序][3]

4. 在搜索框中，键入**123ContactForm**。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_search.png)

5. 在结果面板中，选择**123ContactForm**，然后单击**添加**按钮以添加该应用程序。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，可以配置和测试 Azure AD 单一登录与 123ContactForm 基于一个名为"Britta 人 Simon。"的测试用户

对于单一登录工作，Azure AD 需要知道什么对应项中的用户 123ContactForm 是向用户在 Azure AD 中。 换而言之，Azure AD 用户和 123ContactForm 中的相关的用户之间的链接关系需要建立。

在 123ContactForm，将分配的值**用户名**作为的值的 Azure AD 中**用户名**建立的链接关系。

若要配置和测试 Azure AD 单一登录与 123ContactForm，你需要完成以下构建基块：

1. **[Azure AD 单一登录配置](#configuring-azure-ad-single-sign-on)** -若要使用户可以使用此功能。
2. **[创建一个 Azure AD 的测试用户](#creating-an-azure-ad-test-user)** -若要测试 Azure AD 单一登录与 Britta 人 Simon。
3. **[创建 123ContactForm 测试用户](#creating-a-123contactform-test-user)** -若要具有在链接到用户的 Azure AD 表示的 123ContactForm Britta 人 Simon 的副本。
4. **[Azure AD 测试用户分配](#assigning-the-azure-ad-test-user)** -若要启用 Britta 人 Simon 用于 Azure AD 单一登录。
5. **[单一登录测试](#testing-single-sign-on)** -若要验证是否配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，可以启用 Azure AD 单一登录在 Azure 门户和 123ContactForm 应用程序中配置单一登录。

**若要使用 123ContactForm 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在**123ContactForm**应用程序集成页上，单击**单一登录**。

    ![配置单一登录][4]

2. 上**单一登录**对话框中，选择**模式**作为**SAML 基于登录**若要启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. 上**123ContactForm 域和 Url**部分中，如果你想要配置中的应用程序**IDP 发起的模式下**，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/url1.png)

    a. 在**标识符**文本框中，键入 URL 使用以下模式：`https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b。 在**答复 URL**文本框中，键入 URL 使用以下模式：`https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. 如果你想要配置中的应用程序**SP 发起的模式下**，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/url2.png)

    a. 单击**显示高级 URL 设置**选项

    b。 在**登录 URL**文本框中，键入作为 URL:`https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > 这些值不是实际的。 你将需要更新这些值从实际的 Url 和更高版本在本教程中说明的标识符。
    
5. 上**SAML 签名证书**部分中，单击**元数据 XML**然后将保存您的计算机上的元数据文件。

    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. 单击**保存**按钮。

    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/tutorial_general_400.png)

7. 在上配置单一登录**123ContactForm**端，请转到[https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/)并执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/submit.png) 

    a. 在**电子邮件**文本框中，键入用户即的电子邮件 **BrittaSimon@Contoso.com**.

    b。 单击**上载**并浏览你从 Azure 门户下载的元数据 XML 文件。

    c. 单击**提交窗体**。

8. 上**Microsoft Azure AD 的单一登录-配置应用设置**执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/url3.png)

    a. 如果你想要配置中的应用程序**IDP 发起的模式下**，复制**标识符**你的实例，并将其粘贴中**标识符**中的文本框中**123ContactForm 域和 Url** Azure 门户上的部分。
    
    b。 如果你想要配置中的应用程序**IDP 发起的模式下**，复制**答复 URL**你的实例，并将其粘贴中**答复 URL**中的文本框中**123ContactForm 域和 Url** Azure 门户上的部分。

    c. 如果你想要配置中的应用程序**SP 发起的模式下**，复制**登录 URL**你的实例，并将其粘贴中**登录 URL**中的文本框中**123ContactForm 域和 Url** Azure 门户上的部分。

> [!TIP]
> 你现在可以读取内部这些指令的简洁版本[Azure 门户](https://portal.azure.com)，而你要设置应用程序 ！  在添加此应用程序从后**Active Directory > 企业应用程序**部分中，只需单击**上单一登录**选项卡上和访问通过的嵌入的文档**配置**底部部分中的。 你可以阅读更多有关嵌入的文档的功能： [Azure AD 嵌入文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建一个 Azure AD 的测试用户
此部分的目的是在调用 Britta 人 Simon Azure 门户中创建一个测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建一个测试用户，请执行以下步骤：**

1. 在**Azure 门户**，左侧的导航窗格中，单击**Azure Active Directory**图标。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-123contactform-tutorial/create_aaduser_01.png) 

2. 若要显示的用户的列表，请转到**用户和组**单击**所有用户**。
    
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-123contactform-tutorial/create_aaduser_02.png) 

3. 若要打开**用户**对话框中，单击**添加**对话框顶部。
 
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-123contactform-tutorial/create_aaduser_03.png) 

4. 上**用户**对话框页上，执行以下步骤：
 
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-123contactform-tutorial/create_aaduser_04.png) 

    a. 在**名称**文本框中，键入**BrittaSimon**。

    b。 在**用户名**文本框中，键入**电子邮件地址**BrittaSimon。

    c. 选择**显示密码**并记下的值**密码**。

    d. 单击“创建”。
 
### <a name="creating-a-123contactform-test-user"></a>创建 123ContactForm 测试用户

实时用户设置和应用程序中将自动创建用户进行身份验证后，应用程序支持就。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在此部分中，你将启用 Britta 人 Simon 用于通过授予访问权限 123ContactForm Azure 单一登录。

![将用户分配][200] 

**若要将 Britta 人 Simon 分配到 123ContactForm，执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图，然后导航到的目录视图，并转到**企业应用程序**然后单击**所有应用程序**。

    ![将用户分配][201] 

2. 在应用程序列表中，选择**123ContactForm**。

    ![配置单一登录](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_app.png) 

3. 在左侧菜单中，单击**用户和组**。

    ![将用户分配][202] 

4. 单击**添加**按钮。 然后选择**用户和组**上**添加分配**对话框。

    ![将用户分配][203]

5. 上**用户和组**对话框中，选择**Britta 人 Simon**用户列表中。

6. 单击**选择**按钮上**用户和组**对话框。

7. 单击**分配**按钮上**添加分配**对话框。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在此部分中，你将测试 Azure AD 单一登录配置使用访问面板。

当你单击访问面板中的 123ContactForm 磁贴时，你应该获取自动登录到你 123ContactForm 的应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何与 Azure Active Directory 集成 SaaS 应用程序教程列表](active-directory-saas-tutorial-list.md)
* [应用程序访问与单一登录与 Azure Active Directory 是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_203.png


---
title: "教程：Azure Active Directory 与 EBSCO 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 EBSCO 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 150609a7bf326c243b1a0b5f10bfcfe9a426c2de
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>教程：Azure Active Directory 与 EBSCO 的集成

本教程介绍如何将 EBSCO 与 Azure Active Directory (Azure AD) 集成。

将 EBSCO 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 EBSCO
- 可使用户通过其 Azure AD 帐户自动登录 EBSCO（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 EBSCO 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 EBSCO 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 EBSCO
2. 配置和测试 Azure AD 单一登录

## <a name="adding-ebsco-from-the-gallery"></a>从库中添加 EBSCO
若要配置 EBSCO 与 Azure AD 的集成，需要从库中将 EBSCO 添加到托管 SaaS 应用列表。

**若要从库中添加 EBSCO，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入 **EBSCO**，在结果面板中选择“EBSCO”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 EBSCO](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 EBSCO 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 EBSCO 用户。 换句话说，需要建立 Azure AD 用户与 EBSCO 中相关用户之间的链接关系。

若要配置和测试 EBSCO 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 EBSCO 测试用户](#create-an-ebsco-test-user)** - 可以自动执行 EBSCOhost 用户预配/个性化。 EBSCO 支持实时用户预配。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 EBSCO 应用程序中配置单一登录。

**若要配置 EBSCO 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“EBSCO”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. 在“EBSCO 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![EBSCO 域和 URL 单一登录信息](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url.png)

    在“标识符”文本框中，键入一个 URL：`pingsso.ebscohost.com`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![EBSCO 域和 URL 单一登录信息](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [EBSCO 客户端支持团队](mailto:sso@ebsco.com)获取此值。 

    o   **唯一元素：**  

       **Custid** = 输入唯一的 EBSCO 客户 ID 

       **Profile** = 客户端可以定制链接，以将用户定向到特定的配置文件（取决于从 EBSCO 购买的产品）。 可以输入特定的配置文件 ID。 主 ID 是 eds (EBSCO Discovery Service) 和 ehost（EBSOCOhost 数据库）。 [此处](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)提供了相同操作的说明。

5. EBSCO 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > **name** 属性是必需的，将映射到 EBSCO 应用程序中的“用户标识符”。 默认已添加此属性，因此无需手动添加。
    
6. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ---------------| --------------- |    
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | Email   | user.mail |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-ebsco-tutorial/tutorial_officespace_04.png)

    ![配置单一登录](./media/active-directory-saas-ebsco-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d.单击“下一步”。 单击“确定”

7. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-ebsco-tutorial/tutorial_general_400.png)
    
9. 若要在“EBSCO”端配置单一登录，需要将下载的“元数据 XML”发送给 [EBSCO 支持团队](mailto:sso@ebsco.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-ebsco-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-ebsco-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-ebsco-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-ebsco-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="create-an-ebsco-test-user"></a>创建 EBSCO 测试用户

在 EBSCO 中，用户预配可自动完成。

**若要预配用户帐户，请执行以下步骤：**

Azure AD 将所需的数据传递给 EBSCO 应用程序。 EBSCO 的用户预配可以自动完成，或要求填写表单一次。 具体的过程取决于客户端是否包含大量保存了个人设置的现有 EBSCOhost 帐户。 在实施过程中，可与 [EBSCO 支持团队](mailto:sso@ebsco.com)讨论上述过程。 不管采用哪种方法，客户端都不需要在测试之前创建任何 EBSCOhost 帐户。

   >[!Note]
   >可以自动执行 EBSCOhost 用户预配/个性化。 有关实时用户预配过程，请联系 [EBSCO 支持团队](mailto:sso@ebsco.com)。 
 
### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 EBSCO 的访问权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 EBSCO，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“EBSCO”。

    ![“应用程序”列表中的“EBSCO”链接](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

1. 在访问面板中单击“EBSCO”磁贴时，应会自动登录到 EBSCO 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

2. 登录到该应用程序后，单击右上角的“登录”按钮。

    ![“应用程序”列表中的 EBSCO 登录按钮](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. 此时会出现一次性提示“现在请将现有的 MyEBSCOhost 帐户链接到机构帐户”或“创建新的 MyEBSCOhost 帐户并将其链接到机构帐户”，以便配对机构/SAML 登录名。 该帐户用于在 EBSCOhost 应用程序中进行个性化。 选择选项“创建新帐户”，将会看到，用于个性化的窗体中预先填充了 SAML 响应中的值，如以下屏幕截图所示。 单击“继续”保存所做的选择。
    
     ![“应用程序”列表中的 EBSCO 用户](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_user.png)

4. 完成上述设置后，清除 Cookie/缓存并再次登录。 这一次不再需要手动登录，因为系统已记住个性化设置

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_203.png


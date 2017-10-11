---
title: "教程： Azure Active Directory 与集成不断的 SAML SSO 解析 GmbH |Microsoft 文档"
description: "了解如何配置单一登录 Azure Active Directory 和不断的 SAML SSO 之间解析 GmbH。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 9a36d686ba39b5168860a20e8c4db357888df6a7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>与不断的 SAML SSO 解析 GmbH 教程： Azure Active Directory 集成

在本教程中，你将了解如何将不断的 SAML SSO 集成解析 GmbH 与 Azure Active Directory (Azure AD)。

通过解析 GmbH 与 Azure AD 集成不断的 SAML SSO 提供了以下好处：

- 你可以控制有权访问 SAML SSO 的不断解析 GmbH Azure AD 中
- 你可以使用户可以自动获取登录到不断的 SAML SSO 解析 GmbH （上单一登录），使用其 Azure AD 帐户
- 你可以管理你的帐户在一个中心位置-Azure 门户

如果你想要了解有关与 Azure AD 的 SaaS 应用程序集成的详细信息，请参阅[应用程序访问与单一登录与 Azure Active Directory 是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必备条件

若要配置解析 GmbH 不断的 SAML SSO 与 Azure AD 集成，你需要以下各项：

- Azure AD 订阅
- 解析 GmbH 单一登录启用的订阅上不断 SAML SSO

> [!NOTE]
> 若要测试步骤在本教程中，我们不建议使用生产环境。

若要测试本教程中的步骤，你应遵循这些建议：

- 除非必要，否则，不要使用你的生产环境。
- 如果你没有 Azure AD 试用环境，你可以获取一个月试用版[此处](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案说明
在本教程中，你测试 Azure AD 单一登录在测试环境。 在本教程中概述的方案由两个主要构建基块组成：

1. 从库解析 GmbH 添加不断的 SAML SSO
2. 配置和测试 Azure AD 单一登录

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>从库解析 GmbH 添加不断的 SAML SSO

若要配置不断的 SAML SSO 解析 GmbH 到 Azure AD 的集成，你需要不断的 SAML SSO 解析 GmbH 从库添加到托管的 SaaS 应用程序列表。

**若要从库中添加解析 GmbH 不断的 SAML SSO，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**，在左的导航面板中，单击**Azure Active Directory**图标。 

    ![Active Directory][1]

2. 导航到**企业应用程序**。 然后转到**所有应用程序**。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击**新的应用程序**对话框顶部的按钮。

    ![应用程序][3]

4. 在搜索框中，键入**解析 GmbH 不断的 SAML SSO**。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_search.png)

5. 在结果面板中，选择**解析 GmbH 不断的 SAML SSO**，然后单击**添加**按钮以添加该应用程序。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，可以配置和测试 Azure AD 单一登录与不断的 SAML SSO 解析 GmbH 基于一个名为"Britta 人 Simon。"的测试用户

对于单一登录工作，Azure AD 需要知道哪些对应项中的用户不断的 SAML SSO GmbH 是用户在 Azure AD 中的解析。 换而言之，Azure AD 用户和不断的 SAML sso 解析相关的用户之间的链接关系 GmbH 需要建立。

在解析 GmbH 不断的 SAML SSO，将分配的值**用户名**作为的值的 Azure AD 中**用户名**建立的链接关系。

若要配置和测试 Azure AD 单一登录与不断的 SAML SSO 解析 GmbH，你需要完成以下构建基块：

1. **[Azure AD 单一登录配置](#configuring-azure-ad-single-sign-on)** -若要使用户可以使用此功能。
2. **[创建一个 Azure AD 的测试用户](#creating-an-azure-ad-test-user)** -若要测试 Azure AD 单一登录与 Britta 人 Simon。
3. **[创建由解析 GmbH 测试用户的不断 SAML SSO](#creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user)**  -若要解析链接到用户的 Azure AD 表示的 GmbH 不断的 SAML sso 具有 Britta 人 Simon 的副本。
4. **[Azure AD 测试用户分配](#assigning-the-azure-ad-test-user)** -若要启用 Britta 人 Simon 用于 Azure AD 单一登录。
5. **[单一登录测试](#testing-single-sign-on)** -若要验证是否配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，可以启用 Azure AD 单一登录在 Azure 门户和解析 GmbH 应用程序在不断你 SAML SSO 中配置单一登录。

**若要解析 GmbH Azure AD 单一登录配置了不断的 SAML SSO，请执行以下步骤：**

1. 在 Azure 门户中，在**解析 GmbH 不断的 SAML SSO**应用程序集成页上，单击**单一登录**。

    ![配置单一登录][4]

2. 上**单一登录**对话框中，选择**模式**作为**SAML 基于登录**若要启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_samlbase.png)

3. 上**不断解析 GmbH 域和 Url 的 SAML SSO**部分中，如果你想要配置中的应用程序**IDP**启动模式：

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_1.png)

    a. 在**标识符**文本框中，键入 URL 使用以下模式：`https://<server-base-url>/plugins/servlet/samlsso`

    b。 在**答复 URL**文本框中，键入 URL 使用以下模式：`https://<server-base-url>/plugins/servlet/samlsso`

4. 检查**显示高级 URL 设置**。 如果你想要配置中的应用程序**SP**启动模式：

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_2.png)

    在**登录 URL**文本框中，键入 URL 使用以下模式：`https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > 这些值不是实际的。 使用实际的标识符、 答复 URL 和登录 URL 更新这些值。 联系人[不断解析 GmbH 客户端的 SAML SSO 支持团队](https://www.resolution.de/go/support)以获取这些值。 

5. 上**SAML 签名证书**部分中，单击**元数据 XML**然后将保存您的计算机上的元数据文件。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_certificate.png) 

6. 单击**保存**按钮。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_400.png)  
    
7. 在另一个 web 浏览器窗口，登录到你**解析 GmbH 管理门户通过不断的 SAML SSO**以管理员身份。

8. 将鼠标悬停在小齿轮，然后单击**外接程序**。
    
    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon1.png)

9. 重定向到管理员访问权限页的值时。 输入密码，然后单击**确认**按钮。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon2.png)

10. 下**ATLASSIAN MARKETPLACE**选项卡上，单击**查找新外接程序**。 

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon.png)

11. 搜索**SAML 单一登录 (SSO) 的不断**单击**安装**按钮以安装新的 SAML 插件。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon7.png)

12. 插件安装将启动。 单击“关闭”。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon8.png)

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon9.png)

13. 单击“管理” 。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon10.png)
    
14. 单击**配置**若要配置新的插件。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon11.png)

15. 此外可以下找到此新插件**用户和安全**选项卡。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon3.png)
    
16. 上**SAML 单一登录插件配置**页上，单击**添加其他标识提供程序**按钮以配置标识提供程序的设置。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon4.png)

17. 执行以下步骤，此页上：

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon5.png)
 
    a. 添加**名称**的标识提供程序 （例如 Azure AD）。
    
    b。 添加**说明**的标识提供程序 （例如 Azure AD）。

    c. 单击**XML**和选择**元数据**你从 Azure 门户下载的文件。

    d. 单击**负载**按钮。

    e. 它读取 IdP 元数据，并将填充的屏幕截图中突出显示的字段。 
18. 单击**保存设置**按钮以保存设置。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/addon6.png)

> [!TIP]
> 你现在可以读取内部这些指令的简洁版本[Azure 门户](https://portal.azure.com)，而你要设置应用程序 ！  在添加此应用程序从后**Active Directory > 企业应用程序**部分中，只需单击**上单一登录**选项卡上和访问通过的嵌入的文档**配置**底部部分中的。 你可以阅读更多有关嵌入的文档的功能： [Azure AD 嵌入文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建一个 Azure AD 的测试用户
此部分的目的是在调用 Britta 人 Simon Azure 门户中创建一个测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建一个测试用户，请执行以下步骤：**

1. 在**Azure 门户**，左侧的导航窗格中，单击**Azure Active Directory**图标。

    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_01.png) 

2. 若要显示的用户的列表，请转到**用户和组**单击**所有用户**。
    
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_02.png) 

3. 若要打开**用户**对话框中，单击**添加**对话框顶部。
 
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_03.png) 

4. 上**用户**对话框页上，执行以下步骤：
 
    ![创建一个 Azure AD 的测试用户](./media/active-directory-saas-samlssoconfluence-tutorial/create_aaduser_04.png) 

    a. 在**名称**文本框中，键入**BrittaSimon**。

    b。 在**用户名**文本框中，键入**电子邮件地址**BrittaSimon。

    c. 选择**显示密码**并记下的值**密码**。

    d. 单击“创建”。
 
### <a name="creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>创建由解析 GmbH 测试用户的不断 SAML SSO

若要启用 Azure AD 用户能够登录不断的 SAML SSO 解析 GmbH，它们必须进行设置不断的 SAML SSO 解析 GmbH。  
在解析 GmbH 不断的 SAML SSO，设置是一项手动任务。

**若要设置用户帐户，请执行以下步骤：**

1. 登录到你不断由解析 GmbH 公司站点以管理员身份进行 SAML SSO。

2. 将鼠标悬停在小齿轮，然后单击**用户管理**。

    ![添加员工](./media/active-directory-saas-samlssoconfluence-tutorial/user1.png) 

3. 在用户部分中，单击**将用户添加**选项卡。 上**"添加用户"**对话框页上，执行以下步骤：

    ![添加员工](./media/active-directory-saas-samlssoconfluence-tutorial/user2.png) 

    a. 在**用户名**文本框中，键入 Britta 人 Simon 类似的用户的电子邮件。

    b。 在**全名**文本框中，键入 Britta 人 Simon 类似的用户的完整名称。

    c. 在**电子邮件**文本框中，键入的电子邮件地址的用户喜欢Brittasimon@contoso.com。

    d. 在**密码**文本框中，键入 Britta 人 Simon 密码。

    e. 单击**确认密码**重新输入密码。
    
    f. 单击**添加**按钮。    

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在此部分中，你将启用 Britta 人 Simon 用于解析 GmbH 授予不断的 SAML SSO 访问权限通过 Azure 单一登录。

![将用户分配][200] 

**若要解析 GmbH Britta 人 Simon 赋予不断的 SAML SSO，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图，然后导航到的目录视图，并转到**企业应用程序**然后单击**所有应用程序**。

    ![将用户分配][201] 

2. 在应用程序列表中，选择**解析 GmbH 不断的 SAML SSO**。

    ![配置单一登录](./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_samlssoconfluence_app.png) 

3. 在左侧菜单中，单击**用户和组**。

    ![将用户分配][202] 

4. 单击**添加**按钮。 然后选择**用户和组**上**添加分配**对话框。

    ![将用户分配][203]

5. 上**用户和组**对话框中，选择**Britta 人 Simon**用户列表中。

6. 单击**选择**按钮上**用户和组**对话框。

7. 单击**分配**按钮上**添加分配**对话框。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在此部分中，你将测试 Azure AD 单一登录配置使用访问面板。

通过解析 GmbH 磁贴访问面板中单击不断 SAML SSO 时，你应该获取自动登录到不断你 SAML SSO 解析 GmbH 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何与 Azure Active Directory 集成 SaaS 应用程序教程列表](active-directory-saas-tutorial-list.md)
* [应用程序访问与单一登录与 Azure Active Directory 是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssoconfluence-tutorial/tutorial_general_203.png


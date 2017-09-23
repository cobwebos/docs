---
title: "教程：Azure Active Directory 与 Salesforce 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Salesforce 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 639e40ca7e406a1726033e9f5c5363c289087589
ms.contentlocale: zh-cn
ms.lasthandoff: 06/23/2017

---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>教程：Azure Active Directory 与 Salesforce 集成

在本教程中，了解如何将 Salesforce 与 Azure Active Directory (Azure AD) 集成。

将 Salesforce 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Salesforce
- 可让用户使用其 Azure AD 帐户自动登录到 Salesforce（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Salesforce 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Salesforce 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Salesforce
2. 配置和测试 Azure AD 单一登录

## <a name="adding-salesforce-from-the-gallery"></a>从库中添加 Salesforce
若要配置 Salesforce 与 Azure AD 的集成，需要从库中将 Salesforce 添加到托管的 SaaS 应用列表。

若要从库中添加 Salesforce，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Salesforce”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. 在结果面板中，选择“Salesforce”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Salesforce 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Salesforce 用户。 换句话说，需要建立 Azure AD 用户与 Salesforce 中相关用户之间的关联。

通过将 Azure AD 中“用户名”的值分配为 Salesforce 中“用户名”的值来建立此关联。

若要配置和测试 Salesforce 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Salesforce 测试用户](#creating-a-salesforce-test-user) - 在 Salesforce 中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Salesforce 应用程序中配置单一登录。

若要配置 Salesforce 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Salesforce” 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. 在“Salesforce 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    在“登录 URL”文本框中，使用以下模式键入值： 
   * 企业帐户：`https://<subdomain>.my.salesforce.com`
   * 开发人员帐户：`https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际登录 URL 更新这些值。 请联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)获取这些值。 
 
4. 在“SAML 签名证书”部分中单击“证书”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. 在“Salesforce 配置”部分，单击“配置 Salesforce”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。 

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  在浏览器中打开新选项卡并登录到你的 Salesforce 管理员帐户。

8.  在“管理员”导航窗格下，单击“安全控件”以展开相关部分。 然后单击“单一登录设置”。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  在“单一登录设置”页上，单击“编辑”按钮。
    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > 如果无法为 Salesforce 帐户启用单一登录设置，则需要联系 [Salesforce 客户端支持团队](https://help.salesforce.com/support)。 

10. 选择“已启用 SAML”，然后单击“保存”。

      ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. 若要配置 SAML 单一登录设置，请单击“新建”。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. 在“SAML 单一登录设置编辑”页上进行以下配置：

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“名称”字段中，为此配置键入友好名称。 提供“名称”值时会自动填充“API 名称”文本框。

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 将“SMAL 实体 ID”值粘贴到 Salesforce 中的“证书颁发者”字段。

    c. 在“实体 ID”文本框中，使用以下模式输入 Salesforce 域名：
      
      * 企业帐户：`https://<subdomain>.my.salesforce.com`
      * 开发人员帐户：`https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. 单击“浏览”或“选择文件”打开“选择要上传的文件”对话框，选择你的 Salesforce 证书，然后单击“打开”以上传该证书。

    e. 对于“SAML 标识类型”，请选择“断言包含用户的 salesforce.com 用户名”。

    f. 对于“SAML 标识位置”，请选择“标识位于 Subject 语句的 NameIdentifier 元素中”

    g. 将“单一登录服务 URL”粘贴到 Salesforce 中“标识提供者登录 URL”字段。
    
    h. 对于“服务提供程序发起的请求绑定”，请选择“HTTP 重定向”。
    
    i. 最后，单击“保存”以应用 SAML 单一登录设置。

13. 在 Salesforce 的左侧导航窗格中，单击“域管理”展开相关部分，然后单击“我的域”。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. 向下滚动到“身份验证配置”部分，然后单击“编辑”按钮。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. 在“身份验证服务”部分，选择 SAML SSO 配置的友好名称，然后单击“保存”。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 如果选择了多个身份验证服务，则当用户尝试向 Salesforce 环境发起单一登录时，系统将提示他们选择登录时要使用的身份验证服务。 如果不希望发生这种情况，应将其他所有身份验证服务保持未选中状态。
<CE>    
> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在“Azure 门户”的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. 在对话框顶部，单击“添加”以打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-salesforce-test-user"></a>创建 Salesforce 测试用户

在本部分中，将在 Salesforce 中创建名为 Britta Simon 的用户。 Salesforce 支持在默认情况下保持启用的实时预配。
此部分不存在任何操作项。 尝试访问 Salesforce 时，如果 Salesforce 中没有用户，系统会创建一个新用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Salesforce 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Salesforce，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Salesforce”。

    ![配置单一登录](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

若要测试单一登录设置，请在 [https://myapps.microsoft.com](https://myapps.microsoft.com/) 处打开访问面板，然后登录到测试帐户并单击“Salesforce”。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置用户预配](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png



---
title: "教程：Azure Active Directory 与 Boomi 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Boomi 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: 77f79c4e57aa8dd90fb0a519e7217e4f4e3618e7
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>教程：Azure Active Directory 与 Boomi 集成

在本教程中，了解如何将 Boomi 与 Azure Active Directory (Azure AD) 集成。

将 Boomi 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Boomi
- 可以让用户使用其 Azure AD 帐户自动登录到 Boomi（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Boomi 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Boomi 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Boomi
2. 配置和测试 Azure AD 单一登录

## <a name="adding-boomi-from-the-gallery"></a>从库中添加 Boomi
要配置 Boomi 与 Azure AD 的集成，需要从库中将 Boomi 添加到托管 SaaS 应用列表。

**若要从库中添加 Boomi，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Boomi”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_search.png)

5. 在结果窗格中，选择“Boomi”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Boomi 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Boomi 用户。 换句话说，需要在 Azure AD 用户与 Boomi 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Boomi 中“用户名”的值来建立此链接关系。

若要配置和测试 Boomi 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Boomi 测试用户](#creating-a-boomi-test-user)** - 在 Boomi 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Boomi 应用程序中配置单一登录。

**若要配置 Boomi 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Boomi 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_samlbase.png)

3. 在“Boomi 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://platform.boomi.com/`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://platform.boomi.com/sso/<accountname>/saml`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [Boomi 支持团队](https://boomi.com/company/contact/)获取这些值。

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。
    
    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_certificate.png)

4. Boomi 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_attribute.png)

5. 在“单一登录”对话框上的“用户属性”部分，针对下表中所示的每一行，执行以下步骤：

    | 属性名称 | 属性值 |
    | -------------- | --------------- |
    | FEDERATION_ID | user.mail |
    
    a. 单击“添加属性”，打开“添加属性”对话框。
    
    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d.单击“下一步”。 单击“确定” 。

6. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_general_400.png)

7. 在“Boomi 配置”部分，单击“配置 Boomi”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_configure.png) 

8. 在另一个 Web 浏览器窗口中，以管理员身份登录 Boomi 公司站点。 

9. 导航到“公司名称”，并转到“设置”。

10. 单击“SSO 选项”选项卡，并执行以下步骤。

    ![在应用端配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_11.png)

    a. 选中“启用 SAML 单一登录”复选框。

    b. 单击“导入”，将已下载的证书从 Azure AD 上传到“标识提供者证书”。
    
    c. 在“标识提供者登录 URL”文本框中，输入 Azure AD 应用程序配置窗口中“SAML 单一登录服务 URL”的值。

    d.单击“下一步”。 对于“联合 ID 位置”，选择“联合 ID 位于 FEDERATION_ID 属性元素中”单选按钮。 

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“保存”按钮。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="creating-a-boomi-test-user"></a>创建 Boomi 测试用户

为使 Azure AD 用户能够登录到 Boomi，必须将其预配到 Boomi 中。 就 Boomi 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录 Boomi 公司站点。

2. 登录后，导航到“用户管理”并转到“用户”。

    ![用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "用户")

3. 单击 **+** 图标，此时会打开“添加/维护用户角色”对话框。

    ![用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "用户")

    ![用户](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "用户")

    a. 在“用户电子邮件地址”文本框中，键入用户的电子邮件地址（例如 BrittaSimon@contoso.com）。
    
    b. 在“名字”文本框中，键入用户的名字（如“Britta”）。

    c. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）。
    
    d.单击“下一步”。 输入用户的**联合 ID**。 每个用户都必须具有在帐户中唯一标识用户的联合 ID。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 向用户分配“标准用户”角色。 不要分配管理员角色，因为这会向用户提供标准 Atmosphere 访问权限以及单一登录访问权限。
    
    f. 单击“确定”。
    
    > [!NOTE]
    > 用户不会收到包含可用于登录 AtomSphere 帐户的密码的欢迎通知电子邮件，因为其密码是通过标识提供者进行管理的。 可以使用任何其他 Boomi 用户帐户创建工具或 Boomi 提供的 API 来预配 AAD 用户帐户。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Boomi 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Boomi，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Boomi”。

    ![配置单一登录](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Boomi”磁贴时，用户应自动登录到 Boomi 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png


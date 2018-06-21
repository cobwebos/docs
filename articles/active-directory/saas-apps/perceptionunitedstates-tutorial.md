---
title: 教程：Azure Active Directory 与 Perception United States (Non-UltiPro) 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Perception United States (Non-UltiPro) 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: e4f0fe0399b1230a27460df1718223736176ff01
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228214"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>教程：Azure Active Directory 与 Perception United States (Non-UltiPro) 的集成

本教程介绍如何将 Perception United States (Non-UltiPro) 与 Azure Active Directory (Azure AD) 集成。

将 Perception United States (Non-UltiPro) 与 Azure AD 集成可提供以下好处：

- 可以控制 Azure AD 中谁有权访问 Perception United States (Non-UltiPro)。
- 可以让用户通过其 Azure AD 帐户自动登录到 Perception United States (Non-UltiPro)（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Perception United States (Non-UltiPro) 的集成，需备齐以下项目：

- Azure AD 订阅
- 已启用 Perception United States (Non-UltiPro) 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Perception United States (Non-UltiPro)
2. 配置和测试 Azure AD 单一登录

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>从库中添加 Perception United States (Non-UltiPro)
若要配置 Perception United States (Non-UltiPro)与 Azure AD 的集成，需要从库中将 Perception United States (Non-UltiPro) 添加到托管 SaaS 应用列表。

**若要从库中添加 Perception United States (Non-UltiPro)，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **Perception United States (Non-UltiPro)**，在结果面板中选择“Perception United States (Non-UltiPro)”，并单击“添加”按钮添加该应用程序。

    ![结果列表中的 Perception United States (Non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，将基于一个名为“Britta Simon”的测试用户配置和测试 Perception United States (Non-UltiPro) 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Perception United States (Non-UltiPro) 用户。 换句话说，需要在 Azure AD 用户与 Perception United States (Non-UltiPro) 中相关用户之间建立链接关系。

在 Perception United States (Non-UltiPro) 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 Perception United States (Non-UltiPro) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Perception United States (Non-UltiPro) 测试用户](#create-a-perception-united-states-non-ultipro-test-user)** - 在 Perception United States (Non-UltiPro) 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Perception United States (Non-UltiPro) 应用程序中配置单一登录。

**若要配置 Perception United States (Non-UltiPro) 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Perception United States (Non-UltiPro)”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. 在“Perception United States (Non-UltiPro)域和 URL”部分中，执行以下步骤：

    ![Perception United States (Non-UltiPro) 域和 URL 单一登录信息](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. 在“标识符”文本框中，键入 URL：`https://perception.kanjoya.com/sp`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > 此值不是真实值。 用户将使用实际的回复 URL 更新该值（本教程稍后将会介绍）。
 
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

6. 在“Perception United States (Non-UltiPro)配置”部分中，单击“配置 Perception United States (Non-UltiPro)”打开“配置登录”窗口。 从“快速参考”部分复制“SAML 实体 ID”。

    a. **Perception United States (Non-UltiPro)** 应用程序要求对复制的“SAML 实体 ID”值进行 URL 编码。 若要获取 URI 编码值，请使用以下链接：**http://www.url-encode-decode.com/**。

    b. 获取 URI 编码值之后，请将它与下面所述的“回复 URL”合并：

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. 将上述值粘贴到“Perception United States (Non-UltiPro)域和 URL”部分的“回复 URL”文本框中。

    ![Perception United States (Non-UltiPro) 配置](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. 在另一浏览器窗口中，以管理员身份登录到 Perception United States (Non-UltiPro) 公司站点。

8. 在主工具栏中，单击“帐户设置”。

    ![Perception United States (Non-UltiPro) 用户](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. 在“帐户设置”页上，执行以下步骤：

    ![Perception United States (Non-UltiPro) 用户](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. 在“公司名称”文本框中，键入**公司**的名称。
    
    b. 在“帐户名”文本框中，键入**帐户**的名称。

    c. 在“默认收件人电子邮件”文本框中，键入有效的**电子邮件**。

    d. 选择“SAML 2.0”作为“SSO 标识提供者”。

10. 在“SSO 配置”页上，执行以下步骤：

    ![Perception United States (Non-UltiPro) SSO 配置](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. 选择“电子邮件”作为“SAML NameID 类型”。

    b. 在“SSO 配置名称”文本框中，键入**配置**的名称。
    
    c. 在“标识提供者名称”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。 

    d. 在“SAML 域”文本框中，输入类似于 **@contoso.com** 的域。

    e. 单击“再次上传”以上传“元数据 XML”文件。

    f. 单击“更新”。


> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>创建 Perception United States (Non-UltiPro) 测试用户

本部分将在 Perception United States (Non-UltiPro) 中创建名为 Britta Simon 的用户。 与 [Perception United States (Non-UltiPro) 支持团队](http://www.ultimatesoftware.com/Contact/ContactUs)协作，在 Perception United States (Non-UltiPro) 平台中添加用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，通过授予 Britta Simon 访问 Perception United States (Non-UltiPro) 的权限，使她能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Perception United States (Non-UltiPro)，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Perception United States (Non-UltiPro)”。

    ![应用程序列表中的 Perception United States (Non-UltiPro) 链接](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Perception United States (Non-UltiPro) 磁贴时，应会自动登录到 Perception United States (Non-UltiPro) 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png


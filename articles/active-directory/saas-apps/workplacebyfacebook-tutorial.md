---
title: 教程：Azure Active Directory 与 Workplace by Facebook 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workplace by Facebook 间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2018
ms.author: jeedes
ms.openlocfilehash: 1f83dd64c7f6773ddb8956e6ebbc37b8c55aacec
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423865"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>教程：Azure Active Directory 与 Workplace by Facebook 集成

本教程介绍如何将 Workplace by Facebook 与 Azure Active Directory (Azure AD) 集成。

将 Workplace by Facebook 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Workplace by Facebook
- 可以让用户使用其 Azure AD 帐户自动登录到 Workplace by Facebook（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workplace by Facebook 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Workplace by Facebook 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

> [!NOTE]
> Facebook 有两种产品：Workplace Standard（免费）和 Workplace Premium（付费）。 任何 Workplace Premium 租户都可以配置 SCIM 和 SSO 集成，对成本或所需的许可证没有其他影响。 SSO 和 SCIM 在 Workplace Standard 实例中不可用。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Workplace by Facebook
1. 配置和测试 Azure AD 单一登录

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>从库添加 Workplace by Facebook
要配置 Workplace by Facebook 与 Azure AD 的集成，需要从库中将 Facebook 的 Workplace 添加到托管 SaaS 应用列表。

**若要从库中添加 Workplace by Facebook，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

1. 在搜索框中，键入 **Workplace by Facebook**。

    ![创建 Azure AD 测试用户](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_search.png)

1. 在结果面板中，选择“Workplace by Facebook”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Workplace by Facebook 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Workplace by Facebook 用户。 换句话说，需要建立起 Azure AD 用户与 Workplace by Facebook 中的相关用户之间的关联。

通过将 Workplace by Facebook 中“用户名”的值指定为 Azure AD 中“用户名”的值来建立此关联。

若要配置和测试 Workplace by Facebook 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
1. [配置重新进行身份验证的频率](#configuring-reauthentication-frequency) - 以配置 Workplace 提示进行 SAML 检查。
1. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. [创建 Workplace by Facebook 测试用户](#creating-a-workplace-by-facebook-test-user) - 在 Workplace by Facebook 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
1. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Workplace by Facebook 应用程序中配置单一登录。

**若要配置 Workplace by Facebook 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的Workplace by Facebook 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_samlbase.png)

1. 在“Workplace by Facebook 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<instancename>.facebook.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请参阅工作区公司仪表板的身份验证页，了解你的工作区社区的正确值。 

1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录](./media/workplacebyfacebook-tutorial/tutorial_general_400.png)

1. 在“Workplace by Facebook 配置”部分中，单击“配置 Workplace by Facebook”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/workplacebyfacebook-tutorial/config.png) 

1. 在另一 Web 浏览器窗口中，以管理员身份登录 Workplace by Facebook 公司站点。
  
   > [!NOTE] 
   > 作为 SAML 身份验证过程的一部分，Workplace 可使用查询字符串（最大为 2.5 KB）将参数传递给 Azure AD。

1. 在“公司仪表板”中，转到“身份验证”选项卡。

1. 在“SAML 身份验证”下，从下拉列表中选择“仅 SSO”。

1. 将从 Azure 门户的“Workplace by Facebook 配置”部分复制的值输入到相应字段中：

    *   在“SAML URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
    *   在“SAML 颁发者 URL”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。
    *   在“SAML 注销重定向”（可选）中，粘贴从 Azure 门户复制的“注销 URL”值。
    *   在记事本中打开从 Azure 门户下载的 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“SAML 证书”文本框。

1. 可能需要输入“SAML 配置”部分下列出的受众 URL、收件人 URL 和 ACS（断言使用者服务）URL。

1. 滚动到该部分的底部，单击“测试 SSO”按钮。 此时会出现包含 Azure AD 登录页面的一个弹出窗口。 照常输入凭据进行身份验证。 

    **疑难解答：** 确保从 Azure AD 返回的电子邮件地址与用于登录的 Workplace 帐户相同。

1. 成功完成测试后，滚动到页面底部并单击“保存”按钮。

1. 现在所有使用 Workplace 的用户都会在 Azure AD 登录页面上进行身份验证。

1. **SAML 注销重定向（可选）** - 

    可以选择配置一个 SAML 注销 URL，该 URL 可用于指向 Azure AD 的注销页面。 启用并配置此设置后，用户将不会再被定向到 Workplace 注销页面。 用户会转而被重定向到在“SAML 注销重定向”设置中添加的 URL。

### <a name="configuring-reauthentication-frequency"></a>配置重新进行身份验证的频率

可将 Workplace 配置为每天、每三天、每周、每两周、每个月提示进行 SAML 检查或从不进行检查。

> [!NOTE] 
>移动应用程序上 SAML 检查的最小值为每周。

还可使用此按钮强制重置所有用户的 SAML：立即要求对所有用户进行 SAML 身份验证。


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/workplacebyfacebook-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/workplacebyfacebook-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/workplacebyfacebook-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/workplacebyfacebook-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-workplace-by-facebook-test-user"></a>创建 Workplace by Facebook 测试用户

本部分将在 Workplace by Facebook 中创建一个名为 Britta Simon 的用户。 Workplace by Facebook 支持默认启用的实时预配。

在此部分中无需进行任何操作。 尝试访问 Workplace by Facebook 时，如果 Workplace by Facebook 中尚不存在用户，则系统会创建一个新用户。

>[!Note]
>如需手动创建用户，请联系 [Workplace by Facebook 客户端支持团队](https://workplace.fb.com/faq/)

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Workplace by Facebook 的权限，使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Workplace by Facebook，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Workplace by Facebook”。

    ![配置单一登录](./media/workplacebyfacebook-tutorial/tutorial_workplacebyfacebook_app.png) 

1. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

如果要测试单一登录设置，请打开访问面板。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)
* [配置用户预配](workplacebyfacebook-provisioning-tutorial.md)



<!--Image references-->

[1]: ./media/workplacebyfacebook-tutorial/tutorial_general_01.png
[2]: ./media/workplacebyfacebook-tutorial/tutorial_general_02.png
[3]: ./media/workplacebyfacebook-tutorial/tutorial_general_03.png
[4]: ./media/workplacebyfacebook-tutorial/tutorial_general_04.png

[100]: ./media/workplacebyfacebook-tutorial/tutorial_general_100.png

[200]: ./media/workplacebyfacebook-tutorial/tutorial_general_200.png
[201]: ./media/workplacebyfacebook-tutorial/tutorial_general_201.png
[202]: ./media/workplacebyfacebook-tutorial/tutorial_general_202.png
[203]: ./media/workplacebyfacebook-tutorial/tutorial_general_203.png

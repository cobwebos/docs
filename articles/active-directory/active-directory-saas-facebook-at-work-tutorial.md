---
title: "教程：Azure Active Directory 与 Workplace by Facebook 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Workplace by Facebook 间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: jeedes
ms.openlocfilehash: 4f34f6509d762cba6aba98a6eba010fd94656e67
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>教程：Azure Active Directory 与 Workplace by Facebook 集成

本教程介绍如何将 Workplace by Facebook 与 Azure Active Directory (Azure AD) 集成。

将 Workplace by Facebook 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Workplace by Facebook。
- 可以让用户使用其 Azure AD 帐户自动登录到 Workplace by Facebook（单一登录）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关服务型软件 (SaaS) 应用与 Azure AD 集成的更多详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workplace by Facebook 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Workplace by Facebook 单一登录 (SSO) 的订阅

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD SSO。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Workplace by Facebook。
2. 配置和测试 Azure AD 单一登录。

## <a name="add-workplace-by-facebook-from-the-gallery"></a>从库添加 Workplace by Facebook
要配置 Workplace by Facebook 与 Azure AD 的集成，请从库中将 Workplace by Facebook 添加到托管 SaaS 应用列表。

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮][1]

2. 浏览到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“Workplace by Facebook”，然后从结果中选择“Workplace by Facebook”。 然后选择“添加”，以添加该应用程序。

    ![结果列表中的 Workplace by Facebook](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_search.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Workplace by Facebook 的 Azure AD SSO。

为使 SSO 能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Workplace by Facebook 用户。 换句话说，应在 Azure AD 用户与 Workplace by Facebook 中相关用户之间建立起关联。

通过将 Azure AD 中“用户名”的值指定为 Workplace by Facebook 中“用户名”的值来建立此关联。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD SSO 并在 Workplace by Facebook 应用程序中配置 SSO。

1. 在 Azure 门户中的“Workplace by Facebook 应用程序集成”页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 请在“单一登录”对话框中，为“模式”选择“基于 SAML 的登录”来启用 SSO。
 
    ![“单一登录”对话框](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_samlbase.png)

3. 在“Workplace by Facebook 域和 URL”部分，执行以下步骤：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<company subdomain>.facebook.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://www.facebook.com/company/<scim company id>`

    > [!NOTE]
    > 这些仅为示例值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Workplace by Facebook 客户端支持团队](https://workplace.fb.com/faq/)获取这些值。 

4. 在“SAML 签名证书”部分，选择“证书 (Base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_certificate.png) 

5. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_400.png)

6. 在“Workplace by Facebook 配置”部分，选择“配置 Workplace by Facebook”，打开“配置登录”窗口。 从“快速参考”部分，复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![Workplace by Facebook 配置](./media/active-directory-saas-facebook-at-work-tutorial/config.png) 

7. 在另一 Web 浏览器窗口中，以管理员身份登录 Workplace by Facebook 公司站点。
  
   > [!NOTE] 
   > 作为 SAML 身份验证流程的一部分，Workplace 可使用查询字符串（最大为 2.5 KB）将参数传递给 Azure AD。

8. 在“公司仪表板”中，转到“身份验证”选项卡。

9. 在“SAML 身份验证”下，从下拉列表中选择“仅 SSO”。

10. 将从 Azure 门户的“Workplace by Facebook 配置”部分复制的值输入到相应字段中：

    *   在“SAML URL”文本框中，粘贴从 Azure 门户复制的“单一登录服务 URL”值。
    *   在“SAML 颁发者 URL”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。
    *   在“SAML 注销重定向”（可选）中，粘贴从 Azure 门户复制的“注销 URL”值。
    *   在记事本中打开从 Azure 门户下载的“base-64 编码证书”。 将其内容复制到剪贴板，然后将其粘贴到“SAML 证书”文本框。

11. 可能需要输入“SAML 配置”部分下列出的受众 URL、收件人 URL 和 ACS（断言使用者服务）URL。

12. 滚动到该部分的底部，选择“测试 SSO”。 随即将显示一个带 Azure AD 登录页的弹出窗口。 要进行身份验证，请照常输入凭据。 确保从 Azure AD 返回的电子邮件地址与用于登录的 Workplace 帐户相同。

13. 如果测试已成功完成，则滚动到页面底部，然后选择“保存”。

14. 现在，使用 Workplace 的任何用户都会在 Azure AD 登录页面上进行身份验证。

你可以选择配置一个 SAML 注销 URL，用于指向 Azure AD 的注销页面。 启用并配置此设置后，用户将不会再被定向到 Workplace 注销页面。 取而代之的，用户会被重定向到在“SAML 注销重定向”设置中添加的 URL。


> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了。 从“Active Directory” > “企业应用程序”部分添加此应用后，只需选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)中阅读有关嵌入式文档功能的详细信息。

### <a name="configure-reauthentication-frequency"></a>配置重新进行身份验证的频率

可将 Workplace 配置为每天、每三天、每周、每两周、每个月提示进行 SAML 检查或从不进行检查。

> [!NOTE] 
>移动应用程序上 SAML 检查的最小值为每周。

你也可以强制为所有用户重置 SAML。 若要执行此操作，使用“现在需要对所有用户进行 SAML 身份验证”按钮。


### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

1. 在 **Azure 门户** 的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请选择“添加”。
 
    ![“添加”按钮](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框中执行以下操作：
 
    ![“用户”对话框](./media/active-directory-saas-facebook-at-work-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入“BrittaSimon”。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”，并记下。

    d.单击“下一步”。 选择“创建” 。
 
### <a name="create-a-workplace-by-facebook-test-user"></a>创建 Workplace by Facebook 测试用户

本部分将在 Workplace by Facebook 中创建一个名为 Britta Simon 的用户。 Workplace by Facebook 支持默认启用的实时预配。

在此部分中无需进行任何操作。 尝试访问 Workplace by Facebook 时，如果 Workplace by Facebook 中尚不存在用户，则系统会创建一个新用户。

>[!Note]
>如需手动创建用户，请联系 [Workplace by Facebook 客户端支持团队](https://workplace.fb.com/faq/)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Workplace by Facebook 的权限，使她能够使用 Azure SSO。

![分配用户][200] 

1. 在 Azure 门户中，打开应用程序视图。 转到“目录视图”，然后转到“企业应用程序”，并选择“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Workplace by Facebook”。

    ![应用程序列表中的 Workplace by Facebook 链接](./media/active-directory-saas-facebook-at-work-tutorial/tutorial_workplacebyfacebook_app.png) 

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202] 

4. 选择“添加”。 然后，在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中，选择“用户”列表内的“Britta Simon”。

6. 在“用户和组”对话框中，选择“选择”。

7. 在“添加分配”对话框中选择“分配”。
    
### <a name="test-single-sign-on"></a>测试单一登录

如果要测试 SSO 设置，请打开访问面板。
有关详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。


## <a name="next-steps"></a>后续步骤

* 请参阅[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)。
* 请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)。
* 了解有关如何[配置用户预配](active-directory-saas-facebook-at-work-provisioning-tutorial.md)。



<!--Image references-->

[1]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-facebook-at-work-tutorial/tutorial_general_203.png


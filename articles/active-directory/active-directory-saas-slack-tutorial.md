---
title: "教程：Azure Active Directory 与 Slack 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Slack 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: a1d7c2f01d3bf345f63077c4d42d79476235e941
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>教程：Azure Active Directory 与 Slack 集成

在本教程中，了解如何将 Slack 与 Azure Active Directory (Azure AD) 集成。

将 Slack 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Slack。
* 让用户使用其 Azure AD 帐户自动单一登录 (SSO) 到 Slack。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解软件即服务 (SaaS) 应用与 Azure AD 的集成，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Slack 的集成，需要具有以下项：

* Azure AD 订阅
* 已启用 Slack SSO 的订阅

>[!NOTE]
>不建议使用生产环境测试本教程中的步骤。
>
>

若要测试本教程中的步骤，请遵循以下建议：

* 仅在必要时才使用生产环境。
* 如果没有 Azure AD 试用环境，请获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="scenario-description"></a>方案描述
本教程将在测试环境中测试 Azure AD SSO。 要遵循的方案包括两个主要构建基块：

* 从库中添加 Slack
* 配置和测试 Azure AD SSO

## <a name="add-slack-from-the-gallery"></a>从库中添加 Slack
若要配置 Slack 与 Azure AD 的集成，请执行以下操作，将 Slack 从库添加到托管 SaaS 应用列表：

1. 打开 [Azure 门户](https://portal.azure.com)。
2. 在左窗格中单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮][1]

3. 转到“企业应用程序”，然后选择“所有应用程序”。

    ![“企业应用程序”边栏选项卡中的“所有应用程序”按钮][2]

4. 在“所有应用程序”对话框顶部，单击“添加”。

    ![“所有应用程序”对话框中的“添加”按钮][3]

5. 在搜索框中，键入“Slack”。

    ![“添加应用程序”搜索框](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. 在结果窗格中选择“Slack”，然后单击“添加”按钮添加该应用程序。

     ![在结果窗格中选择“Slack”](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO
在本部分，我们将使用测试用户“Britta Simon”来配置和测试 Slack 的 Azure AD SSO。

若要正常运行 SSO，Azure AD 必须在 Azure AD 用户与其对应的 Slack 用户之间建立链接关系。 可以通过将 Azure AD 中“用户名”的值分配为 Slack 中“用户名”的值来建立此链接关系。

若要配置和测试 Slack 的 Azure AD SSO，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)，以使用 Britta Simon 用户测试 Azure AD SSO。
3. [创建 Slack 测试用户](#creating-a-slack-test-user)，使 Azure AD 用户 Britta Simon 链接到 Slack 中的对应用户。
4. [分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)，使用户 Britta Simon 能够使用 Azure AD SSO。
5. [测试单一登录](#testing-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将执行以下操作，在 Azure 门户中启用 Azure AD SSO，并在 Slack 应用程序中配置 SSO：

1. 在 Azure 门户中的 **Slack** 应用程序集成页上，单击“单一登录”。

    ![Slack 应用程序集成页][4]

2. 在“单一登录”对话框中的“模式”列表内，选择“基于 SAML 的登录”启用 SSO。

    ![“单一登录”对话框](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. 在“Slack 域和 URL”下面执行以下步骤：

    ![“单一登录”对话框](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)
  1. 在“登录 URL”框中，键入使用命名约定 _https://<company name>.slack.com_ 的 URL。
  2. 在“标识符”框中，键入 **https://slack.com**。

     >[!NOTE]
     >上面的值不是实际值。 此处我们建议为 URL 和标识符使用唯一值。 稍后，我们将使用实际 URL 和标识符更新这些值。 若要获取这些值，请联系 [Slack 支持团队](https://slack.com/help/contact)。
     >
     >

4. Slack 应用程序要求以特定的格式显示安全断言标记语言 (SAML) 断言。 在 Slack 应用程序集成页的“用户属性”部分中配置声明并管理这些属性的值，如以下屏幕截图中所示：

    ![在“用户属性”部分中配置声明](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. 在“单一登录”对话框中的“用户属性”部分内，为“用户标识符”选择“user.mail”。 针对下表中的每一行执行以下步骤：

    | 属性名称 | 属性值 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    1. 单击“添加属性”按钮。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    2. 在“添加属性”对话框中的“名称”框内，输入表的“属性名称”列中的第一个名称。

    ![配置单一登录](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    3. 在“值”框中，输入表的“属性值”列中的第一个值。
    4. 单击 **“确定”**。
    5. 针对后面的三个表行重复步骤“a”到“d”。

6. 在“SAML 签名证书”下面，单击“创建新证书”。

    ![创建证书](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. 在“创建新证书”对话框中单击“日历”按钮，选择一个过期（失效）日期，然后单击“保存”。

    ![选择证书过期日期](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. 在“SAML 签名证书”下面选中“激活新证书”复选框，然后单击“保存”。

    ![激活 SAML 签名证书](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. 在“滚动更新证书”弹出窗口中，单击“确定”。

    ![“滚动更新证书”弹出窗口](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. 在“SAML 签名证书”下面，单击“证书(base64)”，然后将证书文件保存到本地驱动器。

    ![将证书保存到本地驱动器](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. 在“Slack 配置”下面，单击“配置 Slack”打开“配置登录”窗口。

    ![单击“配置 Slack”打开“配置登录”窗口](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![“登录配置”窗口](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. 打开一个新的浏览器窗口，然后以管理员身份登录到 Slack 公司站点。

13. 转到“Microsoft Azure AD”，然后转到“团队设置”。

    ![Slack 公司站点上的 Microsoft Azure AD“团队设置”按钮](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. 在“团队设置”下面单击“身份验证”选项卡，然后单击“更改设置”。

    ![“团队设置”页上的“更改设置”按钮](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. 在“SAML 身份验证设置”对话框中，执行以下步骤：

    ![“SAML 身份验证设置”对话框](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)
  1. 在“SAML 2.0 终结点(HTTP)”框中，输入 Azure AD 应用程序配置窗口中“SAML 单一登录服务 URL”值。
  2. 在“标识提供者颁发者”框中，输入 Azure AD 应用程序配置窗口中“SAML 实体 ID”值。
  3. 在记事本中打开下载的证书文件，复制其内容，然后将其粘贴到“公共证书”框中。
  4. 根据 Slack 团队的需要配置上述三项设置。 有关设置的详细信息，请参阅 [Guide to single sign-on with Slack](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack)（使用 Slack 单一登录指南）。
  5. 单击“保存配置”。
  6. 取消选择“允许用户更改其电子邮件地址”。
  7. 选择“允许用户选择自己的用户名”。
  8. 对于“必须对团队进行身份验证”，请选择“可选”。
  
### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分，我们将执行以下操作，在 Azure 门户中创建一个名为 Britta Simon 的测试用户：

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. 转到“用户和组”，然后单击“所有用户”显示用户列表。

    ![Azure AD 中的“所有用户”按钮](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. 在“所有用户”对话框顶部，单击“添加”。

    ![“添加用户”对话框中的“添加”按钮](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中输入以下信息：

    ![“用户”对话框](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)
  1. 在“姓名”框中，键入“BrittaSimon”。
  2. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。
  3. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
  4. 单击“创建”。

### <a name="create-a-slack-test-user"></a>创建 Slack 测试用户

在本部分，我们将在 Slack 中创建一个名为 Britta Simon 的用户。 Slack 支持默认启用的实时预配。

此部分不存在任何操作项。 尝试访问 Slack 时，如果 Slack 中没有用户，系统会创建一个新用户。

>[!NOTE]
>如果需要手动创建用户，请联系 [Slack 支持团队](https://slack.com/help/contact)。
>
>

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们将通过向 Britta Simon 授予对 Slack 的访问权限，使该用户能够使用 Azure SSO。

![分配 Azure SSO 用户][200]

若要将用户 Britta Simon 分配到 Slack，请执行以下步骤：

1. 在 Azure 门户中，打开应用程序视图，转到目录视图，转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201]

2. 在“应用程序”列表中选择“Slack”。

    ![Azure 门户中的应用程序列表](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. 在左窗格中，单击“用户和组”。

    ![左窗格中的“用户和组”按钮][202]

4. 单击“添加”按钮，然后在“添加分配”边栏选项卡中选择“用户和组”。

    ![“添加”按钮和“添加分配”边栏选项卡][203]

5. 在“用户和组”对话框中的“用户”列表内，选择“Britta Simon”。

6. 单击“选择”按钮。

7. 在“添加分配”边栏选项卡中，单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分，我们将使用访问面板测试 Azure AD SSO 配置。

若要测试配置，请转到访问面板中，然后单击“Slack”磁贴。 该用户应自动登录到 Slack 应用程序。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png


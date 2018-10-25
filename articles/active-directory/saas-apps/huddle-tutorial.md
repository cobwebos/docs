---
title: 教程：Azure Active Directory 与 Huddle 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Huddle 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 050a5a210ab10a557b149513a0416b188f4618ba
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2018
ms.locfileid: "48885897"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>教程：Azure Active Directory 与 Huddle 集成

本教程介绍了如何将 Huddle 与 Azure Active Directory (Azure AD) 集成。

将 Huddle 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Huddle
- 可让用户使用其 Azure AD 帐户自动登录到 Huddle（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Huddle 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Huddle 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Huddle
2. 配置和测试 Azure AD 单一登录

## <a name="adding-huddle-from-the-gallery"></a>从库中添加 Huddle

若要配置 Huddle 与 Azure AD 的集成，需从库中将 Huddle 添加到托管 SaaS 应用列表。

**若要从库中添加 Huddle，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Huddle”。 从结果面板中选择“Huddle”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Huddle 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Huddle 用户。 换句话说，需要建立 Azure AD 用户与 Huddle 中相关用户之间的链接关系。

若要配置和测试 Huddle 的 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Huddle 测试用户](#creating-a-huddle-test-user)** - 在 Huddle 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Huddle 应用程序中配置单一登录。

**若要配置 Huddle 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Huddle”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 单击屏幕顶部的“更改单一登录模式”，以选择 **SAML** 模式。

    ![配置单一登录](./media/huddle-tutorial/tutorial_general_300.png)

3. 在“选择单一登录方法”对话框中，对 SAML 模式单击“选择”以启用单一登录。

    ![配置单一登录](./media/huddle-tutorial/tutorial_general_301.png)

4. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](./media/huddle-tutorial/tutorial_general_302.png)

5. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    > [!NOTE]
    > 将在下面输入的域中自动检测 huddle 实例。

    ![Huddle 域和 URL 单一登录信息](./media/huddle-tutorial/tutorial_huddle_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“标识符”文本框中，键入一个 URL：

    | | |
    |--|--|
    | `https://login.huddle.net`|
    | `https://login.huddle.com`|
    | |

    b. 在“回复 URL”文本框中键入 URL：

    | | |
    |--|--|
    | `https://login.huddle.net/saml/browser-sso`|
    | `https://login.huddle.com/saml/browser-sso`|
    | `https://login.huddle.com/saml/idp-initiated-sso`|
    | |

    c. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Huddle 域和 URL 单一登录信息](./media/huddle-tutorial/tutorial_huddle_url1.png)

    在“登录 URL”文本框中，使用以下模式键入任一 URL：

    | | |
    |--|--|
    | `https://<customsubdomain>.huddle.com`|
    | `https://us.huddle.com`|
    | |

    > [!NOTE]
    > 登录 URL 值不是实际值。 使用实际登录 URL 更新此值。 请联系 [Huddle 客户端支持团队](https://huddle.zendesk.com)获取这些值。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“下载”以根据要求下载相应的证书并将其保存在计算机上。

    ![配置单一登录](./media/huddle-tutorial/tutorial_huddle_certificate.png)

7. 在“设置 Huddle”部分中，根据要求复制相应 URL。

    ![配置单一登录](./media/huddle-tutorial/tutorial_huddle_configure.png)

8. 若要在 **Huddle** 端配置单一登录，需要将已下载的证书和从 Azure 门户的“设置 Huddle”部分复制的 URL 发送到 [Huddle 客户端支持团队](https://huddle.zendesk.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

    >[!NOTE]
    > 单一登录需要由 Huddle 支持团队启用。 配置完成后，会收到通知。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，选择“Azure Active Directory”，接着选择“用户”，然后选择“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](./media/huddle-tutorial/create_aaduser_01.png) 

3. 在“用户属性”中，执行以下步骤。

    ![创建 Azure AD 测试用户](./media/huddle-tutorial/create_aaduser_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-a-huddle-test-user"></a>创建 Huddle 测试用户

为了使 Azure AD 用户能够登录到 Huddle，必须将其预配到 Huddle 中。 对于 Huddle，预配是一项手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录到 **Huddle** 公司站点。

2. 单击“工作区”。

3. 单击“人员”**\>“邀请人员”**。

    ![人员](./media/huddle-tutorial/IC787838.png "人员")

4. 在“创建新邀请”部分中，执行以下步骤：
  
    ![新建邀请](./media/huddle-tutorial/IC787839.png "New Invitation")
  
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“选择要邀请人员加入的团队”列表中，选择“团队”。

    b. 在“输入要邀请的人员的电子邮件地址”文本框中，键入希望预配的有效 Azure AD 帐户的“电子邮件地址”。

    c. 单击“邀请”。

    >[!NOTE]
    > Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。

>[!NOTE]
>可以使用任何其他 Huddle 用户帐户创建工具或 Huddle 提供的 API 来预配 Azure AD 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 对 Huddle 的访问权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Huddle”。

    ![配置单一登录](./media/huddle-tutorial/tutorial_huddle_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Huddle 磁贴时，应自动登录到 Huddle 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png

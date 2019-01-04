---
title: 教程：Azure Active Directory 与 ServiceNow 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 ServiceNow 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: jeedes
ms.openlocfilehash: 5b7baffea8e718810a91ea9687a007d36c806aab
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850000"
---
# <a name="tutorial-azure-active-directory-integration-with-servicenow"></a>教程：Azure Active Directory 与 ServiceNow 集成

在本教程中，了解如何将 ServiceNow 与 Azure Active Directory (Azure AD) 集成。

将 ServiceNow 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 ServiceNow。
- 可以让用户使用其 Azure AD 帐户自动登录到 ServiceNow（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 ServiceNow 的集成，需做好以下准备：

- Azure AD 订阅
- 对于 ServiceNow，需要 Calgary 版本或更高版本的 ServiceNow 实例或租户
- 对于 ServiceNow Express，需要 Helsinki 版本或更高版本的 ServiceNow Express 实例
- ServiceNow 租户必须启用[多提供程序单一登录插件](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)。 这可以通过[提交服务请求](https://hi.service-now.com)完成。
- 对于自动配置，请为 ServiceNow 启用多提供程序插件。

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ServiceNow
2. 配置和测试 Azure AD 单一登录

## <a name="adding-servicenow-from-the-gallery"></a>从库中添加 ServiceNow

要配置 ServiceNow 与 Azure AD 的集成，需要从库中将 ServiceNow 添加到托管 SaaS 应用列表。

**若要从库中添加 ServiceNow，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **ServiceNow**，在结果面板中选择“ServiceNow”，单击“添加”按钮添加该应用程序。

    ![结果列表中的 ServiceNow](./media/servicenow-tutorial/tutorial_servicenow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户使用 ServiceNow 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 ServiceNow 用户。 换句话说，需要建立 Azure AD 用户与 ServiceNow 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 ServiceNow 中“用户名”的值来建立此链接关系。

若要配置和测试 ServiceNow 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[针对 ServiceNow 配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on-for-servicenow)** - 让用户能够使用此功能。
2. **[针对 ServiceNow Express 配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on-for-servicenow-express)** - 让用户能够使用此功能。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[创建 ServiceNow 测试用户](#create-a-servicenow-test-user)** - 在 ServiceNow 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
5. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on-for-servicenow"></a>为 ServiceNow 配置 Azure AD 单一登录

本部分介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 ServiceNow 应用程序中配置单一登录。

**若要配置 ServiceNow 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **ServiceNow** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 单击屏幕顶部的“更改单一登录模式”，以选择“SAML”模式。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_general_300.png)

3. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_general_301.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_general_302.png)

5. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![ServiceNow 域和 URL 单一登录信息](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<instance-name>.service-now.com/navpage.do`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<instance-name>.service-now.com`

    > [!NOTE]
    > 这些不是实际值。 稍后需要基于实际登录 URL 和标识符更新这些值（本教程稍后会介绍）。

6. 在“SAML 签名证书”部分中执行以下步骤：

    ![证书下载链接](./media/servicenow-tutorial/tutorial_servicenow_certificate.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“复制”按钮来复制**应用联合元数据 URL** 并将其粘贴到记事本，因为本教程中稍后将使用此应用联合元数据 URL。

    b. 单击“下载”以下载“证书(Base64)”，并将证书保存在计算机上。

7. 以管理员身份登录到 ServiceNow 应用程序。

8. 请按照以下步骤操作，激活“集成 - 多提供程序单一登录安装程序”插件：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在左侧导航窗格中，通过搜索栏搜索“系统定义”部分，单击“插件”。

    ![激活插件](./media/servicenow-tutorial/tutorial_servicenow_03.png "激活插件")

     b. 搜索“集成 - 多提供程序单一登录安装程序”。

     ![激活插件](./media/servicenow-tutorial/tutorial_servicenow_04.png "激活插件")

    c. 选择插件。 单击右键并选择“激活/升级”。

     ![激活插件](./media/servicenow-tutorial/tutorial_activate.png "激活插件")

    d. 单击“激活”按钮。

     ![激活插件](./media/servicenow-tutorial/tutorial_activate1.png "激活插件")

9. 在左侧导航窗格中，通过搜索栏搜索“多提供程序 SSO”，单击“属性”。

    ![配置应用 URL](./media/servicenow-tutorial/tutorial_servicenow_06.png "配置应用 URL")

10. 在“多提供程序 SSO 属性”对话框中，执行以下步骤：

    ![配置应用 URL](./media/servicenow-tutorial/ic7694981.png "配置应用 URL")

    * 对于“启用多提供程序 SSO”，选择“是”。
  
    * 对于“启用将所有标识提供者中的用户自动导入用户表”，选择“是”。

    * 对于“为多提供程序 SSO 集成启用调试日志记录”，选择“是”。

    * 在“...用户表上的字段”文本框中，键入“user_name”。
  
    * 单击“ **保存**”。

11. 可通过两种方式配置 **ServiceNow** - 自动和手动。

12. 若要自动配置 **ServiceNow**，请执行以下步骤：

    * 返回到 Azure 门户中的 ServiceNow 单一登录页。

    * 针对 ServiceNow 提供了一键式配置服务，即，让 Azure AD 自动配置 ServiceNow 的基于 SAML 的身份验证。 若要启用此服务，请转到“ServiceNow 配置”部分，单击“配置 ServiceNow”打开“配置登录”窗口。

        ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    * 在“配置登录”窗体中输入 ServiceNow 实例名称、管理员用户名和管理员密码，单击“立即配置”。 请注意，为使此操作生效，提供的管理员用户名必须具有在 ServiceNow 中分配的 **security_admin** 角色。 否则，若要手动将 ServiceNow 配置为使用 Azure AD 作为 SAML 标识提供者，请单击“手动配置单一登录”，从“快速参考”部分复制“注销 URL、SAML 实体 ID 和 SAML 单一登录服务 URL”。

        ![配置应用 URL](./media/servicenow-tutorial/configure.png "配置应用 URL")

    * 以管理员身份登录到 ServiceNow 应用程序。

    * 在自动配置中，所有必要的设置都在 **ServiceNow** 端配置，但 **X.509 证书**默认情况下未启用。 必须手动将其映射到 ServiceNow 中的标识提供者。 请同样按照以下步骤操作：

    * 在左侧导航窗格中，通过搜索栏搜索“多提供者 SSO”，单击“标识提供者”。

        ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_07.png "配置单一登录")

    * 单击自动生成的标识提供者

        ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_08.png "配置单一登录")

    *  在“标识提供者”部分中，执行以下步骤：

        ![配置单一登录](./media/servicenow-tutorial/automatic_config.png "配置单一登录")

        * 在“名称”文本框中，为你的配置键入一个名称（例如，**Microsoft Azure Federated Single Sign-on**）。

        * 请从文本框中删除已填充的**标识提供者的 SingleLogoutRequest** 值。

        * 复制“ServiceNow 主页”值，将其粘贴到 Azure 门户上“ServiceNow 域和 URL”部分中的“登录 URL”文本框内。

            > [!NOTE]
            > ServiceNow 实例主页是 **ServieNow 租户 URL** 和 **/navpage.do** 的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。

        * 复制“实体 ID/颁发者”值，将其粘贴到 Azure 门户上“ServiceNow 域和 URL”部分中的“标识符”文本框内。

        * 请确保将“名称 ID 策略”设置为 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。 

    * 向下滚动到“X.509 证书”部分，选择“编辑”。

        ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_09.png "配置单一登录")

    * 选择证书，然后单击右箭头图标以添加该证书

        ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_11.png "配置单一登录")

    * 单击“ **保存**”。

    * 单击页面右上角的“测试连接”。

        ![激活插件](./media/servicenow-tutorial/tutorial_activate2.png "激活插件")

    * 单击“测试连接”后，将会显示一个弹出窗口，你需要在其中输入凭据，将显示包含结果的以下页面。 “SSO 注销测试结果”错误是意料之中的，请忽略该错误并单击“激活”按钮。

        ![配置单一登录](./media/servicenow-tutorial/servicenowactivate.png "配置单一登录")
  
13. 若要手动配置 **ServiceNow**，请执行以下步骤：

    * 以管理员身份登录到 ServiceNow 应用程序。

    * 在左侧导航窗格中，单击“标识提供者”。

        ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_07.png "配置单一登录")

    * 在“标识提供者”对话框中，单击“新建”。

        ![配置单一登录](./media/servicenow-tutorial/ic7694977.png "配置单一登录")

    * 在“标识提供者”对话框中，单击“SAML”。

        ![配置单一登录](./media/servicenow-tutorial/ic7694978.png "配置单一登录")

    * 在“导入标识提供者元数据”弹出窗口中，执行以下步骤：

        ![配置单一登录](./media/servicenow-tutorial/idp.png "配置单一登录")

        * 输入从 Azure 门户复制的**应用联合元数据 URL**。

        * 单击“导入”。

    * 它将读取 IdP 元数据 URL，并填充所有字段信息。

        ![配置单一登录](./media/servicenow-tutorial/ic7694982.png "配置单一登录")

        * 在“名称”文本框中，为你的配置键入一个名称（例如，**Microsoft Azure Federated Single Sign-on**）。

        * 请从文本框中删除已填充的**标识提供者的 SingleLogoutRequest** 值。

        * 复制“ServiceNow 主页”值，将其粘贴到 Azure 门户上“ServiceNow 域和 URL”部分中的“登录 URL”文本框内。

            > [!NOTE]
            > ServiceNow 实例主页是 **ServiceNow 租户 URL** 和 **/navpage.do** 的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。

        * 复制“实体 ID/颁发者”值，将其粘贴到 Azure 门户上“ServiceNow 域和 URL”部分中的“标识符”文本框内。

        * 请确保将“名称 ID 策略”设置为 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。

        * 单击“高级”。 在“用户字段”文本框中，键入“电子邮件”或“user_name”，具体取决于哪个字段用于唯一标识 ServiceNow 部署中的用户。

            > [!NOTE]
            > 可以配置 Azure AD 以发出 Azure AD 用户 ID（用户主体名称）或电子邮件地址作为 SAML 令牌中的唯一标识符，方法是转到 Azure 门户的“ServiceNow”>“属性”>“单一登录”部分，并将所需字段映射到 **nameidentifier** 属性。 Azure AD 中为所选属性存储的值（例如用户主体名称）必须匹配 ServiceNow 中为输入字段存储的值（例如 user_name）

        * 单击页面右上角的“测试连接”。

        * 单击“测试连接”后，将会显示一个弹出窗口，你需要在其中输入凭据，将显示包含结果的以下页面。 “SSO 注销测试结果”错误是意料之中的，请忽略该错误并单击“激活”按钮。

          ![配置单一登录](./media/servicenow-tutorial/servicenowactivate.png "配置单一登录")

### <a name="configure-azure-ad-single-sign-on-for-servicenow-express"></a>为 ServiceNow Express 配置 Azure AD 单一登录

1. 在 Azure 门户中的 **ServiceNow** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 单击屏幕顶部的“更改单一登录模式”，以选择“SAML”模式。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_general_300.png)

3. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_general_301.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_general_302.png)

5. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入值：`https://<instance-name>.service-now.com/navpage.do`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<instance-name>.service-now.com`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [ServiceNow 客户端支持团队](https://www.servicenow.com/support/contact-support.html)获取这些值。

6. 在“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_certificates.png)

7. 针对 ServiceNow 提供了一键式配置服务，即，让 Azure AD 自动配置 ServiceNow 的基于 SAML 的身份验证。 若要启用此服务，请转到“设置 ServiceNow”部分，单击“查看分步说明”以打开“配置登录”窗口。

    ![配置单一登录](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

8. 在“配置登录”窗体中输入 ServiceNow 实例名称、管理员用户名和管理员密码，单击“立即配置”。 请注意，为使此操作生效，提供的管理员用户名必须具有在 ServiceNow 中分配的 **security_admin** 角色。 否则，若要手动将 ServiceNow 配置为使用 Azure AD 作为 SAML 标识提供者，请单击“手动配置单一登录”，从“快速参考”部分复制“注销 URL、SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置应用 URL](./media/servicenow-tutorial/configure.png "配置应用 URL")

9. 以管理员身份登录到 ServiceNow Express 应用程序。

10. 在左侧导航窗格中，单击“单一登录”。

    ![配置应用 URL](./media/servicenow-tutorial/ic7694980ex.png "配置应用 URL")

11. 在“单一登录”对话框中，单击右上角的配置图标，设置以下属性：

    ![配置应用 URL](./media/servicenow-tutorial/ic7694981ex.png "配置应用 URL")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 将“启用多提供程序 SSO”切换到右侧。

    b. 将“为多提供程序 SSO 集成启用调试日志记录”切换到右侧。

    c. 在“...用户表上的字段”文本框中，键入“user_name”。

12. 在“单一登录”对话框中，单击“添加新证书”。

    ![配置单一登录](./media/servicenow-tutorial/ic7694973ex.png "配置单一登录")

13. 在“x.509 证书”对话框中，执行以下步骤：

    ![配置单一登录](./media/servicenow-tutorial/ic7694975.png "配置单一登录")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”文本框中，键入配置名称（例如：TestSAML2.0）。

    b. 选择“活动”。

    c. 对于“格式”，选择“PEM”。

    d. 对于“类型”，选择“信任存储证书”。

    e. 在记事本中打开从 Azure 门户下载的 Base64 编码证书，将其内容复制到剪贴板，再将其粘贴到“PEM 证书”文本框。

    f. 单击“更新”。

14. 在“单一登录”对话框中，单击“添加新 IdP”。

    ![配置单一登录](./media/servicenow-tutorial/ic7694976ex.png "配置单一登录")

15. 在“添加新的标识提供者”对话框的“配置标识提供者”下，执行以下步骤：

    ![配置单一登录](./media/servicenow-tutorial/ic7694982ex.png "配置单一登录")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”文本框中，键入配置名称（例如：SAML 2.0）。

    b. 在“标识提供者 URL”字段中，粘贴从 Azure 门户复制的“标识提供者 ID”的值。

    c. 在“标识提供者的 AuthnRequest”字段中，粘贴从 Azure 门户复制的“身份验证请求 URL”的值。

    d. 在“标识提供者的 SingleLogoutRequest”字段中，粘贴从 Azure 门户复制的“单一注销服务 URL”的值

    e. 对于“标识提供者证书”，选择在上一步已创建的证书。

16. 单击“高级设置”，并在“其他标识提供者属性”下，执行以下步骤：

    ![配置单一登录](./media/servicenow-tutorial/ic7694983ex.png "配置单一登录")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“IDP 的 SingleLogoutRequest 协议绑定”文本框中，键入“urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect”。

    b. 在“NameID 策略”文本框中，键入“urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified”。

    c. 在“AuthnContextClassRef 方法”中，键入 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`。

    d. 取消选择“创建 AuthnContextClass”。

17. 在“其他服务提供者属性”下，执行以下步骤：

    ![配置单一登录](./media/servicenow-tutorial/ic7694984ex.png "配置单一登录")

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“ServiceNow 主页”文本框中，键入 ServiceNow 实例主页的 URL。

    > [!NOTE]
    > ServiceNow 实例主页是 **ServiceNow 租户 URL** 和 **/navpage.do** 的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。

    b. 在“实体 ID/颁发者”文本框中，键入 ServiceNow 租户的 URL。

    c. 在“受众 URI”文本框中，键入 ServiceNow 租户的 URL。

    d. 在“时钟偏差”文本框中，键入“60”。

    e. 在“用户字段”文本框中，键入“电子邮件”或“user_name”，具体取决于哪个字段用于唯一标识 ServiceNow 部署中的用户。

    > [!NOTE]
    > 可以配置 Azure AD 以发出 Azure AD 用户 ID（用户主体名称）或电子邮件地址作为 SAML 令牌中的唯一标识符，方法是转到 Azure 门户的“ServiceNow”>“属性”>“单一登录”部分，并将所需字段映射到 **nameidentifier** 属性。 Azure AD 中为所选属性存储的值（例如用户主体名称）必须匹配 ServiceNow 中为输入字段存储的值（例如 user_name）

    f. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](./media/servicenow-tutorial/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](./media/servicenow-tutorial/create_aaduser_02.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="create-a-servicenow-test-user"></a>创建 ServiceNow 测试用户

本部分的目的是在 ServiceNow 中创建名为“Britta Simon”的用户。 ServiceNow 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](servicenow-provisioning-tutorial.md)。

> [!NOTE]
> 如果需要手动创建用户，则需要联系 [ServiceNow 客户端支持团队](https://www.servicenow.com/support/contact-support.html)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 ServiceNow 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“ServiceNow”。

    ![应用程序列表中的 ServiceNow 链接](./media/servicenow-tutorial/tutorial_servicenow_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击“访问面板”中的 ServiceNow 磁贴时，应自动登录到 ServiceNow 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置用户预配](servicenow-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/servicenow-tutorial/tutorial_general_01.png
[2]: ./media/servicenow-tutorial/tutorial_general_02.png
[3]: ./media/servicenow-tutorial/tutorial_general_03.png
[4]: ./media/servicenow-tutorial/tutorial_general_04.png

[100]: ./media/servicenow-tutorial/tutorial_general_100.png

[200]: ./media/servicenow-tutorial/tutorial_general_200.png
[201]: ./media/servicenow-tutorial/tutorial_general_201.png
[202]: ./media/servicenow-tutorial/tutorial_general_202.png
[203]: ./media/servicenow-tutorial/tutorial_general_203.png

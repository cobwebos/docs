---
title: 教程：Azure Active Directory 与 Zendesk 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zendesk 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268168"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>教程：Azure Active Directory 与 Zendesk 集成

本教程介绍了如何将 Zendesk 与 Azure Active Directory (Azure AD) 集成。

将 Zendesk 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Zendesk。
- 可以让用户使用其 Azure AD 帐户自动登录到 Zendesk（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zendesk 的集成，需备齐以下项：

- Azure AD 订阅
- 已启用 Zendesk 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。
本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Zendesk
2. 配置和测试 Azure AD 单一登录

## <a name="adding-zendesk-from-the-gallery"></a>从库中添加 Zendesk

若要配置 Zendesk 与 Azure AD 的集成，需要从库中将 Zendesk 添加到托管 SaaS 应用列表。

若要从库中添加 Zendesk，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/zendesk-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/zendesk-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/zendesk-tutorial/a_new_app.png)

4. 在搜索框中，键入“Zendesk”，在结果面板中选择“Zendesk”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 Zendesk 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Zendesk 用户。 换句话说，需要建立 Azure AD 用户与 Zendesk 中相关用户之间的关联关系。

通过将 Azure AD 中“用户名”的值指定为 Zendesk 中“用户名”的值来建立此链接关系。

若要配置和测试 Zendesk 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Zendesk 测试用户](#create-a-zendesk-test-user)** - 在 Zendesk 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Zendesk 应用程序中配置单一登录。

若要配置 Zendesk 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Zendesk 应用程序集成页上，选择“单一登录”。

    ![图像](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. 单击屏幕顶部的“更改单一登录模式”，以选择“SAML”模式。

      ![图像](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![图像](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![图像](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.zendesk.com`。

    b. 在“标识符”文本框中，使用以下模式键入 URL：`<subdomain>.zendesk.com`。

    ![图像](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Zendesk 客户端支持团队](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)获取这些值。

6. Zendesk 需要特定格式的 SAML 断言。 没有强制的 SAML 属性，但可以选择添加应用程序集成页上的“用户属性”部分中的属性。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](./media/zendesk-tutorial/i4-attribute.png)

7. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](./media/zendesk-tutorial/i2-attribute.png)

    ![图像](./media/zendesk-tutorial/i3-attribute.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。
    
    e. 在“源属性”列表中，键入为该行显示的属性值。
    
    f. 单击“确定”

    g. 单击“ **保存**”。

    > [!NOTE]
    > 可以使用扩展属性添加默认情况下不在 Azure AD 中的属性。 单击 [可在 SAML 中设置的用户属性](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)，获取 Zendesk 接受的 SAML 属性的完整列表。

8. 在 SAML 签名证书部分的“SAML 签名证书”部分，复制“指纹”，然后将其保存在你的计算机上。

    ![图像](./media/zendesk-tutorial/C3_certificate.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 如有必要，为“签名选项”选择相应的选项。

    b. 如有必要，为“签名算法”选择相应的选项。

    c. 单击“保存”

9. 在“设置 Zendesk”部分，单击“查看分步说明”以打开“配置登录”窗口。 从“快速参考”部分中复制以下 URL。

    请注意，URL 可能指明以下信息：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 SAML 单一登录服务 URL

    b. 实体 ID

    c. 注销 URL

    ![图像](./media/zendesk-tutorial/d1_saml.png) 

10. 可通过两种方式配置 Zendesk：自动和手动。
  
11. 若要在 Zendesk 中自动执行配置，需要通过单击“安装扩展”，安装“我的应用安全登录浏览器扩展”。

    ![图像](./media/zendesk-tutorial/install_extension.png)

12. 将扩展添加到浏览器后，单击“安装 Zendesk”会将你定向到 Zendesk 应用程序。 在此处，请提供管理员凭据以登录到 Zendesk。 浏览器扩展将自动为你配置应用程序并自动执行步骤 13。

     ![图像](./media/zendesk-tutorial/d2_saml.png) 

13. 如果你想要手动安装 Zendesk，打开新的 Web 浏览器窗口，以管理员身份登录到你的 Zendesk 公司网站，并执行以下步骤：

    * 单击“管理”。

    * 在左侧导航窗格中，单击“设置”，并单击“安全”。

    * 在“安全”页中，执行以下步骤：

      ![安全](././media/zendesk-tutorial/ic773089.png "安全")

      ![单一登录](././media/zendesk-tutorial/ic773090.png "单一登录")

      a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“管理员和代理”选项卡。

      b. 选择“单一登录 (SSO) 和 SAML”，并选择“SAML”。

      c. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的 SAML 单一登录服务 URL 值。

      d. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的注销 URL 值。

      e. 在“证书指纹”文本框中，粘贴从 Azure 门户复制的证书“指纹”值。

      f. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./media/zendesk-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/zendesk-tutorial/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![图像](./media/zendesk-tutorial/d_userproperties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="create-a-zendesk-test-user"></a>创建 Zendesk 测试用户

本部分的目的是在 Zendesk 中创建名为“Britta Simon”的用户。 Zendesk 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](Zendesk-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤：

> [!NOTE]
> 在登录时会自动预配“最终用户”帐户。 在登录前需要在 Zendesk 中手动预配“代理”和“管理员”帐户。

1. 登录到“Zendesk”租户。

2. 选择“客户列表”选项卡。

3. 选择“用户”选项卡，并单击“添加”。

    ![添加用户](././media/zendesk-tutorial/ic773632.png "添加用户")
4. 键入要预配的现有 Azure AD 帐户的**名称**和**电子邮件**，然后单击“保存”。

    ![新用户](././media/zendesk-tutorial/ic773633.png "新用户")

> [!NOTE]
> 可以使用 Zendesk 提供的任何其他 Zendesk 用户帐户创建工具或 API 来预配 AAD 用户帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Zendesk 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/zendesk-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“Zendesk”。

    ![图像](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/zendesk-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/zendesk-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Zendesk”磁贴时，会自动登录到 Zendesk 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
* [配置用户预配](zendesk-provisioning-tutorial.md)

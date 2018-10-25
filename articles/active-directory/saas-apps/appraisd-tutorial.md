---
title: 教程：Azure Active Directory 与 Appraisd 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Appraisd 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: jeedes
ms.openlocfilehash: dae9a59b89f03a50b1adaed55cd8f97c06906526
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49118071"
---
# <a name="tutorial-azure-active-directory-integration-with-appraisd"></a>教程：Azure Active Directory 与 Appraisd 集成

本教程介绍如何将 Appraisd 与 Azure Active Directory (Azure AD) 集成。

将 Appraisd 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Appraisd。
- 可让用户使用其 Azure AD 帐户自动登录到 Appraisd（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 Appraisd 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Appraisd 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Appraisd
2. 配置和测试 Azure AD 单一登录

## <a name="adding-appraisd-from-the-gallery"></a>从库中添加 Appraisd
要配置 Appraisd 与 Azure AD 的集成，需要将库中的 Appraisd 添加到托管 SaaS 应用列表。

**要从库中添加 Appraisd，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/appraisd-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/appraisd-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/appraisd-tutorial/a_new_app.png)

4. 在搜索框中，键入 Appraisd，在结果面板中选择“Appraisd”，然后单击“添加”按钮来添加应用程序。

     ![图像](./media/appraisd-tutorial/tutorial_appraisd_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Appraisd 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Appraisd 用户。 换句话说，需要建立 Azure AD 用户与 Appraisd 中相关用户之间的链接关系。

要配置和测试 Appraisd 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Appraisd 测试用户](#create-an-appraisd-test-user)** - 在 Appraisd 中创建与 Britta Simon 对应的用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Appraisd 应用程序中配置单一登录。

**要配置 Appraisd 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的 [Appraisd](https://portal.azure.com/) 应用程序集成页上，选择“单一登录”。

    ![图像](./media/appraisd-tutorial/B1_B2_Select_SSO.png)

2. 在“选择单一登录方法”对话框中，选择 SAML 模式以启用单一登录。

    ![图像](./media/appraisd-tutorial/b1_b2_saml_sso.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“基本 SAML 配置”对话框。

    ![图像](./media/appraisd-tutorial/b1-domains_and_urlsedit.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“设置其他 URL”。 

    b. 在“中继状态”文本框中，键入 URL：`<TENANTCODE>`

    c. 如果要在 SP 发起的模式下配置应用程序，请在“登录 URL”文本框中使用以下模式键入一个 URL：`https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > 在本教程稍后将介绍的“Appraisd SSO 配置”页面上，你获取了实际的登录 URL 和中继状态值。
    
5. Appraisd 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](./media/appraisd-tutorial/i3-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“编辑”按钮以打开“管理用户声明”对话框。

    ![图像](./media/appraisd-tutorial/i2-attribute.png)

    ![图像](./media/appraisd-tutorial/i4-attribute.png)

    b. 在“源属性”列表中，选择属性值。

    c. 单击“ **保存**”。 

7. 在“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将其保存到计算机上。

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_certficate.png) 

8. 在“设置 Appraisd”部分中，根据要求复制响应的 URL。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录 URL

    b. Azure Ad 标识符

    c. 注销 URL

    ![图像](./media/appraisd-tutorial/d1_samlsonfigure.png)

9. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Appraisd。

10. 在页面的右上角，单击“设置”图标，然后导航到“配置”。

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

11. 从菜单的左侧，单击“SAML 单一登录”。

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_single.png)

12. 在“SAML 2.0 单一登录配置”页面上，执行以下步骤：
    
    ![图像](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 复制“默认中继状态”值，并将其粘贴到 Azure 门户的“基本 SAML 配置”中的“中继状态”文本框。

    b. 复制“服务发起的登录 URL”值，并将其粘贴到 Azure 门户的“基本 SAML 配置”中的“登录 URL”文本框。

13. 在同一页面上向下滚动，在“标识用户”下执行以下步骤：

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“标识提供者单一登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值，然后单击“保存”。

    b. 在“标识提供者证书颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure Ad 标识符”值，然后单击“保存”。

    c. 在记事本中，打开从 Azure 门户下载的 base-64 编码证书，复制其内容，然后将其粘贴到“X.509 证书”框中并单击“保存”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./media/appraisd-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/appraisd-tutorial/d_adduser.png)

3. 在用户属性中执行以下步骤。

    ![图像](./media/appraisd-tutorial/d_userproperties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“姓名”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-an-appraisd-test-user"></a>创建 Appraisd 测试用户

必须将 Azure AD 用户预配到 Appraisd 中，然后他们才能登录到 Appraisd。 在 Appraisd 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录到 Appraisd。

2. 在页面的右上角，单击“设置”图标，然后导航到“管理中心”。

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. 在页面顶部的工具栏中，单击“人员”，再导航到“添加新用户”。

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. 在“添加新用户”页面上，执行以下步骤：

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名字”文本框中，输入用户的名字，例如 Britta。

    b. 在“姓氏”文本框中，输入用户的姓氏，例如 simon。

    c. 在“电子邮件”文本框中，输入用户的电子邮件，例如 **Brittasimon@contoso.com**。

    d. 单击“添加用户”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Appraisd 的访问权限支持她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/appraisd-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择 Appraisd。

    ![图像](./media/appraisd-tutorial/tutorial_appraisd_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/appraisd-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/appraisd-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Appraisd 磁贴时，应该会自动登录 Appraisd 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

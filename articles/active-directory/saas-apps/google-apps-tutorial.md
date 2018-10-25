---
title: 教程：Azure Active Directory 与 G Suite 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 G Suite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 6974350c6abbc6c5f5a8e10b22e91796e2564b08
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268053"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>教程：Azure Active Directory 与 G Suite 集成

本教程介绍如何将 G Suite 与 Azure Active Directory (Azure AD) 集成。

将 G Suite 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 G Suite。
- 可让用户使用其 Azure AD 帐户自动登录到 G Suite（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 G Suite 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 G Suite 单一登录的订阅
- Google Apps 订阅或 Google Cloud Platform 订阅。

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="frequently-asked-questions"></a>常见问题

1.  **问：此集成是否支持 Google Cloud Platform SSO 与 Azure AD 的集成？**
    
    答：是的。 Google Cloud Platform 和 Google Apps 共用同一个身份验证平台。 因此，若要实现 GCP 集成，需要配置 Google Apps 的 SSO。


1. **问：Chromebook 和其他 Chrome 设备是否与 Azure AD 单一登录兼容？**
   
    答：是的，用户能够使用 Azure AD 凭据登录到 Chromebook 设备。 若要了解为何有时用户会两次收到输入凭据的提示，请参阅此 [G Suite 支持文章](https://support.google.com/chrome/a/answer/6060880)。

1. **问：如果我启用单一登录，用户能够使用他们的 Azure AD 凭据登录到任何 Google 产品（例如 Google Classroom、GMail、Google Drive、YouTube 等）吗？**
   
    答：可以，具体取决于选择为组织启用或禁用的 [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)。

1. **问：能否仅为 G Suite 用户子集启用单一登录？**
   
    答：不可以，启用单一登录会立即要求所有 G Suite 用户使用其 Azure AD 凭据进行身份验证。 由于 G Suite 不支持具有多个标识提供者，因此，G Suite 环境的标识提供者可以是 Azure AD 或 Google - 但不能同时为两者。

1. **问：如果用户通过 Windows 登录，他们是否会自动进行 G Suite 身份验证而不会收到输入密码的提示？**
   
    答：有两种用于启用此方案的选项。 第一种，用户可通过 [Azure Active Directory Join](../device-management-introduction.md) 登录到 Windows 10 设备。 或者，用户可以登录到通过域加入的方式加入到一个本地 Active Directory（已通加 [Active Directory 联合身份验证服务 (AD FS)](../hybrid/plan-connect-user-signin.md) 部署启用 Azure AD 单一登录）的 Windows 设备。 两种选项都要求执行以下教程中的步骤，以在 Azure AD 和 G Suite 之间启用单一登录。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 G Suite
1. 配置和测试 Azure AD 单一登录

## <a name="adding-g-suite-from-the-gallery"></a>从库添加 G Suite

若要配置 G Suite 与 Azure AD 的集成，需要从库中将 G Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 G Suite，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/google-apps-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/google-apps-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/google-apps-tutorial/a_new_app.png)

4. 在搜索框中键入“G Suite”，在结果面板中选择“G Suite”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/google-apps-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 G Suite 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 G Suite 用户。 换句话说，需要建立 Azure AD 用户与 G Suite 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 G Suite 中“用户名”的值，建立此链接关系。

若要配置和测试 G Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 G Suite 测试用户](#create-a-g-suite-test-user)** - 在 G Suite 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 G Suite 应用程序中配置单一登录。

**若要配置 G Suite 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)中的“G Suite”应用程序集成页上，选择“单一登录”。

    ![图像](./media/google-apps-tutorial/b1_b2_select_sso.png)

2. 单击屏幕顶部的“更改单一登录模式”，以选择“SAML”模式。

      ![图像](./media/google-apps-tutorial/b1_b2_saml_ssso.png)

3. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![图像](./media/google-apps-tutorial/b1_b2_saml_sso.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![图像](./media/google-apps-tutorial/b1-domains_and_urlsedit.png)

5. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL： 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    ![图像](./media/google-apps-tutorial/b1-domains_and_urls.png)
 
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [G Suite 客户端支持团队](https://www.google.com/contact/)获取这些值。

6. G Suite 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“用户属性”对话框。

    ![图像](./media/google-apps-tutorial/i3-attribute.png)

7. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并按照以下步骤操作：
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“编辑”按钮，以打开“管理用户声明”对话框。

    ![图像](./media/google-apps-tutorial/i2-attribute.png)

    ![图像](./media/google-apps-tutorial/i4-attribute.png)

    b. 在“源属性”列表中，选择属性值。

    c. 单击“ **保存**”。

8. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“下载”，以根据要求下载相应的证书，并将证书保存在计算机上。

    ![图像](./media/google-apps-tutorial/certificatebase64.png)

9. 在“设置 G Suite”部分中，根据要求复制相应的 URL。

    请注意，URL 可能指明以下信息：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![图像](./media/google-apps-tutorial/d1_saml.png) 

10. 在浏览器中打开新选项卡并使用管理员帐户登录到 [G Suite 管理员控制台](http://admin.google.com/)。

11. 单击“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。
   
    ![单击“安全”。][10]

12. 在“安全”页上单击“设置单一登录 (SSO)”。
   
    ![单击“SSO”。][11]

13. 执行以下配置更改：
   
    ![配置 SSO][12]
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“使用第三方标识提供者设置 SSO”。

    b. 在 G Suite 中的“登录页 URL”字段中，粘贴从 Azure 门户复制的“单一登录服务 URL”的值。

    c. 在 G Suite 中的“注销页 URL”字段中，粘贴从 Azure 门户复制的“注销 URL”的值。 

    d. 在 G Suite 中的“更改密码 URL”字段中，粘贴从 Azure 门户复制的“更改密码 URL”的值。 

    e. 在 G Suite 中，为“验证证书”上传从 Azure 门户下载的证书。

    f. 选择“使用域特定的颁发者”。

    g. 单击“保存更改”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./media/google-apps-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/google-apps-tutorial/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![图像](./media/google-apps-tutorial/d_userproperties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入“BrittaSimon”。
  
    b. 在“用户名”字段中，键入“brittasimon@yourcompanydomain.extension”  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，选中“显示密码”复选框，再记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-g-suite-test-user"></a>创建 G Suite 测试用户

本部分的目的是在 G Suite 中创建名为“Britta Simon”的用户。 G Suite 支持在默认情况下启用的自动预配。 在此部分中无需进行任何操作。 尝试访问 G Suite 时，如果 G Suite 中没有用户，系统会创建一个新用户。

>[!NOTE]
>如果在测试单一登录前尚未启用 Azure AD 中的预配，请确保用户已存在于 G Suite 中。

>[!NOTE] 
>如果需要手动创建用户，请联系 [Google 支持团队](https://www.google.com/contact/)。



### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 G Suite 的访问权限，使其可以使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”和“所有应用程序”。

    ![图像](./media/google-apps-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“G Suite”。

    ![图像](./media/google-apps-tutorial/d_all_proapplications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/google-apps-tutorial/d_leftpaneusers.png)

4. 依次选择“添加”按钮和“添加分配”对话框中的“用户和组”。

    ![图像](./media/google-apps-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”，再单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“G Suite”磁贴时，应会自动登录到 G Suite 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png


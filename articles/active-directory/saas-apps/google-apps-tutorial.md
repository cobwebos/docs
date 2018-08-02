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
ms.date: 04/02/2018
ms.author: jeedes
ms.openlocfilehash: c8ec2b8e312b9bedbd19cb203c0a59177c7fd6a5
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265006"
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


2. **问：Chromebook 和其他 Chrome 设备是否与 Azure AD 单一登录兼容？**
   
    答：是的，用户能够使用 Azure AD 凭据登录到 Chromebook 设备。 若要了解为何有时用户会两次收到输入凭据的提示，请参阅此 [G Suite 支持文章](https://support.google.com/chrome/a/answer/6060880)。

3. **问：如果我启用单一登录，用户能够使用他们的 Azure AD 凭据登录到任何 Google 产品（例如 Google Classroom、GMail、Google Drive、YouTube 等）吗？**
   
    答：可以，具体取决于选择为组织启用或禁用的 [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)。

4. **问：能否仅为 G Suite 用户子集启用单一登录？**
   
    答：不可以，启用单一登录会立即要求所有 G Suite 用户使用其 Azure AD 凭据进行身份验证。 由于 G Suite 不支持具有多个标识提供者，因此，G Suite 环境的标识提供者可以是 Azure AD 或 Google - 但不能同时为两者。

5. **问：如果用户通过 Windows 登录，他们是否会自动进行 G Suite 身份验证而不会收到输入密码的提示？**
   
    答：有两种用于启用此方案的选项。 第一种，用户可通过 [Azure Active Directory Join](../device-management-introduction.md) 登录到 Windows 10 设备。 或者，用户可以登录到通过域加入的方式加入到一个本地 Active Directory（已通加 [Active Directory 联合身份验证服务 (AD FS)](../connect/active-directory-aadconnect-user-signin.md) 部署启用 Azure AD 单一登录）的 Windows 设备。 两种选项都要求执行以下教程中的步骤，以在 Azure AD 和 G Suite 之间启用单一登录。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 G Suite
2. 配置和测试 Azure AD 单一登录

## <a name="adding-g-suite-from-the-gallery"></a>从库添加 G Suite
若要配置 G Suite 与 Azure AD 的集成，需要从库中将 G Suite 添加到托管 SaaS 应用列表。

**若要从库中添加 G Suite，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“G Suite”，在结果面板中选择“G Suite”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 G Suite](./media/google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 G Suite 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 G Suite 用户。 换句话说，需要建立 Azure AD 用户与 G Suite 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 G Suite 中“用户名”的值，建立此链接关系。

若要配置和测试 G Suite 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 G Suite 测试用户](#create-a-g-suite-test-user)** - 在 G Suite 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 G Suite 应用程序中配置单一登录。

**若要配置 G Suite 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **G Suite** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. 在“G Suite 域和 URL”部分，如果想要为 **Gmail** 进行配置，请执行以下步骤：

    ![G Suite 域和 URL 单一登录信息](./media/google-apps-tutorial/tutorial_googleapps_urlgmail.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL： 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
 
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [G Suite 客户端支持团队](https://www.google.com/contact/)获取这些值。

4. 在“G Suite 域和 URL”部分，如果想要为 **Google Cloud Platform** 进行配置，请执行以下步骤：

    ![G Suite 域和 URL 单一登录信息](./media/google-apps-tutorial/tutorial_googleapps_url1.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. 在“标识符”文本框中，使用以下模式键入 URL： 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [G Suite 客户端支持团队](https://www.google.com/contact/)获取这些值。 

5. 在“SAML 签名证书”部分中单击“证书”，然后在计算机上保存证书文件。

    ![证书下载链接](./media/google-apps-tutorial/tutorial_googleapps_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/google-apps-tutorial/tutorial_general_400.png)

7. 在“G Suite 配置”部分，单击“配置 G Suite”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL、SAML 单一登录服务 URL 和更改密码 URL”。

    ![G Suite 配置](./media/google-apps-tutorial/tutorial_googleapps_configure.png) 

8. 在浏览器中打开新选项卡并使用管理员帐户登录到 [G Suite 管理员控制台](http://admin.google.com/)。

9. 单击“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。
   
    ![单击“安全”。][10]

10. 在“安全”页上单击“设置单一登录 (SSO)”。
   
    ![单击“SSO”。][11]

11. 执行以下配置更改：
   
    ![配置 SSO][12]
   
    a. 选择“使用第三方标识提供者设置 SSO”。

    b. 在 G Suite 中的“登录页 URL”字段中，粘贴从 Azure 门户复制的“单一登录服务 URL”的值。

    c. 在 G Suite 中的“注销页 URL”字段中，粘贴从 Azure 门户复制的“注销 URL”的值。 

    d. 在 G Suite 中的“更改密码 URL”字段中，粘贴从 Azure 门户复制的“更改密码 URL”的值。 

    e. 在 G Suite 中，为“验证证书”上传从 Azure 门户下载的证书。

    f. 选择“使用域特定的颁发者”。

    g. 单击“保存更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/google-apps-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/google-apps-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/google-apps-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/google-apps-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-g-suite-test-user"></a>创建 G Suite 测试用户

本部分的目的是在 G Suite 中创建名为“Britta Simon”的用户。 G Suite 支持在默认情况下启用的自动预配。 在此部分中无需进行任何操作。 尝试访问 G Suite 时，如果 G Suite 中没有用户，系统会创建一个新用户。

>[!NOTE] 
>如果需要手动创建用户，请联系 [Google 支持团队](https://www.google.com/contact/)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 G Suite 的访问权限，使其可以使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 G Suite，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“G Suite”。

    ![应用程序列表中的 G Suite 链接](./media/google-apps-tutorial/tutorial_googleapps_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“G Suite”磁贴时，应会自动登录到 G Suite 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/googleapps-tutorial/tutorial_general_01.png
[2]: ./media/googleapps-tutorial/tutorial_general_02.png
[3]: ./media/googleapps-tutorial/tutorial_general_03.png
[4]: ./media/googleapps-tutorial/tutorial_general_04.png

[100]: ./media/googleapps-tutorial/tutorial_general_100.png

[200]: ./media/googleapps-tutorial/tutorial_general_200.png
[201]: ./media/googleapps-tutorial/tutorial_general_201.png
[202]: ./media/googleapps-tutorial/tutorial_general_202.png
[203]: ./media/googleapps-tutorial/tutorial_general_203.png
[10]: ./media/googleapps-tutorial/gapps-security.png
[11]: ./media/googleapps-tutorial/security-gapps.png
[12]: ./media/googleapps-tutorial/gapps-sso-config.png


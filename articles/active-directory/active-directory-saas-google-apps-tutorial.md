---
title: "教程：Azure Active Directory 与 Azure 中的 Google Apps 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Google Apps 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 065841d6b4fe50e953f01bba4d3f23de82b82726
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>教程：Azure Active Directory 与 Google Apps 集成

本教程介绍如何将 Google Apps 与 Azure Active Directory (Azure AD) 集成。

将 Google Apps 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Google Apps
- 可以让用户使用其 Azure AD 帐户自动登录到 Google Apps（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

若要深入了解 SaaS 应用与 Azure AD 集成，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Google Apps 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Google Apps 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可在此处获取一个月的试用版：[试用产品/服务](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="video-tutorial"></a>视频教程
如何在 2 分钟内启用 Google Apps 单一登录：

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>常见问题
1. **问：Chromebook 和其他 Chrome 设备是否与 Azure AD 单一登录兼容？**
   
    答：是的，用户能够使用 Azure AD 凭据登录到 Chromebook 设备。 若要了解为何有时用户会两次收到输入凭据的提示，请参阅此 [Google Apps 支持文章](https://support.google.com/chrome/a/answer/6060880)。

2. **问：如果我启用单一登录，用户能够使用他们的 Azure AD 凭据登录到任何 Google 产品（例如 Google Classroom、GMail、Google Drive、YouTube 等）吗？**
   
    答：可以，具体取决于选择为组织启用或禁用的 [Google 应用](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)。

3. **问：能否仅为 Google Apps 用户子集启用单一登录？**
   
    答：不可以，启用单一登录会立即要求所有 Google Apps 用户使用其 Azure AD 凭据进行身份验证。 由于 Google Apps 不支持具有多个标识提供者，因此，Google Apps 环境的标识提供者可以是 Azure AD 或 Google - 但不能同时为两者。

4. **问：如果用户通过 Windows 登录，他们是否会自动进行 Google Apps 身份验证而不会收到输入密码的提示？**
   
    答：有两种用于启用此方案的选项。 第一种，用户可通过 [Azure Active Directory Join](active-directory-azureadjoin-overview.md) 登录到 Windows 10 设备。 或者，用户可以登录到通过域加入的方式加入到一个本地 Active Directory（已通加 [Active Directory 联合身份验证服务 (AD FS)](active-directory-aadconnect-user-signin.md) 部署启用 Azure AD 单一登录）的 Windows 设备。 两种选项都要求执行以下教程中的步骤，以在 Azure AD 和 Google Apps 之间启用单一登录。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Google Apps
2. 配置和测试 Azure AD 单一登录

## <a name="adding-google-apps-from-the-gallery"></a>从库中添加 Google Apps
要配置 Google Apps 与 Azure AD 的集成，需要从库中将 Google Apps 添加到托管 SaaS 应用列表。

若要从库添加 Google Apps，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在“搜索”框中键入“Google Apps”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_search.png)

5. 在结果面板中，选择“Google Apps”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Google Apps 配置和测试 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Google Apps 用户。 换句话说，需要建立 Azure AD 用户与 Google Apps 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 Google Apps 中“用户名”的值来建立此关联关系。

若要配置并测试 Google Apps 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Google Apps 测试用户](#creating-a-google-apps-test-user) - 在 Google Apps 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 Google Apps 应用程序中配置单一登录。

若要配置 Google Apps 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中，在“Google Apps”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. 在“Google Apps 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://mail.google.com/a/<yourdomain>`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Google 支持团队](https://www.google.com/contact/)。
 
4. 在“SAML 签名证书”部分中，单击“证书”，并将证书保存在计算机上。

    ![配置单一登录](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-google-apps-tutorial/tutorial_general_400.png)

6. 在“Google Apps 配置”部分中，单击“配置 Google Apps”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL、SAML 单一登录服务 URL 和更改密码 URL”。

    ![配置单一登录](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_configure.png) 

7. 在浏览器中打开新选项卡并使用管理员帐户登录到 [Google Apps 管理员控制台](http://admin.google.com/)。

8. 单击“安全”。 如果没有看到该链接，它可能被隐藏在屏幕底部的“其他控件”菜单下。
   
    ![单击“安全”。][10]

9. 在“安全”页上单击“设置单一登录 (SSO)”。
   
    ![单击“SSO”。][11]

10. 执行以下配置更改：
   
    ![配置 SSO][12]
   
    a. 选择“使用第三方标识提供者设置 SSO”。

    b. 在 Google Apps 中的“登录页 URL”字段中，粘贴从 Azure 门户复制的“单一登录服务 URL”值。

    c. 在 Google Apps 中的“注销页 URL”字段中，粘贴从 Azure 门户复制的“注销 URL”值。 

    d. 在 Google Apps 中的“更改密码 URL”字段中，粘贴从 Azure 门户复制的“更改密码 URL”值。 

    e. 在 Google Apps 中，为“验证证书”上传从 Azure 门户下载的证书。

    f. 单击“保存更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-google-apps-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-google-apps-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-google-apps-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-google-apps-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-google-apps-test-user"></a>创建 Google Apps 测试用户

本部分的目的是在 Google Apps Software 中创建名为“Britta Simon”的用户。 Google Apps 支持在默认情况下启用的自动预配。 在此部分中无需进行任何操作。 尝试访问 Google Apps Software 时，如果 Google Apps Software 中没有用户，系统会创建一个新用户。

>[!NOTE] 
>如果需要手动创建用户，请联系 [Google 支持团队](https://www.google.com/contact/)。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Google Apps 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Google Apps，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Google Apps”。

    ![配置单一登录](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，要测试单一登录设置，打开“访问面板”（网址为 [https://myapps.microsoft.com](active-directory-saas-access-panel-introduction.md)），登录到测试帐户，并单击访问面板中的“Google Apps”磁贴。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)
* [配置用户预配](active-directory-saas-google-apps-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_203.png

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png

[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png
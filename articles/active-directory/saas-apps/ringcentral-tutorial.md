---
title: 教程：Azure Active Directory 与 RingCentral 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 RingCentral 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9cd97bc226ec69441b933a9f7bf3caec17f1478
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877116"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>教程：Azure Active Directory 与 RingCentral 的集成

本教程介绍如何将 RingCentral 与 Azure Active Directory (Azure AD) 集成。

将 RingCentral 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 RingCentral。
- 可以让用户使用其 Azure AD 帐户自动登录到 RingCentral（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必备组件

若要配置 Azure AD 与 RingCentral 的集成，需备齐以下项目：

- Azure AD 订阅
- 启用了 RingCentral 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 RingCentral
2. 配置和测试 Azure AD 单一登录

## <a name="adding-ringcentral-from-the-gallery"></a>从库中添加 RingCentral
若要配置 RingCentral 与 Azure AD 的集成，需要从库中将 RingCentral 添加到托管 SaaS 应用列表。

**若要从库中添加 RingCentral，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/ringcentral-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/ringcentral-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/ringcentral-tutorial/a_new_app.png)

4. 在搜索框中键入“RingCentral”，在结果面板中选择“RingCentral”，然后单击“添加”按钮以添加该应用程序。

     ![图像](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，请根据名为“Britta Simon”的测试用户的情况配置和测试 RingCentral 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 RingCentral 用户。 换句话说，需要建立 Azure AD 用户与 RingCentral 中相关用户之间的关联关系。

若要配置并测试 RingCentral 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 RingCentral 测试用户](#create-a-ringcentral-test-user)** - 在 RingCentral 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，请在 Azure 门户中启用 Azure AD 单一登录并在 RingCentral 应用程序中配置单一登录。

**若要配置 RingCentral 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)中的 **RingCentral** 应用程序集成页上，选择“单一登录”。

    ![图像](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. 单击屏幕顶部的“更改单一登录模式”，以选择“SAML”模式。

      ![图像](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. 在“选择单一登录方法”对话框中，选择 SAML 模式以启用单一登录。

    ![图像](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![图像](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. 在“基本 SAML 配置”部分，如果有**服务提供程序元数据文件**，请执行以下步骤：

    a. 单击“上传元数据文件”。

    ![图像](./media/ringcentral-tutorial/b9_saml.png)

    b. 单击“文件夹徽标”来选择元数据文件并单击“上传”。

    ![图像](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. 成功将元数据文件上传后，**标识符**和**回复 URL** 值会自动填充在“基本 SAML 配置”部分的文本框中，如下所示：

    ![图像](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. 在“登录 URL”文本框中，键入 URL：

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > 请在本教程稍后将介绍的“RingCentral SSO 配置”页面上获取**服务提供程序元数据文件**。

6. 如果您尚未**服务提供程序元数据文件**，执行以下步骤：

    a. 在“登录 URL”文本框中，键入 URL：

    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. 在“标识符”文本框中，键入一个 URL：

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. 在“回复 URL”文本框中键入 URL：
    
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![图像](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的证书并将其保存在计算机上。

    ![图像](./media/ringcentral-tutorial/certificatebase64.png)
    
8. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 RingCentral。

9. 在顶部单击“工具”。

    ![图像](./media/ringcentral-tutorial/ringcentral1.png)

10. 导航到“单一登录”。

    ![图像](./media/ringcentral-tutorial/ringcentral2.png)

11. 在“单一登录”页的“SSO 配置”部分，在“步骤 1”中单击“编辑”，执行以下步骤：

    ![图像](./media/ringcentral-tutorial/ringcentral3.png)

12. 在“设置单一登录”页上，执行以下步骤：

    ![图像](./media/ringcentral-tutorial/ringcentral4.png)

    a. 单击“浏览”，上传从 Azure 门户下载的元数据文件。

    b. 上传元数据以后，值会自动填充在“SSO 常规信息”部分中。

    c. 在“属性映射”部分，针对“将电子邮件属性映射到”选项选择 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. 单击“ **保存**”。

    e. 从**步骤 2**单击**下载**若要下载**服务提供程序元数据文件**并在其上传**基本 SAML 配置**部分若要将自动填充**标识符**并**回复 URL**在 Azure 门户中的值。

    ![图像](./media/ringcentral-tutorial/ringcentral6.png) 

    f. 在同一页导航到“启用 SSO”部分，执行以下步骤：

    ![图像](./media/ringcentral-tutorial/ringcentral5.png)

    a. 选择“启用 SSO 服务”。
    
    b. 选择“允许用户使用 SSO 或 RingCentral 凭据登录”。

    c. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./media/ringcentral-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/ringcentral-tutorial/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![图像](./media/ringcentral-tutorial/d_userproperties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在中**用户名**字段中，键入**brittasimon\@yourcompanydomain.extension**  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-ringcentral-test-user"></a>创建 RingCentral 测试用户

在本部分，请在 RingCentral 中创建一个名为“Britta Simon”的用户。 请与 [RingCentral 客户端支持团队](https://success.ringcentral.com/RCContactSupp)协作，在 RingCentral 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 RingCentral 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/ringcentral-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“RingCentral”。

    ![图像](./media/ringcentral-tutorial/d_all_proapplications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/ringcentral-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“RingCentral”磁贴时，会自动登录到 RingCentral 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png


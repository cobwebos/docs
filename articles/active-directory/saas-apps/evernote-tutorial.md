---
title: 教程：Azure Active Directory 与 Evernote 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 Evernote 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 7a9282f5418737b583e29d99893df3fc81f52955
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442626"
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>教程：Azure Active Directory 与 Evernote 的集成

本教程介绍如何将 Evernote 与 Azure Active Directory (Azure AD) 集成。

将 Evernote 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Evernote。
- 可让用户使用其 Azure AD 帐户自动登录到 Evernote（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Evernote 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Evernote 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Evernote
1. 配置和测试 Azure AD 单一登录

## <a name="adding-evernote-from-the-gallery"></a>从库中添加 Evernote
要配置 Evernote 与 Azure AD 的集成，需要将库中的 Evernote 添加到托管的 SaaS 应用列表。

**若要从库中添加 Evernote，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中键入“Evernote”，在结果面板中选择“Evernote”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Evernote](./media/evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Evernote 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Evernote 用户。 换句话说，需要建立 Azure AD 用户与 Evernote 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Evernote 中“用户名”的值，建立此链接关系。

若要通过 Evernote 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Evernote 测试用户](#create-an-evernote-test-user)** - 在 Evernote 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Evernote 应用程序中配置单一登录。

**若要配置 Evernote 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Evernote 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/evernote-tutorial/tutorial_evernote_samlbase.png)

1. 在“Evernote 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![Evernote 域和 URL 单一登录信息](./media/evernote-tutorial/tutorial_evernote_url.png)

    在“标识符”文本框中，键入 URL：`https://www.evernote.com/saml2`

1. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Evernote 域和 URL 单一登录信息](./media/evernote-tutorial/tutorial_evernote_url1.png)

    在“登录 URL”文本框中，键入 URL： `https://www.evernote.com/Login.action`   

1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/evernote-tutorial/tutorial_evernote_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/evernote-tutorial/tutorial_general_400.png)

1. 在“Evernote 配置”部分，单击“配置 Evernote”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![Evernote 配置](./media/evernote-tutorial/tutorial_evernote_configure.png) 

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Evernote 公司站点。

1. 转到“管理控制台”

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. 在“管理控制台”中，转到“安全性”并选择“单一登录”

    ![SSO-Setting](./media/evernote-tutorial/tutorial_evernote_sso.png)

1. 配置以下值：

    ![Certificate-Setting](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **启用 SSO：** SSO 默认已启用（单击“禁用单一登录”可以取消 SSO 要求）

    b. 在“SAML HTTP 请求 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 在记事本中打开从 Azure AD 下载的证书，并复制包括“BEGIN CERTIFICATE”和“END CERTIFICATE”在内的内容并将其粘贴到“X.509 证书”文本框中。 

    d. 单击“保存更改”

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/evernote-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/evernote-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/evernote-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/evernote-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-evernote-test-user"></a>创建 Evernote 测试用户

为了使 Azure AD 用户能够登录到 Evernote，必须将其预配到 Evernote 中。  
对于 Evernote，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Evernote 公司站点。

1. 单击“管理控制台”。

    ![Admin-Console](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

1. 在“管理控制台”中，转到“添加用户”。

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

1. 在“电子邮件”文本框中的“添加团队成员”内，键入用户帐户的电子邮件地址，并单击“邀请”。

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
1. 发送邀请后，Azure Active Directory 帐户持有人将收到邀请电子邮件。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予 Evernote 的访问权限使之能够使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Evernote，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Evernote”。

    ![应用程序列表中的 Evernote 链接](./media/evernote-tutorial/tutorial_evernote_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Evernote 磁贴时，应会登录到 Evernote 应用程序。 一开始以组织帐户登录，但随后需要使用个人帐户登录。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/evernote-tutorial/tutorial_general_01.png
[2]: ./media/evernote-tutorial/tutorial_general_02.png
[3]: ./media/evernote-tutorial/tutorial_general_03.png
[4]: ./media/evernote-tutorial/tutorial_general_04.png

[100]: ./media/evernote-tutorial/tutorial_general_100.png

[200]: ./media/evernote-tutorial/tutorial_general_200.png
[201]: ./media/evernote-tutorial/tutorial_general_201.png
[202]: ./media/evernote-tutorial/tutorial_general_202.png
[203]: ./media/evernote-tutorial/tutorial_general_203.png


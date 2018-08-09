---
title: 教程：Azure Active Directory 与 Clear Review 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Clear Review 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: 604a557a91176c08a361ffd058adda63f53b30fc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433014"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>教程：Azure Active Directory 与 Clear Review 的集成

在本教程中，了解如何将 Clear Review 与 Azure Active Directory (Azure AD) 集成。

将 Clear Review 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Clear Review。
- 可以让用户通过其 Azure AD 帐户自动登录到 Clear Review（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Clear Review 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 Clear Review 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Clear Review
1. 配置和测试 Azure AD 单一登录

## <a name="adding-clear-review-from-the-gallery"></a>从库添加 Clear Review
若要配置 Clear Review 与 Azure AD 的集成，需要将库中的 Clear Review 添加到托管的 SaaS 应用列表。

**若要从库中添加 Clear Review，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“Clear Review”，在结果面板中选择“Clear Review”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Clear Review](./media/clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Clear Review 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Clear Review 用户。 换句话说，需要建立 Azure AD 用户与 Clear Review 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 Clear Review 中“用户名”的值来建立此链接关系。

若要配置和测试 Clear Review 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Clear Review 测试用户](#create-a-clear-review-test-user)** - 在 Clear Review 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 Clear Review 应用程序中配置单一登录。

**若要配置 Clear Review 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Clear Review** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/clearreview-tutorial/tutorial_clearreview_samlbase.png)

1. 在“Clear Review 域和 URL”部分中，如果要在 **IdP 发起的**模式下配置应用程序，请执行以下步骤：

    ![Clear Review 域和 URL 单一登录信息](./media/clearreview-tutorial/tutorial_clearreview_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<customer name>.clearreview.com/sso/metadata/`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<customer name>.clearreview.com/sso/acs/`

1. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Clear Review 域和 URL 单一登录信息](./media/clearreview-tutorial/tutorial_clearreview_url_sp.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 若要获取这些值，请联系 [Clear Review 支持团队](https://clearreview.com/contact/)。

1. Clear Review 应用程序预期名称标识符声明中的用户标识符是唯一的。 应将用户标识符值映射到 **user.mail**。

    ![“属性”部分](./media/clearreview-tutorial/attribute.png)


1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/clearreview-tutorial/tutorial_clearreview_certificate.png)

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/clearreview-tutorial/tutorial_general_400.png)

1. 在“Clear Review 配置”部分中，单击“配置 Clear Review”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![Clear Review 配置](./media/clearreview-tutorial/tutorial_clearreview_configure.png) 

1. 若要在 **Clear Review** 端配置单一登录，请使用管理员凭据打开 **Clear Review** 门户。

1. 从左侧导航窗格中选择“管理”。

    ![配置单一登录“保存”按钮](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

1. 在页面底部选择“更改”。

    ![配置单一登录“保存”按钮](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

1. 在“单一登录设置”页上，执行以下步骤：

    ![配置单一登录“保存”按钮](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. 将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“颁发者 URL”文本框中。

    b. 在“SAML 终结点”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。    

    c. 在“SLO 终结点”文本框中，粘贴从 Azure 门户复制的“登录服务 URL”值。 

    d. 在记事本中打开下载的证书，将其内容粘贴到“X.509 证书”文本框中。   

1. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/clearreview-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/clearreview-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/clearreview-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/clearreview-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
  
### <a name="create-a-clear-review-test-user"></a>创建 Clear Review 测试用户

在本部分中，在 Clear Review 中创建名为 Britta Simon 的用户。 请与 [Clear Review 支持团队](https://clearreview.com/contact/)协作，在 Clear Review 平台中添加用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Clear Review 的权限，以支持其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Clear Review，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Clear Review”。

    ![应用程序列表中的 Clear Review 链接](./media/clearreview-tutorial/tutorial_clearreview_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Clear Review 磁贴时，应会自动登录到 Clear Review 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clearreview-tutorial/tutorial_general_01.png
[2]: ./media/clearreview-tutorial/tutorial_general_02.png
[3]: ./media/clearreview-tutorial/tutorial_general_03.png
[4]: ./media/clearreview-tutorial/tutorial_general_04.png

[100]: ./media/clearreview-tutorial/tutorial_general_100.png

[200]: ./media/clearreview-tutorial/tutorial_general_200.png
[201]: ./media/clearreview-tutorial/tutorial_general_201.png
[202]: ./media/clearreview-tutorial/tutorial_general_202.png
[203]: ./media/clearreview-tutorial/tutorial_general_203.png

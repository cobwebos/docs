---
title: 教程：Azure Active Directory 与 iQualify LMS 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 iQualify LMS 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: d1161480bfd7a4cfeeb81f02234586a515fdffed
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446128"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>教程：Azure Active Directory 与 iQualify LMS 集成

本教程介绍如何将 iQualify LMS 与 Azure Active Directory (Azure AD) 集成。

将 iQualify LMS 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 iQualify LMS。
- 可以让用户使用其 Azure AD 帐户自动登录到 iQualify LMS（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 iQualify LMS 的集成，需要以下项：

- Azure AD 订阅
- 已启用 iQualify LMS 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 iQualify LMS
1. 配置和测试 Azure AD 单一登录

## <a name="adding-iqualify-lms-from-the-gallery"></a>从库中添加 iQualify LMS
要配置 iQualify LMS 与 Azure AD 的集成，需要将库中的 iQualify LMS 添加到托管的 SaaS 应用列表。

**若要从库中添加 iQualify LMS，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“iQualify LMS”，在结果面板中选择“iQualify LMS”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 iQualify LMS](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 iQualify LMS 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 iQualify LMS 用户。 换句话说，需要建立 Azure AD 用户与 iQualify LMS 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 iQualify LMS 中“用户名”的值来建立此链接关系。

若要配置和测试 iQualify LMS 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. [创建 iQualify LMS 测试用户](#create-an-iqualify-lms-test-user) - 在 iQualify LMS 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 iQualify LMS 应用程序中配置单一登录。

**若要配置 iQualify LMS 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 iQualify LMS 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

1. 在“iQualify MS 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![iQualify LMS 域和 URL 单一登录信息](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL： 
    | |
    |--|--|
    | 生产环境：`https://<yourorg>.iqualify.com/`|
    | 测试环境：`https://<yourorg>.iqualify.io`|
    
    b. 在“回复 URL”文本框中，使用以下模式键入 URL： 
    | |
    |--|--|
    | 生产环境：`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | 测试环境：`https://<yourorg>.iqualify.io/auth/saml2/callback` |

1. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![iQualify LMS 域和 URL 单一登录信息](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：
    | |
    |--|--|
    | 生产环境：`https://<yourorg>.iqualify.com/login` |
    | 测试环境：`https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [iQualify LMS 客户端支持团队](https://www.iqualify.com)获取这些值。 

1. iQualify LMS 应用程序要求以特定的格式显示安全断言标记语言 (SAML) 断言。 在 iQualify 应用程序集成页的“用户属性”部分中配置声明并管理这些属性的值，如以下屏幕截图中所示：
    
    ![配置单一登录](./media/iqualify-tutorial/atb.png)

1. 在“单一登录”对话框的“用户属性”部分，针对下表中所示的每一行，执行以下步骤：
    
    | 属性名称 | 属性值 |
    | --- | --- |    
    | 电子邮件 | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "your attribute" | 

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/iqualify-tutorial/atb2.png)

    ![配置单一登录](./media/iqualify-tutorial/atb3.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定”

    e. 针对后面的表行重复步骤“a”到“d”。 

    > [!Note]
    > 为 person_id 属性重复步骤“a”到“d”，此操作为可选操作

1. 在“SAML 签名证书”部分中，单击“证书 (Base64)”，然后在计算机上保存证书文件。

    ![证书下载链接](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/iqualify-tutorial/tutorial_general_400.png)
    
1. 在“iQualify LMS 配置”部分，单击“配置 iQualify LMS”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![iQualify LMS 配置](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

1.  打开一个新的浏览器窗口，并以管理员身份登录到 iQualify 环境。

1. 登录后，单击右上角的头像，然后单击“帐户设置”。

    ![帐户设置](./media/iqualify-tutorial/setting1.png) 
1. 在帐户设置区域，单击左侧的功能区菜单，然后单击“集成”
    
    ![集成](./media/iqualify-tutorial/setting2.png)

1. 在“集成”下，单击“SAML”图标。

    ![SAML 图标](./media/iqualify-tutorial/setting3.png)

1. 在“SAML 身份验证设置”对话框中，执行以下步骤：

    ![SAML 身份验证设置](./media/iqualify-tutorial/setting4.png)

    a. 在“SAML 单一登录服务 URL”框中，粘贴从 Azure AD 应用程序配置窗口复制的“SAML 单一登录服务 URL”值。
    
    b. 在“SAML 注销 URL”框中，粘贴从 Azure AD 应用程序配置窗口复制的“注销 URL”值。
    
    c. 在记事本中打开下载的证书文件，复制其内容，然后将其粘贴到“公共证书”框中。
    
    d. 在“登录按钮标签”中输入要在登录页上显示的按钮的名称。
    
    e. 单击“保存”。

    f. 单击“更新”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/iqualify-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/iqualify-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/iqualify-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/iqualify-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-iqualify-lms-test-user"></a>创建 iQualify LMS 测试用户

在本部分，我们会在 iQualify 中创建一个名为 Britta Simon 的用户。 iQualify LMS 支持默认启用的实时用户预配。

此部分不存在任何操作项。 尝试访问 iQualify 时，如果 iQualify 中尚不存在用户，则系统会创建一个新用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 iQualify LMS 的权限，允许该用户使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 iQualify LMS，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“iQualify LMS”。

    ![应用程序列表中的 iQualify LMS 链接](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“iQualify LMS”磁贴时，应显示 iQualify LMS 应用程序的登录页。 

   ![登录页面](./media/iqualify-tutorial/login.png) 

单击“使用 Azure AD 登录”按钮，应会自动登录到 iQualify LMS 应用程序。

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png


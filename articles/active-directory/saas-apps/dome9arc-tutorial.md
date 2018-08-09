---
title: 教程：Azure Active Directory 与 Dome9 Arc 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Dome9 Arc 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: jeedes
ms.openlocfilehash: 944f1b7ecc3fdc9f063d764a6deae49a68a24d77
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433521"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>教程：Azure Active Directory 与 Dome9 Arc 的集成

本教程介绍如何将 Dome9 Arc 与 Azure Active Directory (Azure AD) 集成。

将 Dome9 Arc 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Dome9 Arc。
- 可以让用户使用其 Azure AD 帐户自动登录到 Dome9 Arc（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Dome9 Arc 的集成，需要准备好以下各项：

- Azure AD 订阅
- 已启用 Dome9 Arc 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Dome9 Arc
1. 配置和测试 Azure AD 单一登录

## <a name="adding-dome9-arc-from-the-gallery"></a>从库添加 Dome9 Arc
若要配置 Dome9 Arc 与 Azure AD 的集成，需从库中将 Dome9 Arc 添加到托管 SaaS 应用列表。

**若要从库中添加 Dome9 Arc，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“Dome9 Arc”，在结果面板中选择“Dome9 Arc”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 Dome9 Arc](./media/dome9arc-tutorial/tutorial_dome9arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Dome9 Arc 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Dome9 Arc 用户。 换句话说，需要建立 Azure AD 用户与 Dome9 Arc 中相关用户之间的关联关系。

在 Dome9 Arc 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此关联。

若要配置并测试 Dome9 Arc 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Dome9 Arc 测试用户](#create-a-dome9-arc-test-user)** - 在 Dome9 Arc 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Dome9 Arc 应用程序中配置单一登录。

**若要配置 Dome9 Arc 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的 **Dome9 Arc** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/dome9arc-tutorial/tutorial_dome9arc_samlbase.png)

1. 在“Dome9 Arc 域和 URL”部分，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![Dome9 Arc 域和 URL 单一登录信息](./media/dome9arc-tutorial/tutorial_dome9arc_url.png)

    a. 在“标识符”文本框中，键入 URL：`https://secure.dome9.com/`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > 需在 dome9 管理门户中选择公司名称值，这一点稍后在教程中讲述。

1. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Dome9 Arc 域和 URL 单一登录信息](./media/dome9arc-tutorial/tutorial_dome9arc_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： `https://secure.dome9.com/sso/saml/<yourcompanyname>`
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 请联系 [Dome9 Arc 客户端支持团队](https://dome9.com/about/contact-us/)以获取这些值。 

1. Dome9 Arc 软件应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。

    ![配置单一登录 attb](./media/dome9arc-tutorial/tutorial_dome9arc_attribute.png)

1. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称  | 属性值 | 
    | --------------- | --------------- | 
    | memberof | user.assignedroles | 
    
    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录 add 属性](./media/dome9arc-tutorial/tutorial_dome9_04.png)

    ![配置单一登录 edit 属性](./media/dome9arc-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定” 。

1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/dome9arc-tutorial/tutorial_dome9arc_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/dome9arc-tutorial/tutorial_general_400.png)
    
1. 在“Dome9 Arc 配置”部分，单击“配置 Dome9 Arc”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![Dome9 Arc 配置](./media/dome9arc-tutorial/tutorial_dome9arc_configure.png) 

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Dome9 Arc 公司站点。

1. 单击右上角的“配置文件设置”，然后单击“帐户设置”。 

    ![Dome9 Arc 配置](./media/dome9arc-tutorial/configure1.png)

1. 导航到 **SSO**，然后单击“启用”。

    ![Dome9 Arc 配置](./media/dome9arc-tutorial/configure2.png)

1. 在“SSO 配置”部分，执行以下步骤：

    ![Dome9 Arc 配置](./media/dome9arc-tutorial/configure3.png)

    a. 在“帐户 ID”文本框中输入公司名称。 此值将在“Azure 门户 URL”部分提到的回复 URL 中使用。

    b. 将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“颁发者”文本框中。

    c. 在“Idp 终结点 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    d. 在记事本中打开下载的 Base64 编码证书，将其内容复制到剪贴板，并粘贴到“X.509 证书”文本框中。

    e. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/dome9arc-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/dome9arc-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/dome9arc-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/dome9arc-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-dome9-arc-test-user"></a>创建 Dome9 Arc 测试用户

为了使 Azure AD 用户能够登录 Dome9 Arc，必须将其预配到应用程序中。 Dome9 Arc 支持恰时预配，但要正常使用此功能，必须选择特定的**角色**并将其分配给用户。

   >[!Note] 
   >若要了解**角色**创建过程以及其他详细信息，请联系 [Dome9 Arc 客户端支持团队](https://dome9.com/about/contact-us/)。

**若要手动预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Dome9 Arc 公司站点。

1. 单击“用户和角色”，然后单击“用户”。

    ![添加员工](./media/dome9arc-tutorial/user1.png)

1. 单击“添加用户”。

    ![添加员工](./media/dome9arc-tutorial/user2.png)

1. 在“创建用户”部分中，执行以下步骤：
    
    ![添加员工](./media/dome9arc-tutorial/user3.png)

    a. 在“电子邮件”文本框中，键入用户的电子邮件，例如 Brittasimon@contoso.com。

    b. 在“名字”文本框中，键入用户的名字（如 Britta）。

    c. 在“姓氏”文本框中，键入用户的姓氏（如 Simon）。

    d. 使“SSO 用户”处于“启用”状态。

    e. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Dome9 Arc 的访问权限，支持她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Dome9 Arc，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Dome9 Arc”。

    ![应用程序列表中的 Dome9 Arc 链接](./media/dome9arc-tutorial/tutorial_dome9arc_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Dome9 Arc 磁贴时，会自动登录到 Dome9 Arc 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dome9arc-tutorial/tutorial_general_01.png
[2]: ./media/dome9arc-tutorial/tutorial_general_02.png
[3]: ./media/dome9arc-tutorial/tutorial_general_03.png
[4]: ./media/dome9arc-tutorial/tutorial_general_04.png

[100]: ./media/dome9arc-tutorial/tutorial_general_100.png

[200]: ./media/dome9arc-tutorial/tutorial_general_200.png
[201]: ./media/dome9arc-tutorial/tutorial_general_201.png
[202]: ./media/dome9arc-tutorial/tutorial_general_202.png
[203]: ./media/dome9arc-tutorial/tutorial_general_203.png


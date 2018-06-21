---
title: 教程：Azure Active Directory 与 Zoom 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zoom 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: ed0018277502d2689d28009e58cdffd39a02cee7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231172"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>教程：Azure Active Directory 与 Zoom 的集成

本教程介绍了如何将 Zoom 与 Azure Active Directory (Azure AD) 进行集成。

将 Zoom 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Zoom。
- 可以让用户使用其 Azure AD 帐户自动登录到 Zoom（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zoom 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了单一登录的 Zoom 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Zoom
2. 配置和测试 Azure AD 单一登录

## <a name="adding-zoom-from-the-gallery"></a>从库中添加 Zoom
若要配置 Zoom 与 Azure AD 的集成，需要从库中将 Zoom 添加到托管 SaaS 应用列表。

**若要从库中添加 Zoom，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Zoom”，在结果面板中选择“Zoom”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Zoom](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Zoom 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Zoom 用户。 换句话说，需要在 Azure AD 用户与 Zoom 中的相关用户之间建立链接关系。

在 Zoom 中，通过将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 Zoom 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Zoom 测试用户](#create-a-zoom-test-user)** - 在 Zoom 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Zoom 应用程序中配置单一登录。

**若要配置 Zoom 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Zoom** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

3. 在“Zoom 域和 URL”部分中，执行以下步骤：

    ![Zoom 域和 URL 单一登录信息](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.zoom.us`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`<companyname>.zoom.us`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Zoom 客户端支持团队](https://support.zoom.us/hc)来获取这些值。

4. Zoom 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 

    ![配置单一登录](./media/zoom-tutorial/tutorial_attribute.png)

5. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，再执行以下步骤：
    
    | 属性名称 | 属性值 | 命名空间值 |
    | ------------------- | -----------|--------- |    
    | 电子邮件地址 | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | 名字 | user.givenname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | 姓氏 | user.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | 电话号码 | user.telephonenumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | 部门 | user.department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 在“命名空间”文本框中，键入为该行显示的命名空间值。
    
    e. 单击“确定” 。 
 
6. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/zoom-tutorial/tutorial_zoom_certificate.png)

7. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/zoom-tutorial/tutorial_general_400.png)

8. 在“Zoom 配置”部分中，单击“配置 Zoom”以打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![Zoom 配置](./media/zoom-tutorial/tutorial_zoom_configure.png)

9. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Zoom 公司站点。

10. 单击“单一登录” 选项卡。
   
    ![“单一登录”选项卡](./media/zoom-tutorial/IC784700.png "单一登录")

11. 单击“安全控制”，并转到“单一登录”设置。

12. 在“单一登录”部分中，执行以下步骤：
   
    ![“单一登录”部分](./media/zoom-tutorial/IC784701.png "单一登录")
   
    a. 在“登录页 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
   
    b. 在“注销页 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。
     
    c. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，再粘贴到“标识提供者证书”文本框中。

    d. 将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“颁发者”文本框中。 

    e. 单击“ **保存**”。

    > [!NOTE] 
    > 有关详细信息，请访问 zoom 文档 [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/zoom-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/zoom-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/zoom-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/zoom-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-zoom-test-user"></a>创建 Zoom 测试用户

为了使 Azure AD 用户能够登录到 Zoom，必须将其预配到 Zoom 中。 就 Zoom 来说，预配任务需要手动完成。

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：

1. 以管理员身份登录到 **Zoom** 公司站点。
 
2. 单击“帐户管理”选项卡，并单击“用户管理”。

3. 在“用户管理”部分，单击“添加用户”。
   
    ![用户管理](./media/zoom-tutorial/IC784703.png "用户管理")

4. 在“添加用户”页上，执行以下步骤：
   
    ![添加用户](./media/zoom-tutorial/IC784704.png "添加用户")
   
    a. 对于“用户类型”，请选择“基本”。

    b. 在“电子邮件”文本框中，键入要预配的有效 Azure AD 帐户的电子邮件地址。

    c. 单击 **“添加”**。

> [!NOTE]
> 可以使用 Zoom 提供的任何其他 Zoom 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Zoom 的权限，允许其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Zoom，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Zoom”。

    ![应用程序列表中的 Zoom 链接](./media/zoom-tutorial/tutorial_zoom_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

当在访问面板中单击 Zoom 磁贴时，应当会自动登录到 Zoom 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zoom-tutorial/tutorial_general_01.png
[2]: ./media/zoom-tutorial/tutorial_general_02.png
[3]: ./media/zoom-tutorial/tutorial_general_03.png
[4]: ./media/zoom-tutorial/tutorial_general_04.png

[100]: ./media/zoom-tutorial/tutorial_general_100.png

[200]: ./media/zoom-tutorial/tutorial_general_200.png
[201]: ./media/zoom-tutorial/tutorial_general_201.png
[202]: ./media/zoom-tutorial/tutorial_general_202.png
[203]: ./media/zoom-tutorial/tutorial_general_203.png


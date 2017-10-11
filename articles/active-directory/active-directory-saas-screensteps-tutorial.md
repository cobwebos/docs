---
title: "教程：Azure Active Directory 与 ScreenSteps 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 ScreenSteps 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: b6ded8ba1adf03fdccbdb7573c09fae1857c8b16
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>教程：Azure Active Directory 与 ScreenSteps 集成

本教程介绍如何将 ScreenSteps 与 Azure Active Directory (Azure AD) 集成。

将 ScreenSteps 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 ScreenSteps。
- 可以让用户使用其 Azure AD 帐户自动登录到 ScreenSteps（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>必备组件

要配置 Azure AD 与 ScreenSteps 的集成，需要以下项：

- Azure AD 订阅
- 已启用 ScreenSteps 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ScreenSteps
2. 配置并测试 Azure AD 单一登录

## <a name="adding-screensteps-from-the-gallery"></a>从库中添加 ScreenSteps
要配置 ScreenSteps 与 Azure AD 的集成，需要从库中将 ScreenSteps 添加到托管 SaaS 应用列表。

**要从库添加 ScreenSteps，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中，键入“ScreenSteps”，在结果面板中选择“ScreenSteps”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 ScreenSteps](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 ScreenSteps 的 Azure AD 单一登录。

要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 ScreenSteps 用户。 换句话说，需要建立 Azure AD 用户与 ScreenSteps 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 ScreenSteps 中“用户名”的值，建立此链接关系。

要配置并测试 ScreenSteps 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 ScreenSteps 测试用户](#create-a-screensteps-test-user)** - 在 ScreenSteps 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 ScreenSteps 应用程序中配置单一登录。

**要配置 ScreenSteps 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 ScreenSteps 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. 在“ScreenSteps 域和 URL”部分中，执行以下步骤：

    ![ScreenSteps 域和 URL 单一登录信息](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > 此值不是真实值。 本教程稍后将介绍如何使用实际登录 URL 来更新该值。 

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. 在“ScreenSteps 配置”部分中，单击“配置 ScreenSteps”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![ScreenSteps 配置](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录 ScreenSteps 公司站点。

8. 单击“帐户设置”。

    ![帐户管理](./media/active-directory-saas-screensteps-tutorial/ic778523.png "帐户管理")

9. 单击“单一登录”。

    ![远程身份验证](./media/active-directory-saas-screensteps-tutorial/ic778524.png "远程身份验证")

10. 单击“创建单一登录终结点”。

    ![远程身份验证](./media/active-directory-saas-screensteps-tutorial/ic778525.png "远程身份验证")

11. 在“创建单一登录终结点”部分中，执行以下步骤：

    ![创建身份验证终结点](./media/active-directory-saas-screensteps-tutorial/ic778526.png "创建身份验证终结点")
    
    a. 在“标题”文本框中，键入标题。
    
    b. 从“模式”列表中，选择“SAML”。
    
    c. 单击“创建” 。

12. “编辑”新的终结点。

    ![编辑终结点](./media/active-directory-saas-screensteps-tutorial/ic778528.png "Edit endpoint")

13. 在“编辑单一登录终结点”部分中，执行以下步骤：

    ![远程身份验证终结点](./media/active-directory-saas-screensteps-tutorial/ic778527.png "远程身份验证终结点")

    a. 单击“上传新 SAML 证书文件”，然后上传从 Azure 门户下载的证书。
    
    b. 在“远程登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
    
    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。
    
    d.单击“下一步”。 选择用户预配后会被分配到的**组**。
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“更新”。

    f. 将“SAML使用者 URL”复制到剪贴板，并粘贴到“ScreenSteps 域和 URL”部分的“登录 URL”文本框中。
    
    g. 返回“编辑单一登录终结点”。
    
    h. 单击“对帐户设为默认”按钮，对登录到 ScreenSteps 的所有用户使用此终结点。 或者可单击“添加到站点”按钮，对 **ScreenSteps** 中的特定站点使用此终结点。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 在“姓名”框中，键入“BrittaSimon”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建” 。
 
### <a name="create-a-screensteps-test-user"></a>创建 ScreenSteps 测试用户

在本部分中，将在 ScreenSteps 中创建名为 Britta Simon 的用户。 与 [ScreenSteps 客户端支持团队](https://www.screensteps.com/contact)合作，在 ScreenSteps 平台中添加用户。 使用单一登录前，必须先创建并激活用户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予 ScreenSteps 的访问权限使之能够使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 ScreenSteps，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“ScreenSteps”。

    ![应用程序列表中的 ScreenSteps 链接](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 ScreenSteps 磁贴时，应自动登录到 ScreenSteps 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png


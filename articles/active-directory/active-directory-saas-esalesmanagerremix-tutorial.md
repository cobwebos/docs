---
title: "教程：Azure Active Directory 与 E Sales Manager Remix 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 E Sales Manager Remix 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>教程：Azure Active Directory 与 E Sales Manager Remix 集成

本教程介绍了如何将 E Sales Manager Remix 与 Azure Active Directory (Azure AD) 进行集成。

将 E Sales Manager Remix 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 E Sales Manager Remix。
- 可以让用户使用其 Azure AD 帐户自动登录到 E Sales Manager Remix（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 E Sales Manager Remix 的集成，需要以下各项：

- Azure AD 订阅
- 一个启用了单一登录的 E Sales Manager Remix 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 E Sales Manager Remix
2. 配置和测试 Azure AD 单一登录

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>从库中添加 E Sales Manager Remix
若要配置 E Sales Manager Remix 与 Azure AD 的集成，需要从库中将 E Sales Manager Remix 添加到托管 SaaS 应用列表。

**若要从库中添加 E Sales Manager Remix，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“E Sales Manager Remix”，在结果面板中选择“E Sales Manager Remix”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 E Sales Manager Remix 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 E Sales Manager Remix 用户。 换句话说，需要在 Azure AD 用户与 E Sales Manager Remix 中的相关用户之间建立链接关系。

若要配置和测试 E Sales Manager Remix 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 E Sales Manager Remix 测试用户](#create-an-e-sales-manager-remix-test-user) - 在 E Sales Manager Remix 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 E Sales Manager Remix 应用程序中配置单一登录。

**若要配置 E Sales Manager Remix 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 E Sales Manager Remix 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. 在“E Sales Manager Remix 域和 URL”部分中，执行以下步骤：

    ![E Sales Manager Remix 域和 URL 单一登录信息](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<Server-Based-URL>/<sub-domain>/esales-pc`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<Server-Based-URL>/<sub-domain>/`

    c. 复制记事本中的“标识符”值。 将在本教程的后面部分使用标识符值。
    
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [E Sales Manager Remix 客户端支持团队](mailto:esupport@softbrain.co.jp)获取这些值。

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. 选择“查看和编辑其他所有用户属性”，然后单击“emailaddress”属性。
    
    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. 从文本框中复制声明的“Namespace”和“Name”值。 生成值并采用后列格式 - `<Namespace>/<Name>`。 将在本教程的后面部分使用此值。

    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. 在“E Sales Manager Remix 配置”部分中，单击“配置 E Sales Manager Remix”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. 以管理员身份登录到 E Sales Manager Remix 应用程序。

10. 从右上角菜单选择“到管理员菜单”。

    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. 选择“系统设置”>“与外部系统协作”

    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. 选择“SAML”

    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. 在“SAML 身份验证设置”部分中，执行以下步骤：

    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. 选择“PC 版本”
    
    b. 从“协作项”部分中的下拉列表中选择“电子邮件”。

    c. 在“协作项”文本框中，粘贴从 Azure 门户复制的声明值，即为： `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d.单击“下一步”。 在“颁发者(实体 ID)”文本框中，粘贴从 Azure 门户的”E Sales Manager Remix 域和 URL”部分中复制的标识符值。

    e. 若要上传从 Azure 门户下载的证书，请选择“文件选择”。

    f. 在“ID 提供程序登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”。

    g. 在“标识提供者注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    h. 单击“设置完成”

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 单击“创建”。
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>创建 E Sales Manager Remix 测试用户

1. 以管理员身份登录到 E Sales Manager Remix 应用程序。

2. 从右上角菜单选择“到管理员菜单”。

    ![E Sales Manager Remix 配置](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. 选择“公司设置”>“部门和员工维护”，然后选择“注册员工”。

    ![用户](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. 在“新员工注册”部分，执行以下步骤：
    
    ![用户](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. 在“员工姓名”文本框中，键入用户名（如 Britta ）。

    b. 使用用户信息填充所有相应的必填字段。
    
    c. 如果启用了 SAML，管理员将不能从登录屏幕登录，所以应通过选择“管理员登录”，向用户授予管理员登录特权

    d.单击“下一步”。 单击“注册”

5. 如果将来想以管理员身份登录，可通过已授予管理员权限的用户登录，然后在右上角菜单中单击“到管理员菜单”。

    ![用户](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 E Sales Manager Remix 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 E Sales Manager Remix，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“E Sales Manager Remix”。

    ![应用程序列表中的 E Sales Manager Remix 链接](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 E Sales Manager Remix 磁贴时，应该会自动登录到 E Sales Manager Remix 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png


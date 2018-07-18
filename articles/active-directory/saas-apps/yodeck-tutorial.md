---
title: 教程：Azure Active Directory 与 Yodeck 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Yodeck 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: 0a3d0fd8d6fda711608b40a975fd1f4bf1e88a7d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218058"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>教程：Azure Active Directory 与 Yodeck 集成

本教程介绍如何将 Yodeck 与 Azure Active Directory (Azure AD) 集成。

将 Yodeck 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Yodeck。
- 可以让用户使用其 Azure AD 帐户自动登录到 Yodeck（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Yodeck 的集成，需要以下项：

- Azure AD 订阅
- 启用 Yodeck 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Yodeck
2. 配置和测试 Azure AD 单一登录

## <a name="adding-yodeck-from-the-gallery"></a>从库中添加 Yodeck
若要配置 Yodeck 与 Azure AD 的集成，需要从库中将 Yodeck 添加到托管 SaaS 应用列表。

**若要从库中添加 Yodeck，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Yodeck”，在结果面板中选择“Yodeck”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Yodeck](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Yodeck 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Yodeck 用户。 换句话说，需要建立 Azure AD 用户与 Yodeck 中相关用户之间的链接关系。

若要配置和测试 Yodeck 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Yodeck 测试用户](#create-a-yodeck-test-user)** - 在 Yodeck 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Yodeck 应用程序中配置单一登录。

**若要配置 Yodeck 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Yodeck”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. 在“Yodeck 域和 URL”部分，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![Yodeck 域和 URL 单一登录信息](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    在“标识符(实体 ID)”文本框中，键入 URL：`https://app.yodeck.com/api/v1/account/metadata/`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Yodeck 域和 URL 单一登录信息](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    在“登录 URL”文本框中，键入 URL：`https://app.yodeck.com/login`

5. 在“SAML 签名证书”部分上，单击”复制”按钮来复制**应用联合元数据 URL**，并将其粘贴到记事本。

    ![证书下载链接](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/yodeck-tutorial/tutorial_general_400.png)
    
7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Yodeck 公司站点。

8. 单击页面右上角的“用户设置”选项表单，然后选择“帐户设置”。

    ![Yodeck 配置](./media/yodeck-tutorial/configure1.png)

9. 选择“SAML”并执行以下步骤：

    ![Yodeck 配置](./media/yodeck-tutorial/configure2.png)

    a. 选择“从 URL 导入”。

    b. 在“URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值，然后单击“导入”。
    
    c. 导入“应用联合元数据 URL”后，其余字段将自动填充。

    d. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/yodeck-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/yodeck-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/yodeck-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/yodeck-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-yodeck-test-user"></a>创建 Yodeck 测试用户

为使 Azure AD 用户能够登录 Yodeck，必须将其预配置到 Yodeck 中。
就 Yodeck 来说，预配任务需要手动完成。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Yodeck 公司站点。

2. 单击页面右上角的“用户设置”选项表单，然后选择“用户”。

    ![添加员工](./media/yodeck-tutorial/user1.png)

3. 单击“+用户”以打开“用户详细信息”选项卡。

    ![添加员工](./media/yodeck-tutorial/user2.png)

4. 在“用户详细信息”对话框页上，执行以下步骤：

    ![添加员工](./media/yodeck-tutorial/user3.png)

    a. 在“名字”文本框中，键入用户的名字（如“Britta”）。

    b. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）。

    c. 在“电子邮件”文本框中，键入用户的电子邮件地址，如 brittasimon@contoso.com。

    d. 根据组织要求选择适当的“帐户权限”选项。
    
    e. 单击“ **保存**”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Yodeck 的访问权限来支持其使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 Yodeck，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Yodeck”。

    ![应用程序列表中的 Yodeck 链接](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Yodeck 磁贴时，应当会自动登录到 Yodeck 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png


---
title: 教程：Azure Active Directory 与 Workteam 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workteam 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd986544dfb1472f5cc8a013fec951dca42a59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898646"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>教程：Azure Active Directory 与 Workteam 集成

本教程介绍如何将 Workteam 与 Azure Active Directory (Azure AD) 集成。

将 Workteam 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Workteam。
- 可让用户使用其 Azure AD 帐户自动登录到 Workteam（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必备组件

若要配置 Azure AD 与 Workteam 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 Workteam 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Workteam
2. 配置和测试 Azure AD 单一登录

## <a name="adding-workteam-from-the-gallery"></a>从库中添加 Workteam
要配置 Workteam 与 Azure AD 的集成，需要从库中将 Workteam 添加到托管 SaaS 应用列表。

**若要从库中添加 Workteam，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Workteam”，在结果面板中选择“Workteam”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Workteam](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为“Britta Simon”的测试用户使用 Workteam 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Workteam 用户。 换句话说，需要建立 Azure AD 用户与 Workteam 中相关用户之间的链接关系。

若要配置和测试 Workteam 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Workteam 测试用户](#create-a-workteam-test-user)** - 在 Workteam 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Workteam 应用程序中配置单一登录。

**若要配置 Workteam 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Workteam** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. 在“Workteam 域和 URL”部分，用户不必执行任何步骤，因为该应用程序已经与 Azure 预先集成。

    ![Workteam 域和 URL 单一登录信息](./media/workteam-tutorial/tutorial_workteam_url.png)

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Workteam 域和 URL 单一登录信息](./media/workteam-tutorial/tutorial_workteam_url1.png)

    在“登录 URL”文本框中，键入 URL `https://app.workte.am`
     
5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/workteam-tutorial/tutorial_general_400.png)
    
7. 在“Workteam 配置”部分，单击“配置 Workteam”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![Workteam 配置](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Workteam。

9. 在右上角单击**个人资料徽标**，然后单击“组织设置”。 

    ![Workteam 设置](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. 在“身份验证”部分下，单击“设置”徽标。

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. 在“SAML 设置”页上，执行以下步骤：

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. 为“SAML IdP”选择“AD Azure”。

    b. 在“SAML 单一登录服务 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    c. 将从 Azure 门户复制的“SAML 实体 ID”值粘贴到“SAML 实体 ID”文本框中。

    d. 在记事本中，打开从 Azure 门户下载的 **base-64 编码证书**，复制其内容，然后将其粘贴到“SAML 签名证书(Base64)”框中 。

    e. 单击“确定”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/workteam-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/workteam-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/workteam-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/workteam-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-workteam-test-user"></a>创建 Workteam 测试用户

为了使 Azure AD 用户能够登录 Workteam，必须对其进行预配才能使其登录 Workteam。 在 Workteam 中，预配属于手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 Workteam。

2. 在“组织设置”页的中上部分，依次单击“用户”、“新建用户”。

    ![Workteam 用户](./media/workteam-tutorial/tutorial_workteam_user.png)

3. 在“新建员工”页上执行以下步骤：

    ![Workteam newuser](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. 在“姓名”文本框中，输入用户的名字，例如 **Brittasimon**。

    b. 在中**电子邮件**文字框中，输入类似的用户的电子邮件**Brittasimon\@contoso.com**。

    c. 单击“确定”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过向 Britta Simon 授予 Workteam 的访问权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Workteam，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Workteam”。

    ![应用程序列表中的 Workteam 链接](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Workteam”磁贴时，应会自动登录到 Workteam 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png


---
title: 教程：Azure Active Directory 与 Silverback 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Silverback 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5614c061586c39e44f04f3542285e55e07f14d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56172700"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>教程：Azure Active Directory 与 Silverback 的集成

在本教程中，你将学习如何将 Silverback 与 Azure Active Directory (Azure AD) 集成。

将 Silverback 与 Azure AD 集成可提供以下好处：

- 可在 Azure AD 中控制谁有权访问 Silverback。
- 可使用户通过其 Azure AD 帐户自动登录 Silverback（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Silverback 的集成，需要准备好以下各项：

- Azure AD 订阅
- 有效的 Silverback 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Silverback
2. 配置和测试 Azure AD 单一登录

## <a name="adding-silverback-from-the-gallery"></a>从库中添加 Silverback
若要配置 Silverback 与 Azure AD 的集成，需要从库中将 Silverback 添加到托管 SaaS 应用列表。

**若要从库中添加 Silverback，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Silverback”，在结果面板中选择“Silverback”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 Silverback](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，你将以名为“Britta Simon”的测试用户的身份使用 Silverback 配置和测试 Azure AD 单一登录。

若要单一登录生效，Azure AD 需要知道与 Azure AD 用户相对应的 Silverback 用户。 换句话说，需要建立 Azure AD 用户与 Silverback 中相关用户之间的关联关系。

若要配置和测试 Silverback 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Silverback 测试用户](#create-a-silverback-test-user)** - 在 Silverback 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，你将在 Azure 门户中启用 Azure AD 单一登录并在 Silverback 应用程序中配置单一登录。

**若要配置 Silverback 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **Silverback** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. 在“Silverback 域和 URL”部分中，执行以下步骤：

    ![Silverback 域和 URL 单一登录信息](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<YOURSILVERBACKURL>.com/ssp`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`<YOURSILVERBACKURL>.com`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Silverback 客户端支持团队](mailto:helpdesk@matrix42.com)获取这些值。 

4. 在“SAML 签名证书”部分上，单击”复制”按钮来复制**应用联合元数据 URL**，并将其粘贴到记事本。

    ![证书下载链接](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/silverback-tutorial/tutorial_general_400.png)

6.  以管理员身份登录 Silverback 服务器并执行以下步骤：

    a.  导航到“管理员” > “验证提供程序”。

    b. 在“验证提供程序设置”页上，执行以下步骤：

    ![管理员 ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    c.  单击“从 URL 导入”。
    
    d.  粘贴复制的元数据 URL 并单击“确定”。
    
    e.  使用“确定”进行确认，值随后会自动填充。
    
    f.  启用“在登录页上显示”。
    
    g.  如果想要按 Azure AD 授权用户自动添加（可选），请启用“动态用户创建”。
    
    h.  为自助服务门户上的按钮创建“标题”。

    i.  单击“选择文件”来上传“图标”。
    
    j.  为按钮选择背景“颜色”。
    
    k.  单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/silverback-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/silverback-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/silverback-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/silverback-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-silverback-test-user"></a>创建 Silverback 测试用户

若要使 Azure AD 用户能够登录 Silverback，则必须将其预配到 Silverback 中。 在 Silverback 中，预配是手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Silverback 服务器。

2. 导航到“用户”，然后“添加新设备用户”。

3. 在“基本”页上，执行以下步骤：

    ![用户 ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. 在“用户名”文本框中，输入用户的名字，例如 **Britta**。

    b. 在“名字”文本框中，输入用户的名字，如 Britta。

    c. 在“姓氏”文本框中，输入用户的名字，如 Simon。

    d. 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 **Brittasimon@contoso.com**。

    e. 在“密码”文本框中，输入密码。
    
    f. 在“确认密码”文本框中，重新输入密码并确认。

    g. 单击“ **保存**”。

>[!NOTE]
>如果不想手动创建每个用户，请启用“管理员” > “验证提供程序”下的“动态用户创建”复选框。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 Britta Simon 访问 Silverback 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Silverback，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Silverback”。

    ![应用程序列表中的 Silverback 链接](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Silverback”磁贴时，用户应自动登录到 Silverback 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png


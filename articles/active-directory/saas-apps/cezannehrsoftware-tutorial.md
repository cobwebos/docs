---
title: 教程：Azure Active Directory 与 Cezanne HR Software 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Cezanne HR Software 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: c44d1219c9f352a868aff3305372537ecd7a45b7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225759"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>教程：Azure Active Directory 与 Cezanne HR Software 的集成

在本教程中，了解如何将 Cezanne HR Software 与 Azure Active Directory (Azure AD) 集成。

将 Cezanne HR Software 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Cezanne HR Software。
- 可以让用户使用其 Azure AD 帐户自动登录到 Cezanne HR Software（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Cezanne HR Software 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Cezanne HR Software 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Cezanne HR Software
2. 配置和测试 Azure AD 单一登录

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>从库中添加 Cezanne HR Software
要配置 Cezanne HR Software 与 Azure AD 的集成，需要从库中将 Cezanne HR Software 添加到托管 SaaS 应用列表。

**若要从库中添加 Cezanne HR Software，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Cezanne HR Software”，在结果面板中选择“Cezanne HR Software”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Cezanne HR Software](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Cezanne HR Software 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Cezanne HR Software 用户。 换句话说，需要在 Azure AD 用户与 Cezanne HR Software 中的相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 Cezanne HR Software 中“用户名”的值来建立此链接关系。

若要配置并测试 Cezanne HR Software 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Cezanne HR Software 测试用户](#create-a-cezannehrsoftware-test-user)** - 在 Cezanne HR Software 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录，并在 Cezanne HR Software 应用程序中配置单一登录。

**若要配置 Cezanne HR Software 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的“Cezanne HR Software”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. 在“Cezanne HR Software 域和 URL”部分中，执行以下步骤：

    ![Cezanne HR Software 域和 URL 单一登录信息](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. 在“登录 URL”文本框中，键入 URL：`https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. 在“标识符”文本框中，键入 URL：`https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. 在“回复 URL”文本框中，键入 URL：`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 若要获取这些值，请联系 [Cezanne HR Software 客户端支持团队](https://cezannehr.com/services/support/)。

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

6. 在“Cezanne HR Software 配置”部分中，单击“配置 Cezanne HR Software”打开“配置登录”窗口。

    ![Cezanne HR Software 配置](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

7. 向下滚动到“快速参考”部分。 从“快速参考”部分中复制“SAML 单一登录服务 URL 和 SAML 实体 ID”

    ![Cezanne HR Software 配置](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

8. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Cezanne HR Software 租户。

9. 在左侧导航窗格中，单击“系统设置”。 转到“安全设置”。 然后导航到“单一登录配置”。

    ![在应用端配置单一登录](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

10. 在“允许用户使用以下单一登录 (SSO) 服务进行登录”面板上，选中“SAML 2.0”框并选择“高级配置”选项。

    ![在应用端配置单一登录](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

11. 单击“新增”按钮。

    ![在应用端配置单一登录](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

12. 在“SAML 2.0 标识提供者”部分中执行以下步骤。

    ![在应用端配置单一登录](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. 对于“显示名称”，输入标识提供者的名称。

    b. 在“实体标识符”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。 

    c. 将“SAML 绑定”更改为“POST”。

    d. 在“安全令牌服务终结点”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    e. 在用户 ID 属性名称文本框中，输入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。
    
    f. 单击“上传”图标，上传从 Azure 门户下载的证书。
    
    g. 单击“确定”按钮。 

13. 单击“保存”按钮。

    ![在应用端配置单一登录](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-cezanne-hr-software-test-user"></a>创建 Cezanne HR Software 测试用户

为了使 Azure AD 用户能够登录到 Cezanne HR Software，必须将其预配到 Cezanne HR Software 中。 对于 Cezanne HR Software，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1.  以管理员身份登录到 Cezanne HR Software 公司站点。

2.  在左侧导航窗格中，单击“系统设置”。 转到“管理用户”。 然后导航到“添加新用户”。

    ![新建用户](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "New User")

3.  在“人员详细信息”部分中，执行以下步骤︰

    ![新建用户](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "New User")
    
    a. 将“内部用户”设置为“关闭”。
    
    b. 在“名字”文本框中，键入用户的名字（如“Britta”）。  
 
    c. 在“姓氏”文本框中，键入用户的姓氏（如“Simon”）。
    
    d. 在“电子邮件”文本框中，键入用户的电子邮件地址（如 Brittasimon@contoso.com）。

4.  在“帐户信息”部分中，执行以下步骤：

    ![新建用户](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "New User")
    
    a. 在“用户名”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。
    
    b. 在“密码”文本框中，键入用户的密码。
    
    c. 对于“安全角色”，选择“HR 专业人员”。
    
    d. 单击“确定”。

5. 导航到“单一登录”选项卡，并在“SAML 2.0 标识符”区域中选择“新增”。

    ![用户](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "用户")

6. 对于“标识提供者”，选择标识提供者，在“用户标识符”文本框中，输入 Britta Simon 帐户的电子邮件地址。

    ![用户](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "用户")
    
7. 单击“保存”按钮。

    ![用户](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "用户")

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Cezanne HR Software 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Cezanne HR Software，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Cezanne HR Software”。

    ![应用程序列表中的 Cezanne HR Software 链接](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Cezanne HR Software 磁贴时，应当会自动登录到 Cezanne HR Software 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png


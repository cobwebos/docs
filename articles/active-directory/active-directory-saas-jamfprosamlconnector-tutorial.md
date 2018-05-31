---
title: 教程：Azure Active Directory 与 Jamf Pro 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Jamf Pro 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 488787adfbae0147c3bd0425d839e2ad8c5ed786
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308757"
---
# <a name="tutorial-azure-active-directory-integration-with-jamf-pro"></a>教程：Azure Active Directory 与 Jamf Pro 集成

在本教程中，了解如何将 Jamf Pro 与 Azure Active Directory (Azure AD) 集成。

将 Jamf Pro 与 Azure AD 集成可以提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Jamf Pro。
- 可使用户通过其 Azure AD 帐户自动登录 Jamf Pro（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Jamf Pro 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 Jamf Pro 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Jamf Pro
2. 配置和测试 Azure AD 单一登录

## <a name="adding-jamf-pro-from-the-gallery"></a>从库添加 Jamf Pro
要配置 Jamf Pro 与 Azure AD 的集成，需要从库中将 Jamf Pro 添加到托管 SaaS 应用列表。

若要从库中添加 Jamf Pro，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Jamf Pro”，在结果面板中选择“Jamf Pro”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Jamf Pro](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Jamf Pro 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Jamf Pro 用户。 换句话说，需要建立 Azure AD 用户与 Jamf Pro 中相关用户之间的链接关系。

若要配置和测试 Jamf Pro 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Jamf Pro 测试用户](#create-a-jamf-pro-test-user)** - 在 Jamf Pro 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Jamf Pro 应用程序中配置单一登录。

若要配置 Jamf Pro 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Jamf Pro 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_samlbase.png)

3. 在“Jamf Pro 域和 URL”部分，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![Jamf Pro 域和 URL 单一登录信息](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url.png)

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.jamfcloud.com/saml/SSO`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Jamf Pro 域和 URL 单一登录信息](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.jamfcloud.com`
     
    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 将从 Jamf Pro 门户中的“单一登录”部分获取实际的标识符值（本教程稍后会介绍）。 可以从标识符值提取实际的子域值，并在登录 URL 和答复 URL 中使用该子域信息。

5. 在“SAML 签名证书”部分上单击“复制”按钮复制应用联合元数据 URL，并将其粘贴到记事本。

    ![证书下载链接](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_400.png)
    
7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Jamf Pro 公司站点。

8. 单击页面右上角的“设置”图标。

    ![Jamf Pro 配置](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

9. 单击“单一登录”。

    ![Jamf Pro 配置](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure2.png)

10. 向下滚动到“单一登录”部分下的“标识提供者”，并执行以下步骤：

    ![Jamf Pro 配置](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure3.png)

    a. 从“标识提供者”下拉菜单中选择选项“其他”。

    b. 在“其他提供程序”文本框中输入“Azure AD”。

    c. 选择“标识提供者元数据源”下拉菜单中的选项“元数据 URL”，并将你从 Azure 门户复制的“应用联合元数据 URL”值粘贴到下面的文本框中。

    d. 复制“实体 ID”值并将其粘贴到 Azure 门户上“Jamf Pro 域和 URL”部分的“标识符(实体 ID)”文本框中。

    >[!NOTE]
    > 此处的 `aadsso` 是用于引用的子域部分。 使用此值完成 Azure 门户上的“Jamf Pro 域和 URL”部分中的登录 URL 和答复 URL。

    e. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-jamfprosamlconnector-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-jamf-pro-test-user"></a>创建一个 Jamf Pro 测试用户

若要使 Azure AD 用户能够登录 Jamf Pro，必须将其预配到 Jamf Pro 中。 就 Jamf Pro 来说，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Jamf Pro 公司站点。

2. 单击页面右上角的“设置”图标。

    ![添加员工](./media/active-directory-saas-jamfprosamlconnector-tutorial/configure1.png)

3. 单击“Jamf Pro 用户帐户和组”。

    ![添加员工](./media/active-directory-saas-jamfprosamlconnector-tutorial/user1.png)

4. 单击“新建” 。

    ![添加员工](./media/active-directory-saas-jamfprosamlconnector-tutorial/user2.png)

5. 选择“创建标准帐户”。

    ![添加员工](./media/active-directory-saas-jamfprosamlconnector-tutorial/user3.png)

6. 在“新建帐户”对话框执行以下步骤：

    ![添加员工](./media/active-directory-saas-jamfprosamlconnector-tutorial/user4.png)

    a. 在“用户名”文本框中键入“BrittaSimon”的全名。

    b. 根据你的组织为“访问级别”、“特权设置”和“访问状态”选择合适的选项。
    
    c. 在“全名”文本框中，键入 Britta Simon 的全名。

    d. 在“电子邮件地址”文本框中，键入 Britta Simon 帐户的电子邮件地址。

    e. 在“密码”文本框中，键入用户的密码。

    f. 在“验证密码”文本框中，键入用户的密码。

    g. 单击“ **保存**”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Jamf Pro 的权限，支持其使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 分配到 Jamf Pro，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Jamf Pro”。

    ![应用程序列表中的 Jamf Pro 链接](./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Jamf Pro 磁贴时，应当会自动登录到 Jamf Pro 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jamfprosamlconnector-tutorial/tutorial_general_203.png


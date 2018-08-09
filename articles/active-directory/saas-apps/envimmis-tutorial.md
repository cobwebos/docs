---
title: 教程：Azure Active Directory 与 Envi MMIS 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Envi MMIS 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab89f8ee-2507-4625-94bc-b24ef3d5e006
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 96168dcb8400d2580d0b64257ceb861c1da3ff65
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447279"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>教程：Azure Active Directory 与 Envi MMIS 集成

本教程介绍了如何将 Envi MMIS 与 Azure Active Directory (Azure AD) 进行集成。

将 Envi MMIS 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 Envi MMIS。
- 可以让用户使用其 Azure AD 帐户自动登录到 Envi MMIS（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Envi MMIS 的集成，需要具有以下项：

- Azure AD 订阅
- 启用 Envi MMIS 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Envi MMIS
1. 配置和测试 Azure AD 单一登录

## <a name="adding-envi-mmis-from-the-gallery"></a>从库中添加 Envi MMIS
若要配置 Envi MMIS 与 Azure AD 的集成，需要从库中将 Envi MMIS 添加到托管 SaaS 应用的列表。

**若要从库中添加 Envi MMIS，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中键入“Envi MMIS”，在结果面板中选择“Envi MMIS”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Envi MMIS](./media/envimmis-tutorial/tutorial_envimmis_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，请基于一个名为“Britta Simon”的测试用户使用 Envi MMIS 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Envi MMIS 用户。 换句话说，需要建立 Azure AD 用户与 Envi MMIS 中相关用户之间的链接关系。

若要配置并测试 Envi MMIS 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Envi MMIS 测试用户](#create-an-envi-mmis-test-user)** - 在 Envi MMIS 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Envi MMIS 应用程序中配置单一登录。

**若要配置 Envi MMIS 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **Envi MMIS** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/envimmis-tutorial/tutorial_envimmis_samlbase.png)

1. 在“Envi MMIS 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![Envi MMIS 域和 URL 单一登录信息](./media/envimmis-tutorial/tutorial_envimmis_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://www.<CUSTOMER DOMAIN>.com/Account`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

1. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Envi MMIS 域和 URL 单一登录信息](./media/envimmis-tutorial/tutorial_envimmis_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： `https://www.<CUSTOMER DOMAIN>.com/Account`
     
    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Envi MMIS 客户端支持团队](mailto:support@ioscorp.com)以获取这些值。

1. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/envimmis-tutorial/tutorial_envimmis_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/envimmis-tutorial/tutorial_general_400.png)

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Envi MMIS 站点。

1. 单击“我的域”选项卡。

    ![配置单一登录“保存”按钮](./media/envimmis-tutorial/configure1.png)

1. 单击“编辑”。

    ![配置单一登录“保存”按钮](./media/envimmis-tutorial/configure2.png)

1. 选择“使用远程身份验证”复选框，然后从“身份验证类型”下拉列表中选择“HTTP 重定向”。

    ![配置单一登录“保存”按钮](./media/envimmis-tutorial/configure3.png)

1. 选择“资源”选项卡，然后单击“上传元数据”。

    ![配置单一登录“保存”按钮](./media/envimmis-tutorial/configure4.png)

1. 在“上传元数据”弹出窗口中，执行以下步骤：

    ![配置单一登录“保存”按钮](./media/envimmis-tutorial/configure5.png)

    a. 从“上传方式”下拉列表中选择“文件”选项。

    b. 选择“选择文件”图标即可从 Azure 门户上传已下载的元数据文件。

    c. 单击“确定” 。

1. 在上传下载的元数据文件以后，相关字段会自动进行填充。 单击“更新”。

    ![配置单一登录“保存”按钮](./media/envimmis-tutorial/configure6.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/envimmis-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/envimmis-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/envimmis-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/envimmis-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-envi-mmis-test-user"></a>创建 Envi MMIS 测试用户

若要使 Azure AD 用户能够登录到 Envi MMIS，必须将这些用户预配到 Envi MMIS 中。  
对于 Envi MMIS，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Envi MMIS 公司站点。

1. 单击“用户列表”选项卡。

    ![添加员工](./media/envimmis-tutorial/user1.png)

1. 单击“添加用户”按钮。

    ![添加员工](./media/envimmis-tutorial/user2.png)

1. 在“添加用户”部分中，执行以下步骤：

    ![添加员工](./media/envimmis-tutorial/user3.png)

    a. 在“用户名”文本框中，键入 Britta Simon 帐户的用户名（例如 **brittasimon@contoso.com**）。
    
    b. 在“名字”文本框中，键入 BrittaSimon 的名字（如“Britta”）。

    c. 在“姓氏”文本框中，键入 BrittaSimon 的姓氏（如“Simon”）。

    d. 在“职务”文本框中，键入用户的职务。
    
    e. 在“电子邮件地址”文本框中，键入 Britta Simon 帐户的电子邮件地址，例如 **brittasimon@contoso.com**。

    f. 在“SSO 用户名”文本框中，键入 Britta Simon 帐户的用户名（例如 **brittasimon@contoso.com**）。

    g. 单击“ **保存**”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Envi MMIS 的访问权限支持她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Envi MMIS，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Envi MMIS”。

    ![应用程序列表中的 Envi MMIS 链接](./media/envimmis-tutorial/tutorial_envimmis_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Envi MMIS 磁贴时，应当会自动登录到 Envi MMIS 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/envimmis-tutorial/tutorial_general_01.png
[2]: ./media/envimmis-tutorial/tutorial_general_02.png
[3]: ./media/envimmis-tutorial/tutorial_general_03.png
[4]: ./media/envimmis-tutorial/tutorial_general_04.png

[100]: ./media/envimmis-tutorial/tutorial_general_100.png

[200]: ./media/envimmis-tutorial/tutorial_general_200.png
[201]: ./media/envimmis-tutorial/tutorial_general_201.png
[202]: ./media/envimmis-tutorial/tutorial_general_202.png
[203]: ./media/envimmis-tutorial/tutorial_general_203.png


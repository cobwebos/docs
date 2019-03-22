---
title: 教程：Azure Active Directory 与 Infinite Campus 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Infinite Campus 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0ada3055a3347cb42179fddbba671f2f03f502d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "57885061"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>教程：Azure Active Directory 与 Infinite Campus 的集成

本教程介绍如何将 Infinite Campus 与 Azure Active Directory (Azure AD) 集成。

将 Infinite Campus 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Infinite Campus。
- 可以让用户使用其 Azure AD 帐户自动登录到 Infinite Campus（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>必备组件

若要配置 Azure AD 与 Infinite Campus 的集成，需要以下项：

- Azure AD 订阅
- 无限校园上单一登录已启用的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
- 最小值，你需要是 Azure Active Directory 管理员，并且具有校园产品安全角色的"学生信息系统 (SIS)"以完成配置。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Infinite Campus
2. 配置和测试 Azure AD 单一登录

## <a name="adding-infinite-campus-from-the-gallery"></a>从库中添加 Infinite Campus

若要配置 Infinite Campus 与 Azure AD 的集成，需要从库中将 Infinite Campus 添加到托管 SaaS 应用列表。

**若要从库添加 Infinite Campus，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Infinite Campus”，在结果面板中选择“Infinite Campus”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Infinite Campus](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于一个名为“Britta Simon”的测试用户使用 Infinite Campus 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Infinite Campus 用户。 换句话说，需要建立 Azure AD 用户与 Infinite Campus 中相关用户之间的关联关系。

若要配置并测试 Infinite Campus 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建无限校园测试用户](#creating-a-infinite-campus-test-user)** -Britta Simon 的对应在链接到用户的 Azure AD 表示形式的无限园区。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Infinite Campus 应用程序中配置单一登录。

**若要配置 Infinite Campus 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **Infinite Campus** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 上**基本 SAML 配置**部分中，如果您具有**服务提供程序元数据文件**导出无限校园，完成步骤通过 4.d，4.a，然后跳到步骤 11.c。 如果没有服务提供程序元数据文件，请跳到步骤 5。

    a. 单击“上传元数据文件”。

        ![image](common/b9_saml.png)

    b. 单击“文件夹徽标”来选择元数据文件并单击“上传”。

    ![图像](common/b9(1)_saml.png)

    c. 成功将元数据文件上传后，**标识符**和**回复 URL** 值会自动填充在“基本 SAML 配置”部分的文本框中，如下所示：

    ![图像](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. 在“登录 URL”文本框中，使用以下模式键入 URL（域将因托管模型而异）：`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

5. 如果没有服务提供程序元数据文件，请执行以下步骤（请注意，域因托管模型而异）：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinite Campus 域和 URL 单一登录信息](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击复制**图标**来复制应用联合元数据 URL，并将其粘贴到记事本 **。** 

    ![证书下载链接](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. 在“设置 Infinite Campus”部分中，当上传或利用 Azure 元数据文件/URL 时，使用以下值进行验证。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![Infinite Campus 配置](common/configuresection.png)

8. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 Infinite Campus。

9. 在菜单左侧，单击“系统管理”。

    ![管理](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. 导航到“用户安全性” > “SAML 管理” > “SSO 服务提供程序配置”。

    ![SAML](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. 在“SSO 服务提供程序配置”页上执行以下步骤：

    ![SSO](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. 选择“启用 SAML 单一登录”。
    
    b. 编辑**可选属性名称**包含**名称**
    
    c. 上**选择一个选项来检索标识提供者 (IDP) 服务器数据**部分中，选择**元数据 URL**，将粘贴**应用联合元数据 Url** （从上面的步骤 6) 中框中，然后再单击**同步**。

    d. 单击“服务提供程序元数据”链接，在计算机上保存服务提供程序元数据文件，并在“基本 SAML 配置”部分将其上传，以便在 Azure 门户中自动填充“标识符”和“回复 URL”值（若要上传和自动填充值，请参阅步骤 4；若要手动输入值，请参阅步骤 5）。

    e. 单击“同步”后，将在“SSO 服务提供程序配置”页中自动填充值。

    f. 单击“ **保存**”。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为“Britta Simon”的单个测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在中**用户名**字段中，键入**brittasimon\@yourcompanydomain.extension**  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-an-infinite-campus-test-user"></a>创建无限校园测试用户

Infinite Campus 采用以人口统计为中心的体系结构。 请联系 [Infinite Campus 支持团队](mailto:sales@infinitecampus.com)，以便在 Infinite Campus 平台中添加用户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Infinite Campus 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Infinite Campus”。

    ![配置单一登录](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Infinite Campus 磁贴时，应会自动登录到 Infinite Campus 应用程序。 如果你登录到您管理的 Azure AD 的同一浏览器中的无限校园应用程序，请确保您已登录到 Azure AD 测试用户。 有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png

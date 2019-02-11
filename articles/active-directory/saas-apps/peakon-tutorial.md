---
title: 教程：Azure Active Directory 与 Peakon 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Peakon 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 637988179228fbf0a6000de74a1185af98277e3c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178947"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>教程：Azure Active Directory 与 Peakon 集成

在本教程中，了解如何将 Peakon 与 Azure Active Directory (Azure AD) 集成。

Peakon 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Peakon。
- 可以让用户使用其 Azure AD 帐户自动登录到 Peakon（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Peakon 的集成，需要准备好以下各项：

- Azure AD 订阅
- 启用了 Peakon 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，否则请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Peakon
2. 配置和测试 Azure AD 单一登录

## <a name="adding-peakon-from-the-gallery"></a>从库中添加 Peakon

若要配置 Peakon 与 Azure AD 的集成，需要从库中将 Peakon 添加到托管 SaaS 应用列表。

若要从库中添加 Peakon，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Peakon”，在结果面板中选择“Peakon”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Peakon](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Peakon 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Peakon 用户。 换句话说，需要建立 Azure AD 用户与 Peakon 中相关用户之间的链接关系。

若要配置和测试 Peakon 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Peakon 测试用户](#creating-a-peakon-test-user) - 在 Peakon 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Peakon 应用程序中配置单一登录。

若要配置 Peakon 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中，在 Peakon 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”图标，打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![Peakon 域和 URL 单一登录信息](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://app.peakon.com/saml/<companyid>/metadata`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://app.peakon.com/saml/<companyid>/assert`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![Peakon 域和 URL 单一登录信息](./media/peakon-tutorial/tutorial_peakon_url1.png)

    在“登录 URL”文本框中，键入 URL `https://app.peakon.com/login`

    > [!NOTE]
    > 这些不是实际值。 本教程稍后将介绍如何使用实际的标识符和回复 URL 来更新这些值。

6. 在“SAML 签名证书”部分中，单击“下载”以下载“证书(原始)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. 在“设置 Peakon”部分中，根据要求复制相应的 URL。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![Peakon 配置](common/configuresection.png)

8. 在另一个 Web 浏览器窗口中，以管理员身份登录 Peakon。

9. 在页面左侧的菜单栏中，单击“ **配置**”，然后导航到“ **集成**”。

    ![配置](./media/peakon-tutorial/tutorial_peakon_config.png)

10. 在“集成”页上，单击“单一登录”。

    ![单一](./media/peakon-tutorial/tutorial_peakon_single.png)

11. 在“单一登录”部分中，单击“启用”。

    ![启用](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. 在“使用 SAML 的员工的单一登录”部分中，执行以下步骤：

    ![SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. 在“SSO 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。

    b. 在“SSO 注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

    c. 单击“选择文件”，将从 Azure 门户下载的证书上传到“证书”框中。

    d. 单击该 **图标**以复制“**实体 ID**”，并将其粘贴到 Azure 门户中“ **基本 SAML 配置**”部分的“ **标识符**”文本框中。 

    e. 单击该 **图标**以复制“**回复 URL (ACS)**”，并将其粘贴到 Azure 门户中“ **基本 SAML 配置**”部分的“ **回复 URL**”文本框中。 

    f. 单击“保存”

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-a-peakon-test-user"></a>创建 Peakon 测试用户

为使 Azure AD 用户能够登录到 Peakon，必须将其预配到 Peakon 中。  
就 Peakon 来说，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Peakon 公司站点。

2. 在页面左侧的菜单栏中，单击“ **配置**”，然后导航到“ **员工**”。

    ![员工](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. 单击页面右上角的“ **添加员工**”。

      ![添加员工](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. 在“新增员工”对话框页上，执行以下步骤：

     ![新增员工](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. 在“姓名”文本框中，键入名字“Britta”和姓氏“Simon”。

    b. 在“电子邮件”文本框中，键入电子邮件地址，如 Brittasimon@contoso.com。

    c. 单击“创建员工”。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Peakon 的访问权限支持其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Peakon”。

    ![配置单一登录](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Peakon 磁贴时，应该会自动登录 Peakon 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

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

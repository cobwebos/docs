---
title: 教程：Azure Active Directory 与 Workstars 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workstars 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 89b0ace19e7faea053cff0b3be2080f58dc0563c
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211156"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>教程：Azure Active Directory 与 Workstars 集成

在本教程中，了解如何将 Workstars 与 Azure Active Directory (Azure AD) 集成。

将 Workstars 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Workstars。
- 可让用户使用其 Azure AD 帐户自动登录到 Workstars（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 Workstars 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Workstars 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Workstars
2. 配置和测试 Azure AD 单一登录

## <a name="adding-workstars-from-the-gallery"></a>从库中添加 Workstars
要配置 Workstars 与 Azure AD 的集成，需要从库中将 Workstars 添加到托管 SaaS 应用列表。

**若要从库中添加 Workstars，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Workstars”，在结果面板中选择“Workstars”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Workstars](./media/workstars-tutorial/tutorial_workstars_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Workstars 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Workstars 用户。 换句话说，需要建立 Azure AD 用户与 Workstars 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Workstars 中“用户名”的值来建立此链接关系。

若要使用 Workstars 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Workstars 测试用户](#create-a-workstars-test-user)** - 在 Workstars 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Workstars 应用程序中配置单一登录。

**若要使用 Workstars 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在“Workstars”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/workstars-tutorial/tutorial_workstars_samlbase.png)

3. 在“Workstars 域和 URL”部分中，执行以下步骤：

    ![Workstars 域和 URL 单一登录信息](./media/workstars-tutorial/tutorial_workstars_url.png)

    a. 在“标识符”文本框中，键入 URL：`https://workstars.com`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际回复 URL 更新此值。 请联系 [Workstars 支持团队](https://support.workstars.com)获取此值。
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/workstars-tutorial/tutorial_workstars_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/workstars-tutorial/tutorial_general_400.png)

6. 在“Workstars 配置”部分，单击“配置 Workstars”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![Workstars 配置](./media/workstars-tutorial/tutorial_workstars_configure.png) 

7. 在另一浏览器窗口中，以管理员身份登录到 Workstars 公司站点。

8. 在主工具栏中，单击“设置”。

    ![Workstars 设置](./media/workstars-tutorial/tutorial_workstars_sett.png)

9. 转到“登录”、 > “设置”。

    ![Workstars 登录](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Workstars 设置](./media/workstars-tutorial/tutorial_workstars_settings.png)

10. 在“单一登录(SAML) - 设置”页上，执行以下步骤：
    
    ![Workstars saml](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. 在“标识提供者名称”文本框中，键入“Office 365”。

    b. 在“标识提供者实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    c. 复制已下载的证书文件的内容，并将其粘贴到“x509 证书”文本框中。 

    d. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
    
    e. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。 

    f. 选择“名称 ID”作为“电子邮件（默认）”。

    g. 单击“确认”。
    
> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/workstars-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/workstars-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/workstars-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/workstars-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
  
### <a name="create-a-workstars-test-user"></a>创建 Workstars 测试用户

本部分的内容是在 Workstars 中创建名为“Britta Simon”的用户。 请与 [Workstars 支持团队](https://support.workstars.com)协作，在 Workstars 平台中添加用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Workstars 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Workstars，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Workstars”。

    ![应用程序列表中的 Workstars 链接](./media/workstars-tutorial/tutorial_workstars_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Workstars”磁贴，即可自动登录到 Workstars 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workstars-tutorial/tutorial_general_01.png
[2]: ./media/workstars-tutorial/tutorial_general_02.png
[3]: ./media/workstars-tutorial/tutorial_general_03.png
[4]: ./media/workstars-tutorial/tutorial_general_04.png

[100]: ./media/workstars-tutorial/tutorial_general_100.png

[200]: ./media/workstars-tutorial/tutorial_general_200.png
[201]: ./media/workstars-tutorial/tutorial_general_201.png
[202]: ./media/workstars-tutorial/tutorial_general_202.png
[203]: ./media/workstars-tutorial/tutorial_general_203.png


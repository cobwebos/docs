---
title: 教程：Azure Active Directory 与 OrgChart Now 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 OrgChart Now 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67095435"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>教程：Azure Active Directory 与 OrgChart Now 集成

本教程介绍如何将 OrgChart Now 与 Azure Active Directory (Azure AD) 集成。
将 OrgChart Now 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 OrgChart Now。
* 可让用户使用其 Azure AD 帐户自动登录到 OrgChart Now（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 OrgChart Now 的集成，需要准备好以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 已启用 OrgChart Now 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* OrgChart Now 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>从库中添加 OrgChart Now

要配置 OrgChart Now 与 Azure AD 的集成，需要从库中将 OrgChart Now 添加到托管 SaaS 应用列表。

**若要从库中添加 OrgChart Now，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“OrgChart Now”，在结果面板中选择“OrgChart Now”，然后单击“添加”按钮添加应用程序。   

     ![结果列表中的 OrgChart Now](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 OrgChart Now 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 OrgChart Now 相关用户之间建立链接关系。

若要配置和测试 OrgChart Now 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 OrgChart Now 单一登录](#configure-orgchart-now-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 OrgChart Now 测试用户](#create-orgchart-now-test-user)** - 在 OrgChart Now 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 OrgChart Now 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“OrgChart Now”应用程序集成页上，选择“单一登录”。  

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分执行以下步骤：

    ![OrgChart Now 域和 URL 单一登录信息](common/idp-identifier.png)

    在“标识符”  文本框中，键入一个 URL：`https://sso2.orgchartnow.com`

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![image](common/both-preintegrated-signon.png)

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` 是从“设置 OrgChart Now”部分（本教程稍后会介绍）复制的“Azure AD 标识符”   。

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

7. 在“设置 OrgChart Now”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-orgchart-now-single-sign-on"></a>配置 OrgChart Now 单一登录

若要在 **OrgChart Now** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [OrgChart Now 支持团队](mailto:ocnsupport@officeworksoftware.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 OrgChart Now 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“OrgChart Now”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“OrgChart Now”。 

    ![应用程序列表中的 OrgChart Now 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-orgchart-now-test-user"></a>创建 OrgChart Now 测试用户

若要让 Azure AD 用户登录 OrgChart Now，必须将其预配到 OrgChart Now 中。 

1. OrgChart Now 支持在默认情况下启用的实时预配。 尝试访问 OrgChart Now 期间，如果尚不存在用户，则会创建一个新用户。 仅当 SSO 请求来自已识别的 IDP，并且在用户列表中未找到 SAML 断言中的电子邮件时，实时用户预配功能才会创建**只读**用户。 对于此自动预配功能，需要在 OrgChart Now 中创建标题为“常规”的访问组。  请遵循以下步骤创建访问组：

    a. 单击 UI 右上角的**齿轮**图标后，转到“管理组”选项。

    ![OrgChart Now 组](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. 选择“添加”图标，将组命名为“常规”，然后单击“确定”。    

    ![OrgChart Now 添加](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. 选择常规用户或只读用户能够访问的文件夹：

    ![OrgChart Now 文件夹](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **锁定**文件夹，以便只有管理员用户可以修改它们。 然后按“确定”。 

    ![OrgChart Now 锁定](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. 若要创建**管理员**用户和**读/写**用户，必须手动创建用户，以通过 SSO 获取对其特权级别的访问。 若要预配用户帐户，请执行以下步骤：

    a. 以安全管理员身份登录到 OrgChart Now。

    b.  单击右上角的“设置”，然后导航到“管理用户”。  

    ![OrgChart Now 设置](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. 单击“添加”并执行以下步骤： 

    ![OrgChart Now 管理](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * 在“用户 ID”文本框中输入用户 ID，例如 **brittasimon\@contoso.com**。

    * 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 brittasimon\@contoso.com。

    * 单击“添加”  。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“OrgChart Now”磁贴时，应会自动登录到设置了 SSO 的 OrgChart Now。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


---
title: 教程：将 Azure Active Directory 与 SAP Business ByDesign 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 SAP Business ByDesign 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091672"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>教程：将 Azure Active Directory 与 SAP Business ByDesign 集成

在本教程中，了解如何将 SAP Business ByDesign 与 Azure Active Directory (Azure AD) 集成。
将 SAP Business ByDesign 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 SAP Business ByDesign。
* 可以让用户使用其 Azure AD 帐户自动登录到 SAP Business ByDesign（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 SAP Business ByDesign 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用 SAP Business ByDesign 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP Business ByDesign 支持 SP 发起的 SSO 

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>从库中添加 SAP Business ByDesign

要配置 SAP Business ByDesign 与 Azure AD 的集成，需要从库中将 SAP Business ByDesign 添加到托管 SaaS 应用列表。

**若要从库中添加 SAP Business ByDesign，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“SAP Business ByDesign”，从结果面板中选择“SAP Business ByDesign”，然后单击“添加”按钮添加该应用程序    。

    ![结果列表中的 SAP Business ByDesign](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SAP Business ByDesign 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 SAP Business ByDesign 中相关用户之间建立链接关系。

若要配置和测试 SAP Business ByDesign 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SAP Business ByDesign 单一登录](#configure-sap-business-bydesign-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 SAP Business ByDesign 测试用户](#create-sap-business-bydesign-test-user)** - 在 SAP Business ByDesign 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 SAP Business ByDesign 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的“SAP Business ByDesign”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    ![SAP Business ByDesign 域和 URL 单一登录信息](common/sp-identifier.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<servername>.sapbydesign.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<servername>.sapbydesign.com`

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [SAP Business ByDesign 客户端支持团队](https://www.sap.com/products/cloud-analytics.support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. SAP Business ByDesign 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![image](common/edit-attribute.png)

6. 单击“编辑”图标以编辑名称标识符值   。

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. 在“管理用户声明”部分中，执行以下步骤：![图像](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. 选择“转换”作为“源”   。

    b. 在“转换”下拉列表中，选择“ExtractMailPrefix()”   。

    c. 从“参数 1”下拉列表中，选择要用于实现的用户属性  。 例如，如果想要使用 EmployeeID 作为唯一用户标识符并且已在 ExtensionAttribute2 中存储属性值，则选择 user.extensionattribute2。

    d. 单击“保存”  。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 SAP Business ByDesign”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>配置 SAP Business ByDesign 单一登录

1. 以管理员权限登录到 SAP Business ByDesign 门户。

2. 导航到“应用程序和用户管理常见任务”  ，单击“标识提供者”  选项卡。

3. 单击“新建标识提供者”，并选择从 Azure 门户下载的元数据 XML 文件  。 导入元数据时，系统会自动上传所需的签名证书和加密证书。

    ![配置单一登录](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. 若要在 SAML 请求中包括**断言使用者服务 URL**，请选择“包括断言使用者服务 URL”  。

5. 单击“激活单一登录”  。

6. 保存所做更改。

7. 单击“我的系统”  选项卡。

    ![配置单一登录](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. 在“Azure AD 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    ![配置单一登录](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. 指定员工是否可以手动在以下两者中选择：使用用户 ID 和密码登录，或者通过选择“手动选择标识提供者”  使用 SSO 登录。

10. 在“SSO URL”  部分中，指定员工应该用于登录到系统的 URL。
    在“发送给员工的 URL”下拉列表中，可以选择下列选项：

    **非 SSO URL**

    系统仅将普通的系统 URL 发送给员工。 员工不能使用 SSO 登录，必须改用密码或证书。

    **SSO URL**

    系统仅将 SSO URL 发送给员工。 员工可以使用 SSO 登录。 通过 IdP 重定向身份验证请求。

    **自动选择**

    如果 SSO 未处于活动状态，系统将向员工发送普通的系统 URL。 如果 SSO 处于活动状态，系统会检查员工是否有密码。 如果密码可用，系统会向员工发送 SSO URL 和非 SSO URL。 但是，如果员工没有密码，则仅向员工发送 SSO URL。

11. 保存所做更改。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。 例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP Business ByDesign 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“SAP Business ByDesign”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“SAP Business ByDesign”  。

    ![应用程序列表中的 SAP Business ByDesign 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-sap-business-bydesign-test-user"></a>创建 SAP Business ByDesign 测试用户

在本部分中，会在 SAP Business ByDesign 中创建一个名为“Britta Simon”的用户。 请协助 [SAP Business ByDesign 客户端支持团队](https://www.sap.com/products/cloud-analytics.support.html)将用户添加到 SAP Business ByDesign 平台。 

> [!NOTE]
> 请确保 NameID 值应与 SAP Business ByDesign 平台中的用户名字段匹配。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 SAP Business ByDesign 磁贴时，应当会自动登录到为其设置了 SSO 的 SAP Business ByDesign。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

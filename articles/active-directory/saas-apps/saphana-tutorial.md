---
title: 教程：Azure Active Directory 与 SAP HANA 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP HANA 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a34fe5637e895ea69b6fc4c277b7722b306c97
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "73161182"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>教程：将 Azure Active Directory 与 SAP HANA 集成

在本教程中，了解如何将 SAP HANA 与 Azure Active Directory (Azure AD) 集成。
将 SAP HANA 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 SAP HANA。
* 可让用户使用其 Azure AD 帐户自动登录到 SAP HANA（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

若要配置 Azure AD 与 SAP HANA 的集成，需要以下项：

- Azure AD 订阅
- 已启用单一登录 (SSO) 的 SAP HANA 订阅
- 一个在任何公共 IaaS、本地、Azure VM 或 Azure 中的 SAP 大型实例上运行的 HANA 实例
- XSA 管理 Web 接口以及安装在 HANA 实例上的 HANA Studio

> [!NOTE]
> 我们不建议使用 SAP HANA 的生产环境测试本教程中的步骤。 首先在应用程序的开发环境或过渡环境中测试集成，然后使用生产环境。

若要测试本教程中的步骤，请遵循以下建议：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了 SAP HANA 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* SAP HANA 支持“IDP”发起的 SSO 
* SAP HANA 支持“实时”用户预配 

## <a name="adding-sap-hana-from-the-gallery"></a>从库中添加 SAP HANA

若要配置 SAP HANA 与 Azure AD 的集成，需要从库中将 SAP HANA 添加到托管 SaaS 应用列表。

**若要从库中添加 SAP HANA，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入“SAP HANA”，在结果面板中选择“SAP HANA”，然后单击“添加”按钮添加该应用程序    。

     ![结果列表中的 SSAP HANA](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SAP HANA 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 SAP HANA 相关用户之间建立链接关系。

若要使用 SAP HANA 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 SAP HANA 单一登录](#configure-sap-hana-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **创建 SAP HANA 测试用户[ - 在 SAP HANA 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式](#create-sap-hana-test-user)** 。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要使用 SAP HANA 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“SAP HANA”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    ![SAP HANA 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中键入以下值：`HA100`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [SAP HANA 客户支持团队](https://cloudplatform.sap.com/contact.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. SAP HANA 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![image](common/edit-attribute.png)

6. 在“用户属性和声明”对话框中的“用户属性”部分，执行以下步骤：
 
    a. 单击“编辑图标”，打开“管理用户声明”对话框   。

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. 从“转换”列表中，选择“ExtractMailPrefix()”   。

    c. 从“参数 1”列表中，选择“user.mail”   。

    d. 单击“保存”  。

7. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>配置 SAP HANA 单一登录

1. 若要在 SAP HANA 端配置单一登录，请通过转到相应的 HTTPS 终结点登录到“HANA XSA Web 控制台”。 

    > [!NOTE]
    > 在默认配置中，URL 将请求重定向到登录屏幕，这需要经过身份验证的 SAP HANA 数据库用户的凭据。 登录的用户必须有权执行 SAML 管理任务。

2. 在 XSA Web 界面中，转到“SAML 标识提供者”。  选择屏幕底部的 **+** 按钮显示“添加标识提供者信息”窗格。  然后执行以下步骤：

    ![添加标识提供者](./media/saphana-tutorial/sap1.png)

    a. 在“添加标识提供者信息”  窗格中，将从 Azure 门户下载的元数据 XML 内容粘贴到“元数据”  框中。

    ![添加标识提供者设置](./media/saphana-tutorial/sap2.png)

    b. 如果 XML 文档的内容有效，则分析过程会提取“常规数据”屏幕区域中的“使用者、实体 ID 和颁发者”字段所需的信息。   此外，还会提取“目标”屏幕区域中的 URL 字段（例如，“基 URL”和“单一登录 URL (*)”   字段）所需的信息。

    ![添加标识提供者设置](./media/saphana-tutorial/sap3.png)

    c. 在“常规数据”屏幕区域的“名称”框中，输入新 SAML SSO 标识提供者的名称。  

    > [!NOTE]
    > SAML IDP 的名称是必需的，且必须唯一。 将 SAML 选作 SAP HANA XS 应用程序要使用的身份验证方法时，该名称会显示在可用的 SAML IDP 列表中。 例如，可以在 XS 项目管理工具的“身份验证”屏幕区域中执行此操作。 

3. 选择“保存”  保存 SAML 标识提供者的详细信息并将新 SAML IDP 添加到已知 SAML IDP 列表。

    ![“保存”按钮](./media/saphana-tutorial/sap4.png)

4. 在 HANA Studio 中“配置”选项卡的系统属性内，根据 **saml** 筛选设置。 然后将 **assertion_timeout** 从 **10 秒**调整为 **120 秒**。

    ![assertion_timeout 设置](./media/saphana-tutorial/sap7.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP HANA 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“SAP HANA”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入并选择“SAP HANA”。 

    ![应用程序列表中的 DSAP HANA 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-sap-hana-test-user"></a>创建 SAP HANA 测试用户

若要使 Azure AD 用户能够登录到 SAP HANA，必须在 SAP HANA 中预配这些用户。
SAP HANA 支持默认启用的“实时预配”  。

如果需要手动创建用户，请执行以下步骤：

>[!NOTE]
>可更改用户使用的外部身份验证。 他们可以使用 Kerberos 等外部系统进行身份验证。 有关外部标识的详细信息，请联系[域管理员](https://cloudplatform.sap.com/contact.html)。

1. 以管理员身份打开 [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)，并为 DB-User 启用 SAML SSO。

    ![创建用户](./media/saphana-tutorial/sap5.png)

2. 选中“SAML”  左侧隐藏的复选框，并选择“配置”链接。 

3. 选择“添加”以添加 SAML IDP。   选择相应的 SAML IDP，再选择“确定”。 

4. 添加“外部标识”（本例中为 BrittaSimon）或选择“任何”。   然后选择“确定”。 

   > [!Note]
   > 如果未选中“任何”复选框，则 HANA 中的用户名必须与域后缀前面的 UPN 中的用户名完全匹配。  （例如，BrittaSimon@contoso.com 在 HANA 中将会变成 BrittaSimon。）

5. 针对测试，请将所有“XS”  角色分配给该用户。

    ![分配角色](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > 请只授予适合用例的权限。

6. 保存用户。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 SAP HANA 磁贴时，应会自动登录到为其设置了 SSO 的 SAP HANA。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


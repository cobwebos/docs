---
title: 教程：Azure Active Directory 与 iPass SmartConnect 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 iPass SmartConnect 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bf8c7e76aa6dd69cc505be237e47d8e10e7e8db5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264856"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>教程：Azure Active Directory 与 iPass SmartConnect 集成

本教程介绍如何将 iPass SmartConnect 与 Azure Active Directory (Azure AD) 集成。
将 iPass SmartConnect 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 iPass SmartConnect。
* 可以让用户通过其 Azure AD 帐户自动登录到 iPass SmartConnect（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 iPass SmartConnect 的集成，需备齐以下项目：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* 启用了单一登录的 iPass SmartConnect 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* iPass SmartConnect 支持 **SP 和 IDP** 发起的 SSO
* iPass SmartConnect 支持**恰时**用户预配

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>从库中添加 iPass SmartConnect

要配置 iPass SmartConnect 与 Azure AD 的集成，需要从库中将 iPass SmartConnect 添加到托管 SaaS 应用列表。

**若要从库中添加 iPass SmartConnect，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“iPass SmartConnect”，在结果面板中选择“iPass SmartConnect”，然后单击“添加”按钮添加该应用程序。

     ![结果列表中的 iPass SmartConnect](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，我们基于名为“Britta Simon”的测试用户为 iPass SmartConnect 配置和测试 Azure AD 单一登录。
若要使单一登录有效，需要在 Azure AD 用户与 iPass SmartConnect 相关用户之间建立关联。

若要配置和测试 iPass SmartConnect 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 iPass SmartConnect 单一登录](#configure-ipass-smartconnect-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 iPass SmartConnect 测试用户](#create-ipass-smartconnect-test-user)** - 在 iPass SmartConnect 中创建 Britta Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

要为 iPass SmartConnect 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“iPass SmartConnect”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![iPass SmartConnect 域和 URL 单一登录信息](common/preintegrated.png)

5. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![iPass SmartConnect 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”文本框中，键入 URL：`https://om-activation.ipass.com/ClientActivation/ssolanding.go`

6. iPass SmartConnect 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![image](common/edit-attribute.png)

7. 在“用户属性”对话框的“用户声明”部分中，通过使用“编辑图标”编辑声明或使用“添加新声明”添加声明，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称 |  源属性|
    | ---------------| ----------|
    | firstName | user.givenname |
    | lastName | user.surname |
    | 电子邮件 | user.userprincipalname |
    | username | user.userprincipalname |
    | | |

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](common/new-save-attribute.png)

    ![图像](common/new-attribute-details.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 将“命名空间”留空。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“确定”

    g. 单击“ **保存**”。

8. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上。

    ![证书下载链接](common/metadataxml.png)

9. 在“设置 iPass SmartConnect”部分，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-ipass-smartconnect-single-sign-on"></a>配置 iPass SmartConnect 单一登录

若要在 **iPass SmartConnect** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [iPass SmartConnect 支持团队](mailto:help@ipass.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 iPass SmartConnect 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“iPass SmartConnect”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“iPass SmartConnect”。

    ![应用程序列表中的 iPass SmartConnect 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-ipass-smartconnect-test-user"></a>创建 iPass SmartConnect 测试用户

在本部分，我们将在 iPass SmartConnect 中创建一个名为 Britta Simon 的用户。 请与  [iPass SmartConnect 支持团队](mailto:help@ipass.com) 协作，将需要列入允许列表的用户或域添加到 iPass SmartConnect 平台中。 如果域是由团队添加，用户会自动预配到 iPass SmartConnect 平台。 使用单一登录前，必须先创建并激活用户。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

**若要在 SP 发起的流中测试该应用程序，请执行以下步骤：**

a. 在[此处](https://om-activation.ipass.com/ClientActivation/ssolanding.go)下载 Windows iPass SmartConnect 客户端。

![应用程序列表中的 iPass SmartConnect 链接](./media/ipasssmartconnect-tutorial/testing3.png)

b. 安装客户端并启动。

c. 单击“开始”。

![应用程序列表中的 iPass SmartConnect 链接](./media/ipasssmartconnect-tutorial/testing1.png) 

d. 输入包含域的 Azure 用户名。 单击“继续”。 随后会重定向到 Azure 登录页。

![应用程序列表中的 iPass SmartConnect 链接](./media/ipasssmartconnect-tutorial/testing2.png) 

e. 成功完成身份验证后，将启动客户端激活。 客户端将会激活。

**若要在 IdP 发起的流中测试该应用程序，请执行以下步骤：**

a. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)。

b. 单击“iPass SmartConnect”应用。

c. 将启动 SSA 页。单击“下载适用于 Windows 的应用”安装 iPass SmartConnect 客户端。

![应用程序列表中的 iPass SmartConnect 链接](./media/ipasssmartconnect-tutorial/testing4.png)

d. 安装后，客户端在首次启动时，会在接受条款和条件后自动启动激活。

e. 如果激活未启动，请单击 SSA 页上的“激活”按钮启动激活。

f. 客户端将会激活。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
---
title: 教程：Azure Active Directory 与 Signagelive 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Signagelive 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bff57635c87b77270e1bd20e04834dec132b2df6
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678338"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>教程：Azure Active Directory 与 Signagelive 集成

本教程介绍如何将 Signagelive 与 Azure Active Directory (Azure AD) 集成。
将 Signagelive 与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Signagelive。
* 可让用户使用其 Azure AD 帐户自动登录到 Signagelive（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Signagelive 的集成，需要以下项目：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 已启用 Signagelive 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Signagelive 支持 SP 发起的 SSO。

## <a name="add-signagelive-from-the-gallery"></a>从库中添加 Signagelive

若要配置 Signagelive 与 Azure AD 的集成，请先从库中将 Signagelive 添加到托管 SaaS 应用列表。

若要从库中添加 Signagelive，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中输入 **Signagelive**。 

     ![结果列表中的 Signagelive](common/search-new-app.png)

5. 在结果窗格中选择“Signagelive”，然后选择“添加”按钮添加该应用程序。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Signagelive 的 Azure AD 单一登录。
若要正常使用单一登录，需要在 Azure AD 用户与 Signagelive 相关用户之间建立链接。

若要配置和测试 Signagelive 的 Azure AD 单一登录，请先完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [配置 Signagelive 单一登录](#configure-signagelive-single-sign-on) - 在应用程序端配置单一登录设置。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Signagelive 测试用户](#create-a-signagelive-test-user) - 在 Signagelive 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Signagelive 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **Signagelive** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML”启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”部分执行以下步骤：

    ![Signagelive 域和 URL 单一登录信息](common/sp-signonurl.png)

    在“登录 URL”框中，输入使用以下模式的 URL：`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新该值。 若要获取该值，请联系 [Signagelive 客户端支持团队](mailto:support@signagelive.com)。 也可以参考 Azure 门户中的“基本 SAML 配置”部分显示的模式。

5. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以根据要求从给定的选项下载“证书(原始)”。 然后将证书保存在计算机上。

    ![证书下载链接](common/certificateraw.png)

6. 在“设置 Signagelive”部分，复制所需的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-signagelive-single-sign-on"></a>配置 Signagelive 单一登录

若要在 Signagelive 端配置单一登录，请将下载的“证书(原始)”以及从 Azure 门户复制的 URL 发送给 [Signagelive 支持团队](mailto:support@signagelive.com)。 他们将确保在两端正确设置 SAML SSO 连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户”对话框中执行以下步骤：

    ![“用户”对话框](common/user-properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，输入“brittasimon@yourcompanydomain.extension”。 例如，在本例中，可以输入“BrittaSimon@contoso.com”。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Signagelive 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Signagelive”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Signagelive”。

    ![应用程序列表中的 Signagelive 链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”按钮。 然后，在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中的“用户”列表内，选择“Britta Simon”。 然后单击屏幕底部的“选择”按钮。

6. 如果希望在 SAML 断言中使用角色值，请在“选择角色”对话框中，从列表中选择用户的相应角色。 接下来，单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="create-a-signagelive-test-user"></a>创建 Signagelive 测试用户

本部分需在 Signagelive 中创建名为“Britta Simon”的用户。 在  [Signagelive 支持团队](mailto:support@signagelive.com)的配合下，将用户添加到 Signagelive 平台。 必须先创建并激活用户，然后才能使用单一登录。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分，我们将使用“我的应用”门户测试 Azure AD 单一登录配置。

在“我的应用”门户中选择“Signagelive”磁贴时，应会自动登录到 Signagelive。 有关“我的应用”门户的详细信息，请参阅[什么是“我的应用”门户？](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


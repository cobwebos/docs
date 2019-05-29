---
title: 教程：Azure Active Directory 与 Trello 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Trello 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865792"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>教程：Azure Active Directory 与 Trello 集成

在本教程中，了解如何将 Trello 与 Azure Active Directory (Azure AD) 集成。
将 Trello 与 Azure AD 集成提供以下优势：

* 可在 Azure AD 中控制谁有权访问 Trello。
* 可让用户使用其 Azure AD 帐户自动登录到 Trello（单一登录）。
* 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Trello 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取[一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 已启用单一登录的 Trello 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Trello 支持 SP 和 IDP 发起的 SSO

* Trello 支持实时用户预配

## <a name="add-trello-from-the-gallery"></a>从库中添加 Trello

若要配置 Trello 与 Azure AD 的集成，需要从库中将 Trello 添加到托管 SaaS 应用列表。

若要从库中添加 Trello，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 依次选择“企业应用程序”、“所有应用程序”。  

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮  。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Trello**，在结果窗格中选择“Trello”。 

5. 选择“添加”按钮，添加该应用程序  。

     ![结果列表中的 Trello](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户来配置并测试 Trello 的 Azure AD 单一登录。

若要正常使用单一登录，需要在 Azure AD 用户与 Trello 中的相关用户之间建立关联。

若要配置和测试 Trello 的 Azure AD 单一登录，需要完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [配置 Trello 单一登录](#configure-trello-single-sign-on) - 在应用程序端配置单一登录设置。
3. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [创建 Trello 测试用户](#create-a-trello-test-user) - 在 Trello 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

> [!NOTE]
> 应从 Trello 获取 **\<企业\>** 数据域。 如果没有数据域值，请联系 [Trello 支持团队](mailto:support@trello.com)获取企业的数据域。

若要配置 Trello 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的 Trello 应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML”启用单一登录。  

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择“编辑”图标打开“基本 SAML 配置”对话框。   

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 若要将应用程序配置为 IDP 发起的模式，请在“基本 SAML 配置部分”中执行以下步骤： 

    ![Trello 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”框中，使用以下模式输入 URL：`https://trello.com/auth/saml/metadata` 

    b. 在“回复 URL”框中，使用以下模式输入 URL：`https://trello.com/auth/saml/consume/<enterprise>` 

5. 若要将应用程序配置为 SP 发起的模式，请选择“设置其他 URL”并执行以下步骤： 

    ![Trello 域和 URL 单一登录信息](common/metadata-upload-additional-signon.png)

    在“登录 URL”框中，使用以下模式输入 URL：`https://trello.com/auth/saml/login/<enterprise>` 

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Trello 客户端支持团队](mailto:support@trello.com)获取这些值。 也可以参考 Azure 门户中的“基本 SAML 配置”部分显示的模式。 

6. Trello 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。  在“使用 SAML 设置单一登录”页上，选择“编辑”按钮打开“用户属性”对话框。   

    ![“用户属性”对话框](common/edit-attribute.png)

7. 在“用户属性”对话框中的“用户声明”部分，按上图所示配置 SAML 令牌属性。   然后执行以下步骤：

    | 名称 |  源属性|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. 选择“添加新声明”打开“管理用户声明”对话框。  

    ![“用户声明”对话框](common/new-save-attribute.png)

    ![管理用户声明](common/new-attribute-details.png)

    b. 在“名称”框中，输入该行显示的属性名称。 

    c. 将“命名空间”留空。 

    d. 对于“源”，请选择“属性”。  

    e. 在“源属性”列表中，输入该行显示的属性值。 

    f. 选择“确定”。 

    g. 选择“保存”。 

8. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以根据自己的需要从给定的选项中下载“证书(Base64)”。     然后将证书保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

9. 在“设置 Trello”部分中，根据自己的需要复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-trello-single-sign-on"></a>配置 Trello 单一登录

若要在 Trello 端配置单一登录，请先将下载的“证书(Base64)”以及从 Azure 门户复制的 URL 发送给 [Trello 支持团队](mailto:support@trello.com)。  他们将确保在两端正确设置 SAML SSO 连接。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户”对话框中执行以下步骤： 

    ![“用户”对话框](common/user-properties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中，输入“brittasimon@yourcompanydomain.extension”。  例如，在本例中，可以输入“BrittaSimon@contoso.com”。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  

    d. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Trello 的权限，使她能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Trello”。   

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Trello”  。

    ![“应用程序”列表中的“Trello”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 选择“添加用户”按钮。  然后，在“添加分配”对话框中选择“用户和组”。  

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表内的“Britta Simon”  。 然后单击屏幕底部的“选择”按钮。 

6. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中选择用户的相应角色。  然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中选择“分配”按钮。  

### <a name="create-a-trello-test-user"></a>创建 Trello 测试用户

在本部分中，会在 Trello 中创建一个名为“Britta Simon”的用户。 Trello 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Trello 中尚不存在用户，则会在身份验证后创建一个新用户。

> [!NOTE]
> 如果需要手动创建用户，请联系  [Trello 支持团队](mailto:support@trello.com)。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分，我们将使用“我的应用”门户测试 Azure AD 单一登录配置。

在“我的应用”门户中选择“Trello”磁贴时，应会自动登录到 Trello。 有关“我的应用”门户的详细信息，请参阅[什么是“我的应用”门户？](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


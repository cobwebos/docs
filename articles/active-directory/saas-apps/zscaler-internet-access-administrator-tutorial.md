---
title: 教程：Azure Active Directory 与 Zscaler Internet Access Administrator 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Zscaler Internet Access Administrator 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ea555097-bf62-45dd-9b45-b75c50324a69
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 153c6517560614b8a1eb9c0241aefc121b391d19
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823583"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-internet-access-administrator"></a>教程：Azure Active Directory 与 Zscaler Internet Access Administrator 的集成

本教程介绍如何将 Zscaler Internet Access Administrator 与 Azure Active Directory (Azure AD) 集成。
将 Zscaler Internet Access Administrator 与 Azure AD 集成可带来以下优势：

* 可在 Azure AD 中控制谁有权访问 Zscaler Private Internet Administrator。
* 可让用户使用其 Azure AD 帐户自动登录到 Zscaler Internet Access Administrator（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Zscaler Internet Access Administrator 的集成，需准备好以下各项：

* 一个 Azure AD 订阅。 如果你没有 Azure AD 环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。
* Zscaler Internet Access Administrator 订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Zscaler Internet Access Administrator 支持 **IDP** 发起的 SSO

## <a name="adding-zscaler-internet-access-administrator-from-the-gallery"></a>从库中添加 Zscaler Internet Access Administrator

若要配置 Zscaler Internet Access Administrator 与 Azure AD 的集成，需要从库中将 Zscaler Internet Access Administrator 添加到托管 SaaS 应用列表。

**若要从库中添加 Zscaler Internet Access Administrator，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 **Zscaler Internet Access Administrator**，从结果面板中选择“Zscaler Internet Access Administrator”，然后单击“添加”按钮以添加该应用程序。

     ![结果列表中的 Zscaler Internet Access Administrator](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们基于名为 **Britta Simon** 的测试用户配置和测试 Zscaler Internet Access Administrator 的 Azure AD 单一登录。
要正常进行单一登录，需要建立 Azure AD 用户与 Zscaler Internet Access Administrator 中相关用户之间的链接关系。

若要配置和测试 Zscaler Internet Access Administrator 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Zscaler Internet Access Administrator 单一登录](#configure-zscaler-internet-access-administrator-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Zscaler Internet Access Administrator 测试用户](#create-zscaler-internet-access-administrator-test-user)** - 在 Zscaler Internet Access Administrator 中创建用户 Britta Simon 的对应用户，并将其链接到其 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Zscaler Internet Access Administrator 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Zscaler Internet Access Administrator”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![Zscaler Internet Access Administrator 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，根据要求键入 URL：

    | |
    |--|
    | `https://admin.zscaler.net` |
    | `https://admin.zscalerone.net` |
    | `https://admin.zscalertwo.net` |
    | `https://admin.zscalerthree.net` |
    | `https://admin.zscloud.net` |
    | `https://admin.zscalerbeta.net` |

    b. 在“回复 URL”文本框中，根据要求键入 URL：

    | |
    |--|
    | `https://admin.zscaler.net/adminsso.do` |
    | `https://admin.zscalerone.net/adminsso.do` |
    | `https://admin.zscalertwo.net/adminsso.do` |
    | `https://admin.zscalerthree.net/adminsso.do` |
    | `https://admin.zscloud.net/adminsso.do` |
    | `https://admin.zscalerbeta.net/adminsso.do` |

5. Zscaler Internet Access Administrator 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性和声明”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮打开“用户属性和声明”对话框。

    ![“属性”链接](./media/zscaler-internet-access-administrator-tutorial/tutorial_zscaler-internet_attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | 名称  | 源属性  |
    | ---------| ------------ |
    | 角色     | user.assignedroles |

    a. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](./common/new-save-attribute.png)
    
    ![图像](./common/new-attribute-details.png)

    b. 在“源属性”列表中，选择属性值。

    c. 单击“确定” 。

    d. 单击“ **保存**”。

    > [!NOTE]
    > 若要了解如何在 Azure AD 中配置角色，请单击[此处](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)

7. 在“使用 SAML 设置单一登录”页上，在“SAML 签名证书”部分中，单击“下载”以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

8. 在“设置 Zscaler Internet Access Administrator”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-zscaler-internet-access-administrator-single-sign-on"></a>配置 Zscaler Internet Access Administrator 单一登录

1. 在另一个 Web 浏览器窗口中，登录到 Zscaler Internet Access 管理员 UI。

2. 转到“管理”>“管理员管理”执行以下步骤，然后单击“保存”：

    ![管理](./media/zscaler-internet-access-administrator-tutorial/AdminSSO.png "Administration")

    a. 选中“启用 SAML 身份验证”。

    b. 单击“上传”，以上传从 Azure 门户的“公共 SSL 证书”中下载的 Azure SAML 签名证书。

    c. （可选）为了提高安全性，请添加**颁发者**详细信息以验证 SAML 响应的颁发者。

3. 在管理员 UI 中，执行以下步骤：

    ![管理](./media/zscaler-internet-access-administrator-tutorial/ic800207.png)

    a. 将鼠标悬停在左下角附近的“激活”菜单上。

    b. 单击“激活”。

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

在本部分，我们通过授予 Britta Simon 访问 Zscaler Internet Access Administrator 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”、“Zscaler Internet Access Administrator”。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，键入 **Zscaler Private Access Administrator** 并将其选中。

    ![应用程序列表中的“Zscaler Internet Access Administrator”链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-zscaler-internet-access-administrator-test-user"></a>创建 Zscaler Internet Access Administrator 测试用户

本部分的目的是在 Zscaler Internet Access Administrator 中创建名为 Britta Simon 的用户。 Zscaler Internet Access 不支持实时预配管理员 SSO。 你需要手动创建管理员帐户。
有关如何创建管理员帐户的步骤，请参阅 Zscaler 文档：

https://help.zscaler.com/zia/adding-admins

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Zscaler Internet Access Administrator”磁贴时，应当会自动登录到已为其设置了 SSO 的 Zscaler Internet Access 管理员 UI。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
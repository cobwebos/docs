---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Concur Travel and Expense 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Concur Travel and Expense 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373418"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Concur Travel and Expense 集成

在本教程中，了解如何将 Concur Travel and Expense 与 Azure Active Directory (Azure AD) 集成。 将 Concur Travel and Expense 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Concur Travel and Expense。
* 让用户使用其 Azure AD 帐户自动登录到 Concur Travel and Expense。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* Concur Travel and Expense 订阅。
* 在 Concur 用户帐户下的“公司管理员”角色。 若要测试自己是否有适当的访问权限，可访问 [Concur SSO 自助工具](https://www.concursolutions.com/nui/authadmin/ssoadmin)。 如果没有访问权限，请联系 Concur 支持部门或实现项目经理。 

## <a name="scenario-description"></a>方案描述

本教程配置并测试 Azure AD SSO。

* Concur Travel and Expense 支持 **IDP** 和 **SP** 发起的 SSO
* Concur Travel and Expense 支持在生产和实现环境中测试 SSO 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，这适用于下述三个区域中的每个区域：美国、EMEA 和中国。 因此，只能为一个租户中的每个区域配置一个实例。 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>从库中添加 Concur Travel and Expense

若要配置 Concur Travel and Expense 与 Azure AD 的集成，需从库中将 Concur Travel and Expense 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Concur Travel and Expense”   。
1. 从结果面板中选择“Concur Travel and Expense”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>为 Concur Travel and Expense 配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Concur Travel and Expense 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Concur Travel and Expense 相关用户之间建立链接关系。

若要配置和测试 Concur Travel and Expense 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Concur Travel and Expense SSO](#configure-concur-travel-and-expense-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Concur Travel and Expense 测试用户](#create-concur-travel-and-expense-test-user)** - 在 Concur Travel and Expense 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Concur Travel and Expense”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置  部分，应用程序在 **IDP** 发起的模块中进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”  按钮来保存配置。

    > [!NOTE]
    > “标识符(实体 ID)”和“URL (断言使用者服务 URL)”特定于区域。 请根据 Concur 实体的数据中心进行选择。 如果不知道 Concur 实体的数据中心，请联系 Concur 支持部门。 

5. 在“设置 SAML 单一登录”页上，单击与“用户属性”对应的编辑/笔形图标以编辑设置   。 唯一用户标识符需与 Concur 用户 login_id 匹配。 通常应将 **user.userprincipalname** 更改为 **user.mail**。

    ![编辑用户属性](common/edit-attribute.png)

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该元数据并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Concur Travel and Expense 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Concur Travel and Expense”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-concur-travel-and-expense-sso"></a>配置Concur Travel and Expense SSO

1. 若要在 **Concur Travel and Expense** 端配置单一登录，需要将下载的“联合元数据 XML”上传到 [Concur SSO 自助服务工具](https://www.concursolutions.com/nui/authadmin/ssoadmin)，然后使用具有“公司管理员”角色的帐户登录。  

1. 单击“添加”  。
1. 输入 IdP 的自定义名称，例如“Azure AD (US)”。 
1. 单击“上传 XML 文件”，  ，然后附加以前下载的**联合元数据 XML**。
1. 单击“添加元数据”，保存所做的更改。 

    ![Concur SSO 自助工具屏幕截图](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>创建 Concur Travel and Expense 测试用户

在本部分中，将在 Concur Travel and Expense 中创建名为 B.Simon 的用户。 与 Concur 支持团队合作，将用户添加到 Concur Travel and Expense 平台。 使用单一登录前，必须先创建并激活用户。 

> [!NOTE]
> B.Simon 的 Concur 登录 ID 需与 B.Simon 在 Azure AD 中的唯一标识符匹配。 例如，如果 B.Simon 的 Azure AD 唯一标识符为 `B.Simon@contoso.com`， 则 B.Simon 的 Concur 登录 ID 也需要是 `B.Simon@contoso.com`。 

## <a name="configure-concur-mobile-sso"></a>配置 Concur Mobile SSO
若要启用 Concur Mobile SSO，需向 Concur 支持团队提供**用户访问 URL**。 按以下步骤从 Azure AD 获取**用户访问 URL**：
1. 转到“企业应用程序” 
1. 单击“Concur Travel and Expense” 
1. 单击“属性” 
1. 复制**用户访问 URL**，将该 URL 提供给 Concur 支持部门

> [!NOTE]
> 用于配置 SSO 的自助选项不可用，因此请联系 Concur 支持团队，让其启用移动 SSO。 

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Concur Travel and Expense 磁贴时，应会自动登录到为其设置了 SSO 的 Concur Travel and Expense。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试集成 Concur Travel and Expense 和 Azure AD](https://aad.portal.azure.com/)


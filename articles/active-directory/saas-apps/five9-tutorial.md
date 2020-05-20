---
title: 教程：将 Azure Active Directory 与 Five9 Plus Adapter (CTI, Contact Center Agents) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Five9 Plus Adapter (CTI, Contact Center Agents) 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: dd4eab2d8e877cac468097c0a6ed6aa3cfb750b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102451"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>教程：将 Azure Active Directory 与 Five9 Plus Adapter (CTI, Contact Center Agents) 集成

本教程介绍如何将 Five9 Plus Adapter (CTI, Contact Center Agents) 与 Azure Active Directory (Azure AD) 集成。
将 Five9 Plus Adapter (CTI, Contact Center Agents) 与 Azure AD 集成具有以下好处：

* 可以在 Azure AD 中控制谁有权访问 Five9 Plus Adapter (CTI, Contact Center Agents)。
* 可以让用户使用其 Azure AD 帐户自动登录到 Five9 Plus Adapter (CTI, Contact Center Agents)（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Five9 Plus Adapter (CTI, Contact Center Agents) 的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Five9 Plus Adapter (CTI, Contact Center Agents) 单一登录的订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Five9 Plus Adapter (CTI, Contact Center Agents) 支持 IDP 发起的 SSO 

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>从库添加 Five9 Plus Adapter (CTI, Contact Center Agents)

若要配置 Five9 Plus Adapter (CTI, Contact Center Agents) 与 Azure AD 的集成，请从库将 Five9 Plus Adapter (CTI, Contact Center Agents) 添加到托管 SaaS 应用列表中。

若要从库添加 Five9 Plus Adapter (CTI, Contact Center Agents)，请执行以下步骤： 

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中，键入“Five9 Plus Adapter (CTI, Contact Center Agents)”，在结果面板中选择“Five9 Plus Adapter (CTI, Contact Center Agents)”，然后单击“添加按钮”，以添加该应用程序    。

     ![结果列表中的 Five9 Plus Adapter (CTI, Contact Center Agents)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分将基于名为 Britta Simon 的测试用户来配置并测试 Five9 Plus Adapter (CTI, Contact Center Agents) 的 Azure AD 单一登录  。
若要运行单一登录，需要在 Azure AD 用户与 Five9 Plus Adapter (CTI, Contact Center Agents) 中相关用户之间建立链接关系。

若要配置和测试 Five9 Plus Adapter (CTI, Contact Center Agents) 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Five9 Plus Adapter (CTI, Contact Center Agents) 单一登录](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** - 在应用程序端配置单一登录。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Five9 Plus Adapter (CTI, Contact Center Agents) 测试用户](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** - 在 Five9 Plus Adapter (CTI, Contact Center Agents) 创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Five9 Plus Adapter (CTI, Contact Center Agents) 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的 Five9 Plus Adapter (CTI, Contact Center Agents) 应用程序集成页中，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    ![Five9 Plus Adapter (CTI, Contact Center Agents) 域和 URL 单一登录信息](common/idp-intiated.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL： 
    
    |    环境      |       代码      |
    | :-- | :-- |
    | 适用于 Five9 Plus Adapter for Microsoft Dynamics CRM | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | 适用于 Five9 Plus Adapter for Zendesk | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | 适用于 Five9 Plus Adapter for Agent Desktop Toolkit | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. 在“回复 URL”文本框中，使用以下模式键入 URL： 

    |      环境     |      代码      |
    | :--                  | :--           |
    | 适用于 Five9 Plus Adapter for Microsoft Dynamics CRM | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | 适用于 Five9 Plus Adapter for Zendesk | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | 适用于 Five9 Plus Adapter for Agent Desktop Toolkit | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

7. 在“设置 Five9 Plus Adapter (CTI, Contact Center Agents)”部分，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>配置 Five9 Plus Adapter (CTI, Contact Center Agents) 单一登录

1. 若要在“Five9 Plus Adapter (CTI, Contact Center Agents)”端配置单一登录，需要将下载的“证书 (Base64)”和复制的相应 URL 发送到 [Five9 Plus Adapter (CTI, Contact Center Agents) 支持团队](https://www.five9.com/about/contact)。 此外，若要进一步配置 SSO，请根据适配器遵循以下步骤：

    a. “Five9 Plus Adapter for Agent Desktop Toolkit”管理员指南：[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. “Five9 Plus Adapter for Microsoft Dynamics CRM”管理员指南：[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. “Five9 Plus Adapter for Zendesk”管理员指南：[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

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

在本部分中，通过授予 Britta Simon 访问 Five9 Plus Adapter (CTI, Contact Center Agents) 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Five9 Plus Adapter (CTI, Contact Center Agents)”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Five9 Plus Adapter (CTI, Contact Center Agents)”。 

    ![应用程序列表中的 Five9 Plus Adapter (CTI, Contact Center Agents)](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>创建 Five9 Plus Adapter (CTI, Contact Center Agents) 测试用户

本部分在 Five9 Plus Adapter (CTI, Contact Center Agents) 中创建一个名为 Britta Simon 的用户。 通过 [Five9 Plus Adapter (CTI, Contact Center Agents) 支持团队](https://www.five9.com/about/contact)在 Five9 Plus Adapter (CTI, Contact Center Agents) 平台添加用户。 使用单一登录前，必须先创建并激活用户。 

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在“访问面板”中单击“Five9 Plus Adapter (CTI, Contact Center Agents)”磁贴时，应自动登录到为其设置 SSO 的 Five9 Plus Adapter (CTI, Contact Center Agents)。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


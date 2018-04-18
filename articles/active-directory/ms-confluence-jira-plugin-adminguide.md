---
title: Azure Active Directory SSO 插件的管理员指南 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Jira/Confluence 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: d34ff6021816c73fb064a3ce73b7fcf3ae22dbd1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="admin-guide-for-the-azure-active-directory-sso-plug-in"></a>Azure Active Directory SSO 插件的管理员指南

## <a name="overview"></a>概述

Azure Active Directory (Azure AD) 单一登录 (SSO) 插件可让 Microsoft Azure AD 客户使用其工作或学校帐户登录到基于 Atlassian Jira 和 Confluence Server 的产品。 此插件实施基于 SAML 2.0 的 SSO。

## <a name="how-it-works"></a>工作原理

当用户登录到 Atlassian Jira 或 Confluence 应用程序时，登录页上会显示“使用 Azure AD 登录”按钮。 当用户选择该按钮时，系统会要求他们使用 Azure AD 组织登录页（即工作或学校帐户）登录。

用户通过身份验证后，应该可以登录到应用程序。 如果他们已使用其工作或学校帐户的 ID 和密码进行身份验证，则会直接登录到应用程序。 

登录过程跨 Jira 和 Confluence。 如果用户已登录到 Jira 应用程序，并且 Confluence 已在同一个浏览器窗口中打开，则用户不需要提供另一应用的凭据。 

用户也可以通过工作或学校帐户下的“我的应用”进入 Atlassian 产品。 登录时，系统不会要求他们输入凭据。

> [!NOTE]
> 用户预配不是通过该插件完成的。

## <a name="audience"></a>目标受众

Jira 和 Confluence 管理员可以使用该插件来启用通过 Azure AD 进行的 SSO。

## <a name="assumptions"></a>假设

* 为 Jira 和 Confluence 实例启用了 HTTPS。
* 已在 Jira 或 Confluence 中创建用户。
* 在 Jira 或 Confluence 中为用户分配了角色。
* 管理员有权访问配置插件所需的信息。
* 还可以在公司网络外部使用 Jira 或 Confluence。
* 该插件只能与 Jira 和 Confluence 的本地版本配合工作。

## <a name="prerequisites"></a>先决条件

在安装插件之前，请注意以下信息：

* Jira 和 Confluence 安装在 Windows 64 位版本上。
* Jira 和 Confluence 版本已启用 HTTPS。
* 可在 Internet 上可用 Jira 和 Confluence。
* 为 Jira 和 Confluence 创建了管理员凭据。
* 为 Azure AD 创建了管理员凭据。
* 已在 Jira 和 Confluence 中禁用 WebSudo。

## <a name="supported-versions-of-jira-and-confluence"></a>支持的 Jira 和 Confluence 版本

该插件支持以下版本的 Jira 和 Confluence：

* Jira 核心和软件：6.0 到 7.2.0
* Jira Service Desk：3.0 到 3.2
* Confluence：5.0 至 5.10

## <a name="installation"></a>安装

若要安装插件，请执行以下步骤：

1. 以管理员身份登录到 Jira 或 Confluence 实例。
    
2. 转到 Jira/Confluence 管理控制台，并选择“加载项”。
    
3. 在 Atlassian Marketplace 中搜索“Microsoft SAML SSO 插件”。
 
   相应的插件版本随即显示在搜索结果中。
 
5. 选择插件和通用插件管理器 (UPM) 并安装。
 
插件安装后，会显示在“管理加载项”的“用户安装的加载项”部分。
    
## <a name="plug-in-configuration"></a>插件配置

在开始使用插件之前，必须对其进行配置。 选择插件，选择“配置”按钮，并提供配置详细信息。

下图显示了 Jira 和 Confluence 中的配置屏幕：
    
![插件配置屏幕](./media/ms-confluence-jira-plugin-adminguide/jira.png)

*   **元数据 URL**：用于从 Azure AD 获取联合元数据的 URL。
 
*   **标识符**：由 Azure AD 用来验证请求源的 URL。 此值映射到 Azure AD 中的“标识符”元素。 插件自动派生此 URL，其格式为 https://*<域:端口>*/。
 
*   **回复 URL**：启动 SAML 登录的标识提供者 (IdP) 中的回复 URL。 此值映射到 Azure AD 中的“回复 URL”元素。 插件自动派生此 URL，其格式为 https://*<域:端口>*/plugins/servlet/saml/auth。
 
*   **登录 URL**：启动 SAML 登录的 IdP 中的登录 URL。 此值映射到 Azure AD 中的“登录”元素。 插件自动派生此 URL，其格式为 https://*<域:端口>*/plugins/servlet/saml/auth。
 
*   **IdP 实体 ID**：IdP 使用的实体 ID。 解析元数据 URL 时会填充此框。
 
*   **登录 URL**：来自 IdP 的登录 URL。 解析元数据 URL 时会从 Azure AD 填充此框。
 
*   **注销 URL**：来自 IdP 的注销 URL。 解析元数据 URL 时会从 Azure AD 填充此框。
 
*   **X.509 证书**：IdP 的 X.509 证书。 解析元数据 URL 时会从 Azure AD 填充此框。
 
*   **登录按钮名称**：组织希望用户在登录页上看到的登录按钮名称。
 
*   **SAML 用户 ID 位置**：Jira 或 Confluence 用户 ID 预期在 SAML 响应中出现的位置。 此值可能出现在 **NameID** 或自定义属性名称中。
 
*   **属性名称**：用户 ID 预期所在的属性的名称。
 
*   **启用主领域发现**：如果公司使用基于 Active Directory 联合身份验证服务 (AD FS) 的登录，请选中此选项。
 
*   **域名**：使用基于 AD FS 的登录时要提供的域名。
 
*   **启用单一注销**：如果希望在用户从 Jira 或 Confluence 注销时你要从 Azure AD 注销，请选中此选项。

## <a name="troubleshooting"></a>故障排除

* **收到多个证书错误**：登录到 Azure AD，并删除针对应用提供的多个证书。 确保只存在一个证书。

* **Azure AD 中的证书即将过期**：加载项会处理证书的自动滚动更新。 在证书即将过期时，新证书应标记为活动，未使用的证书应被删除。 当用户尝试在此场景中登录到 Jira 时，插件会提取并保存新证书。

* **如何禁用 WebSudo（禁用安全管理员会话）**：
    
  * 对于 Jira，安全管理员会话（即，在访问管理功能之前需要确认密码）默认已启用。 如果想要在 Jira 实例中禁用此功能，请在 jira-config.properties 文件中指定以下行：`ira.websudo.is.disabled = true`
    
  * 对于 Confluence，请遵循 [Confluence 支持站点](https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html)中的步骤。

* **预期通过“元数据 URL”填充的字段不会填充**：
    
  * 检查 URL 是否正确。 检查是否已映射到正确的租户和应用 ID。
    
  * 在浏览器中输入此 URL，查看是否可以收到联合元数据 XML。

* **出现内部服务器错误**：查看安装日志目录中的日志。 如果当用户尝试使用 Azure AD SSO 登录时你收到了错误，可以向支持团队共享日志。

* **当用户尝试登录时出现“找不到用户 ID”错误**：在 Jira 或 Confluence 中创建用户 ID。

* **Azure AD 中出现“找不到应用”错误**：查看相应的 URL 是否已映射到 Azure AD 中的应用。

* **需要支持**：联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 该团队会在 24-48 个工作小时内做出响应。
    
  也可以通过 Azure 门户渠道向 Microsoft 提交支持票证。
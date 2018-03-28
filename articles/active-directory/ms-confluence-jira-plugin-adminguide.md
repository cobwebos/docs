---
title: Microsoft Azure Active Directory 单一登录插件管理员指南 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Microsoft Azure Active Directory single sign-on for JIRA 之间配置单一登录。
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
ms.openlocfilehash: af949d1db8af37a534a16364f9f0763479c436e4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-admin-guide"></a>Microsoft Azure Active Directory 单一登录插件管理员指南

## <a name="table-of-contents"></a>目录

1. **[概述](#overview)**
2. **[工作原理](#how-it-works)**
3. **[受众](#audience)**
4. **[假设](#assumptions)**
5. **[先决条件](#prerequisites)**
6. **[支持的 JIRA 和 CONFLUENCE 版本](#supported-versions-of-jira-and-confluence)**
7. **[安装](#installation)**
8. **[插件配置](#plugin-configuration)**
9. **[加载项配置屏幕中的字段说明：](#field-explanation-for-add---on-configuration-screen:)**
10. **[故障排除](#troubleshooting)**

## <a name="overview"></a>概述

借助这些加载项，Microsoft Azure AD 客户可以使用其组织用户名和密码登录到基于 Atlassian Jira 和 Confluence 服务器的产品。 此方法实施基于  SAML 2.0 的 SSO。

## <a name="how-it-works"></a>工作原理

当用户登录到 Atlassian Jira 或 Confluence 应用程序时，登录页上会显示“使用 Azure AD 登录”按钮。 当用户单击此按钮时，系统会要求他们使用 Azure AD 组织登录页登录。

用户通过身份验证后，应该可以登录到应用程序。 如果他们已使用组织 ID 和密码完成身份验证，则会直接登录到应用程序。 另请注意 JIRA 和 Confluence 中的登录方式。 如果用户已登录到 JIRA 应用程序，并且 Confluence 也在同一个浏览器窗口中打开，则用户只需登录一次，而不需要再次为其他应用提供凭据。 用户还可以使用 Azure 帐户通过“我的应用”访问 Atlassian 产品，并且在登录时，系统不会要求提供凭据。

> [!NOTE]
> 用户预配不是使用此加载项完成的。

## <a name="audience"></a>受众

计划使用此插件来启用基于 Azure AD 的 SSO 的 JIRA 和 Confluence 管理员。

## <a name="assumptions"></a>假设

* JIRA/Confluence 实例已启用 HTTPS
* 已在 JIRA/Confluence 中创建用户
* 在 JIRA/Confluence 中为用户分配了角色
* 管理员有权访问配置插件所需的信息
* 还可以在公司网络外部使用 JIRA/Confluence
* 加载项只适用于本地版本的 JIRA 和 Confluence

## <a name="prerequisites"></a>先决条件

在继续安装加载项之前，请注意以下先决条件：

* JIRA/Confluence 安装在 Windows 64 位版本上
* JIRA/Confluence 版本已启用 HTTPS
* 请注意以下“支持的版本”部分中所列的受支持插件版本。
* 可在 Internet 上使用 JIRA/Confluence。
* JIRA/Confluence 的管理员凭据
* Azure AD 的管理员凭据
* 应在 JIRA 和 Confluence 中禁用 WebSudo

## <a name="supported-versions-of-jira-and-confluence"></a>支持的 JIRA 和 Confluence 版本

目前支持以下 JIRA 和 Confluence 版本：

* JIRA 核心和软件：6.0 到 7.2.0
* JIRA 服务台：3.0 到 3.2
* Confluence：5.0 至 5.10

## <a name="installation"></a>安装

管理员应遵循以下步骤安装插件：

1. 以管理员身份登录到 JIRA/Confluence 实例
    
2. 转到“JIRA/Confluence 管理”并单击“加载项”。
    
3. 在 Atlassian Marketplace 中搜索“Microsoft SAML SSO 插件”
 
4. 相应的加载项版本将显示在搜索结果中
 
5. 选择所需的插件，然后，UPM 将会安装该插件。
 
6. 插件安装后，它会显示在“管理加载项”部分的“用户安装的加载项”部分
 
7. 在开始使用插件之前，必须先对它进行配置。
 
8. 单击该插件，随后会显示配置按钮。
 
9. 单击该按钮提供配置输入
    
## <a name="plugin-configuration"></a>插件配置

下图显示了 JIRA 和 Confluence 中的加载项配置屏幕
    
![加载项配置](./media/ms-confluence-jira-plugin-adminguide/jira.png)

### <a name="field-explanation-for-add-on-configuration-screen"></a>加载项配置屏幕中的字段说明：

1.   元数据 URL：用于从 Azure AD 获取联合元数据的 URL
 
2.   标识符：由 Azure AD 用来验证请求的源。 此值映射到 Azure AD 中的“标识符”元素。 插件会自动派生此值，其格式为 https://<域:端口>/
 
3.   回复 URL：在 IdP 中使用回复 URL 可以启动 SAML 登录。 此值映射到 Azure AD 中的“回复 URL”元素。 插件会自动派生此值，其格式为 https://<域:端口>/plugins/servlet/saml/auth
 
4.   登录 URL：在 IdP 中使用登录 URL 可以启动 SAML 登录。 此值映射到 Azure AD 中的“登录”元素。 插件会自动派生此值，其格式为 https://<域:端口>/plugins/servlet/saml/auth
 
5.   IdP 实体 ID：IdP 使用的实体 ID。 解析元数据 URL 时会填充此值。
 
6.   登录 URL：来自 IdP 的登录 URL。 解析元数据 URL 时会从 Azure AD 填充此值。
 
7.   注销 URL：来自 IdP 的注销 URL。 解析元数据 URL 时会从 Azure AD 填充此值。
 
8.   X.509 证书：IdP 的 X.509 证书。 解析元数据 URL 时会从 Azure AD 填充此值。
 
9.   登录按钮名称：将登录按钮命名为组织想要看到的名称。 在登录屏幕中的登录按钮上，用户会看到此文本。
 
10.   SAML 用户 ID 位置：用户 ID 预期在 SAML 响应中出现的位置。 此值可能出现在 NameID 或自定义属性名称中。 此 ID 必须是 JIRA/Confluence 用户 ID。
 
11.   属性名称：用户 ID 预期所在的属性的名称。
 
12.   启用主领域发现：如果公司使用基于 ADFS 的登录，请选中此标志。
 
13.   域名：如果使用基于 ADFS 的登录，请在此处提供域名。
 
14.   启用单一注销：如果希望在用户从 JIRA/Confluence 注销时你要从 Azure AD 注销，请选中此选项。

### <a name="troubleshooting"></a>故障排除

* 如果遇到多个证书错误
    
    * 登录到 Azure AD，并删除针对应用提供的多个证书。 确保只存在一个证书。

* Azure AD 中的证书即将过期。
    
    * 加载项会处理证书的自动滚动更新。 在证书即将过期时，新证书应标记为活动，未使用的证书应被删除。 当用户尝试在此场景中登录到 JIRA 时，加载项会提取新证书并将其保存在插件中。

* 如何禁用 WebSudo（禁用安全管理员会话）
    
    * JIRA：安全管理员会话（即，在访问管理功能之前需要确认密码）默认已启用。 如果想要在 JIRA 实例中禁用此功能，可以在 jira-config.properties 文件中指定以下行："ira.websudo.is.disabled = true"
    
    * Confluence：遵循 URL https://confluence.atlassian.com/doc/configuring-secure-administrator-sessions-218269595.html 中所述的步骤

* 预期通过“元数据 URL”填充的字段不会填充
    
    * 检查 URL 是否正确。 检查是否已映射到正确的租户和应用 ID。
    
    * 在浏览器中点击此 URL，查看是否可以收到联合元数据 XML。

* 内部服务器错误：
    
    * 认真查看安装日志目录中的日志。 如果当用户尝试使用 Azure AD SSO 登录时你收到了错误，可让他们提供日志，并让他们参阅本文档中下面所提供的支持信息。

* 当用户尝试登录时出现“找不到用户 ID”错误
    
    * 未在 JIRA/Confluence 中创建用户，请创建该用户。

* Azure AD 中出现“找不到应用”错误
    
    * 查看相应的 URL 是否已映射到 Azure AD 中的应用。

* 支持详细信息：通过 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)与我们取得联系。 我们将在 24-48 个工作小时内做出答复。
    
    * 也可以通过 Azure 门户渠道向 Microsoft 提交支持票证。
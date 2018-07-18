---
title: Azure Active Directory SSO 插件的常见问题解答 | Microsoft Docs
description: 获取有关在 Azure Active Directory 与 Jira/Confluence 之间配置单一登录的常见问题的解答。
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
ms.date: 05/31/2018
ms.author: jeedes
ms.openlocfilehash: eb7576c48d3836eec8051d849cefe4c5ec6658c9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699200"
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Azure Active Directory SSO 插件的常见问题解答

如果你有关于此插件的任何问题，请参阅下面的常见问题解答。

## <a name="what-does-the-plug-in-do"></a>此插件的功能是什么？

此插件为 Atlassian Jira（包括 Jira 核心、Jira 软件、Jira 服务台）和 Confluence 本地软件提供单一登录功能。 此插件作为标识提供者 (IdP) 与 Azure Active Directory (Azure AD) 配合工作。

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>此插件与哪些 Atlassian 产品配合工作？

此插件与 Jira 和 Confluence 的本地版本配合工作。

## <a name="does-the-plug-in-work-on-cloud-versions"></a>此插件是否支持云版本？

不会。 此插件仅支持 Jira 和 Confluence 的本地版本。

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>此插件支持 Jira 和 Confluence 的哪些版本？

此插件支持以下版本：

* Jira 核心和软件：6.0 到 7.8
* Jira Service Desk：3.0 到 3.2
* Confluence：5.0 至 5.10

## <a name="is-the-plug-in-free-or-paid"></a>此插件是免费的还是付费的？

它是一个免费加载项。

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>部署此插件后，是否需要重启 Jira 或 Confluence？

不需要重启。 可以立即开始使用此插件。

## <a name="how-do-i-get-support-for-the-plug-in"></a>如何获得对此插件的支持？

有关此插件所需的任何支持，可以联系 [Azure AD SSO 集成团队](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)。 该团队会在 24-48 个工作小时内做出响应。

也可以通过 Azure 门户渠道向 Microsoft 提交支持票证。

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>此插件是否支持 Jira 和 Confluence 的 Mac 或 Ubuntu 安装？

我们仅在 Jira 和 Confluence 的 64 位 Windows Server 安装上测试了此插件。

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>此插件是否可以与除 Azure AD 之外的 IdP 配合工作？

不会。 它仅可与 Azure AD 配合工作。

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>此插件与哪个版本的 SAML 配合工作？

它与 SAML 2.0 配合工作。

## <a name="does-the-plug-in-do-user-provisioning"></a>此插件是否执行用户预配？

不会。 此插件仅提供基于 SAML 2.0 的 SSO。 在进行 SSO 登录前，必须将用户预配到应用程序中。

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>此插件是否支持 Jira 和 Confluence 的群集版本？

不会。 此插件与 Jira 和 Confluence 的本地版本配合工作。

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>此插件是否可以与 Jira 和 Confluence 的 HTTP 版本配合工作？

不会。 此插件仅可与已启用 HTTPS 的安装配合工作。
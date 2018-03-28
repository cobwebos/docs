---
title: Microsoft Azure Active Directory 单一登录插件常见问题解答 | Microsoft Docs
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
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Microsoft Azure Active Directory 单一登录插件常见问题解答 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1.什么是 Microsoft SSO 加载项？

此加载项为 Atlassian 的 JIRA（包括 JIRA 核心、JIRA 软件、JIRA 服务台）和 Confluence 本地软件提供单一登录功能。 此加载项使用 Azure AD 作为 IdP。

## <a name="2-add-on-works-with-which-atlassian-products"></a>2.此加载项适用于哪些 Atlassian 产品？

到目前为止，此加载项适用于本地版本的 JIRA 和 Confluence。

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3.此加载项是否支持云版本？

不会。 仅支持本地版本的 JIRA 和 Confluence。

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4.支持 JIRA 和 Confluence 的哪些版本？

下面是支持的版本列表：

* JIRA 核心和软件：6.0 到 7.2.2 
* JIRA 服务台：3.0 到 3.2 
* Confluence：5.0 至 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5.此加载项是免费还是付费？

这是免费的加载项，可以从 Atlassian 市场安装。

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6.部署此加载项后，是否需要重启 JIRA/Confluence？

部署加载项后不需要重启。 可以在部署后立即开始使用此加载项。

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7.如何获取加载项支持？

请通过以下方式与我们联系：<email>。我们将在 <> 小时内答复。 也可以通过 Azure 门户渠道向 Microsoft 提交支持票证。 也可以致电我们：<Number>（工作日上午 <> 点到下午 <> 点）。

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8.此加载项是否支持 JIRA 和 Confluence 的 Mac 或 Ubuntu 安装？

我们仅在 JIRA 和 Confluence 的 64 位 Windows Server 安装上测试了此加载项。

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9.此加载项是否适用于 Azure AD 以外的其他 Idp？

不会。 此加载项仅适用于 Azure AD。

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10.此加载项适用于哪个版本的 SAML？

此加载项适用于 SAML 2.0。

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11.此加载项也一定要使用预配吗？

不会。 到目前为止，此加载项仅提供基于 SAML 2.0 的 SSO。 在 SSO 登录前，必须将用户预配到应用程序中。

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12.此加载项是否支持群集版本的 JIRA 和 Confluence？

不会。 此加载项适用于本地版本的 JIRA 和 Confluence。

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13.此插件是否适用于 HTTP 版本的 JIRA 和 Confluence？

不会。 此加载项仅适用于已启用 HTTPS 的安装。

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14.是否需要购买此加载项的许可证？

它是免费加载项。
---
title: "使用 Azure AD 应用程序代理登录本地应用程序时遇到问题 | Microsoft Docs"
description: "无法使用 Azure AD 应用程序代理登录与 Azure AD 集成的本地应用程序时所遇到的常见问题疑难解答"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e3db08e5615385cbbbc77972f3cc560e9de9957b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理登录本地应用程序时遇到问题

如果登录本地应用程序时遇到问题，可尝试使用以下步骤来解决问题。

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>我可以加载应用程序，但页面上的某些内容有损坏

以下文档可帮助你解决一些此类最常见问题。

  * [我可以访问我的应用程序，但应用程序页面未正常显示](https://docs.microsoft.com/azure/active-directory/application-proxy-page-appearance-broken-problem/)
  * [我可以访问我的应用程序，但应用程序加载时间太长](https://docs.microsoft.com/azure/active-directory/application-proxy-page-load-speed-problem/)
  * [我可以访问我的应用程序，但应用程序页面上的链接失效](https://docs.microsoft.com/azure/active-directory/application-proxy-page-links-broken-problem/)

## <a name="im-having-a-connectivity-problem-my-application"></a>应用程序出现连接问题
  以下文档可帮助你解决一些此类最常见问题。
  * [我不知道要为应用程序打开哪些端口](https://docs.microsoft.com/azure/active-directory/application-proxy-connectivity-ports-how-to/)
  * [由于应用程序的连接器组中不存在有效的连接器，因此出现了问题](https://docs.microsoft.com/azure/active-directory/application-proxy-connectivity-no-working-connector/)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>在管理门户中配置 AD 应用程序代理时遇到问题
  以下文档可帮助你解决一些此类最常见问题。
  * [配置应用程序代理应用程序时遇到问题](https://docs.microsoft.com/azure/active-directory/application-proxy-config-how-to/)
  * [我不知道如何配置应用程序代理应用程序的单一登录](https://docs.microsoft.com/azure/active-directory/application-proxy-config-sso-how-to/)
  * [我在管理门户中创建应用程序时遇到问题](https://docs.microsoft.com/azure/active-directory/application-proxy-config-problem/)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>设置应用程序的后端身份验证时遇到问题
  以下文档可帮助你解决一些此类最常见问题。
  * [我不知道如何配置 Kerberos 约束委派](https://docs.microsoft.com/azure/active-directory/application-proxy-back-end-kerberos-constrained-delegation-how-to/)
  * [我不知道如何使用 PingAccess 配置应用程序](https://docs.microsoft.com/azure/active-directory/application-proxy-back-end-ping-access-how-to/)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>登录应用程序时遇到问题
  以下文档可帮助你解决一些此类最常见问题。
  * [我收到了“无法访问此企业应用程序”错误](https://docs.microsoft.com/azure/active-directory/application-proxy-sign-in-bad-gateway-timeout-error/)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>应用程序代理程序连接器出现问题
  以下文档可帮助你解决一些此类最常见问题。
  * [安装应用程序代理程序连接器时出现问题](https://docs.microsoft.com/azure/active-directory/application-proxy-connector-installation-problem/)

## <a name="next-steps"></a>后续步骤
[如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)

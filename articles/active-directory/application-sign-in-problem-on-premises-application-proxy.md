---
title: 使用 Azure AD 应用程序代理登录本地应用程序时遇到问题 | Microsoft Docs
description: 无法使用 Azure AD 应用程序代理登录与 Azure AD 集成的本地应用程序时所遇到的常见问题疑难解答
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 9a60dc685fa23527aabaaea250c4584f33040b7c
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365615"
---
# <a name="problems-signing-in-to-an-on-premises-application-using-the-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理登录本地应用程序时遇到问题

如果登录本地应用程序时遇到问题，可尝试使用以下步骤来解决问题。

## <a name="i-can-load-my-application-but-something-on-the-page-looks-broken"></a>我可以加载应用程序，但页面上的某些内容有损坏

以下文档可帮助你解决一些此类最常见问题。

  * [我可以访问我的应用程序，但应用程序页面未正常显示](application-proxy-page-appearance-broken-problem.md)
  * [我可以访问我的应用程序，但应用程序加载时间太长](application-proxy-page-load-speed-problem.md)
  * [我可以访问我的应用程序，但应用程序页面上的链接失效](application-proxy-page-links-broken-problem.md)

## <a name="im-having-a-connectivity-problem-my-application"></a>应用程序出现连接问题
  以下文档可帮助你解决一些此类最常见问题。
  * [我不知道要为应用程序打开哪些端口](application-proxy-connectivity-ports-how-to.md)
  * [由于应用程序的连接器组中不存在有效的连接器，因此出现了问题](application-proxy-connectivity-no-working-connector.md)

## <a name="im-having-a-problem-configuring-the-azure-ad-application-proxy-in-the-admin-portal"></a>在管理门户中配置 AD 应用程序代理时遇到问题
  以下文档可帮助你解决一些此类最常见问题。
  * [配置应用程序代理应用程序时遇到问题](application-proxy-config-how-to.md)
  * [我不知道如何配置应用程序代理应用程序的单一登录](application-proxy-config-sso-how-to.md)
  * [我在管理门户中创建应用程序时遇到问题](application-proxy-config-problem.md)

## <a name="im-having-a-problem-setting-up-back-end-authentication-to-my-application"></a>设置应用程序的后端身份验证时遇到问题
  以下文档可帮助你解决一些此类最常见问题。
  * [我不知道如何配置 Kerberos 约束委派](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
  * [我不知道如何使用 PingAccess 配置应用程序](application-proxy-back-end-ping-access-how-to.md)

## <a name="im-having-a-problem-when-signing-in-to-my-application"></a>登录应用程序时遇到问题
  以下文档可帮助你解决一些此类最常见问题。
  * [我收到了“无法访问此企业应用程序”错误](application-proxy-sign-in-bad-gateway-timeout-error.md)

## <a name="im-having-a-problem-with-the-application-proxy-agent-connector"></a>应用程序代理程序连接器出现问题
  以下文档可帮助你解决一些此类最常见问题。
  * [安装应用程序代理程序连接器时出现问题](application-proxy-connector-installation-problem.md)

## <a name="next-steps"></a>后续步骤
[如何提供对本地应用程序的安全远程访问](manage-apps/application-proxy.md)

---
title: "如何向自定义开发的应用程序授予权限 | Microsoft Docs"
description: "如何使用 Azure AD 门户或 URL 参数向自定义开发的应用程序授予权限"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 922774c2482737537b64787ae473231ec1fbb68e
ms.contentlocale: zh-cn
ms.lasthandoff: 04/14/2017

---

<a id="how-to-grant-permissions-to-a-custom-developed-application" class="xliff"></a>

# 如何向自定义开发的应用程序授予权限

如果希望对应用提前授予许可，或遇到未同意应用的错误，请尝试执行以下这些步骤。

<a id="how-to-perform-admin-consent-for-your-application" class="xliff"></a>

## 如何为应用程序执行管理员许可

这等同于向组织中所有用户的应用程序授予许可。

1. 以“全局管理员”身份导航到“应用注册”边栏选项卡，然后选择应用。

2. 选择“所需的权限”，最后点击边栏选项卡顶部的“授予权限”按钮。

此外，也可以通过提供应用的配置并附加 *&prompt=admin\_consent* 向 *login.microsoftonline.com* 来构造请求。 使用管理员凭据登录后，该应用即已授予所有用户的同意。

<a id="how-to-force-user-consent-for-your-application" class="xliff"></a>

## 如何为应用程序强制执行用户同意

* 附加到身份验证请求 *&prompt=consent*，这要求最终用户在每次进行身份验证时同意。

<a id="next-steps" class="xliff"></a>

## 后续步骤

[同意并将应用集成到 AzureAD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications)

[同意并为 AzureAD v2.0 聚合应用授予权限](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)


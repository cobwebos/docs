---
title: "对应用程序执行许可时发生的意外错误 | Microsoft Docs"
description: "介绍对应用程序进行许可期间可能发生的错误，以及解决这些错误的方法"
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
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 21dabdcf0697b38ad3598ee8126a1bd62007efb5
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017

---

# <a name="unexpected-error-when-performing-consent-to-an-application"></a>对应用程序执行许可时发生的意外错误

本文介绍对应用程序进行许可期间可能发生的错误。 如果要对不包含任何错误消息的意外许可提示进行故障排除，请参阅 [Azure AD 的身份验证方案](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)。

与 Azure Active Directory 集成的许多应用程序需要获取访问其他资源的权限才能正常工作。 当这些资源也与 Azure Active Directory 集成时，通常使用通用许可框架来请求访问它们的权限。 

这将显示许可提示，此情形通常发生在首次使用应用程序时，但也可能发生在后续使用应用程序时。

对于用户而言，必须满足一些条件才能对应用程序所需的权限进行许可。 如果不满足这些条件，可能会发生各种错误。 其中包括：

## <a name="requesting-not-authorized-permissions-error"></a>请求未授予的权限错误
* **AADSTS90093：**&lt;clientAppDisplayName&gt; 正在请求一个或多个无权授予的权限。 请与管理员联系，他/她可代表你对此应用程序进行许可。

当非公司管理员用户尝试使用的应用程序请求只有管理员才能授予的权限时，会发生此错误。 此错误可通过管理员代表其组织授予访问此应用程序的权限进行解决。

## <a name="policy-prevents-granting-permissions-error"></a>策略阻止权限授予错误
* **AADSTS90093：**&lt;tenantDisplayName&gt; 管理员设置的策略阻止授予 &lt;应用名称&gt; 请求的权限。 请与 &lt;tenantDisplayName&gt; 管理员联系，他/她可代表你授予对此应用的权限。

当公司管理员关闭用户对应用程序进行许可的能力，非管理员用户尝试使用的应用程序需要许可时，会发生此错误。 此错误可通过管理员代表其组织授予访问此应用程序的权限进行解决。

## <a name="intermittent-problem-error"></a>不稳定问题错误
* **AADSTS90090：**我们似乎遇到了不稳定问题，它记录了尝试授予 &lt;clientAppDisplayName&gt; 的权限。 请稍后重试。

此错误表明服务端已发生不稳定问题。 可通过重新尝试对应用程序进行许可来解决此问题。

## <a name="resource-not-available-error"></a>资源不可用错误
* **AADSTS65005：**&lt;clientAppDisplayName&gt; 应用已请求访问不可用的 &lt;resourceAppDisplayName&gt; 资源的权限。 

请与应用程序开发人员联系。

##  <a name="resource-not-available-in-tenant-error"></a>租户中资源不可用错误
* **AADSTS65005：**&lt;clientAppDisplayName&gt; 正在请求访问组织 &lt;tenantDisplayName&gt; 中不可用的 &lt;resourceAppDisplayName&gt; 资源。 

确保此资源可用，或与 &lt;tenantDisplayName&gt; 管理员联系。

## <a name="permissions-mismatch-error"></a>权限不匹配错误
* **AADSTS65005：**应用已请求对访问 &lt;resourceAppDisplayName&gt; 资源进行许可。 由于此请求与应用注册期间预配置应用的方式不匹配，该请求失败。 与应用供应商联系。**

在以下情况下会发生所有这些错误：用户尝试许可的应用程序请求对资源应用程序的访问权限，该资源应用程序在组织目录（租户）中找不到。 出现这种情况的原因可能有很多个：

-   客户端应用程序开发人员错误地配置了应用程序，从而导致应用程序请求访问无效的资源。 在这种情况下，为了解决此问题，应用程序开发人员必须更新客户端应用程序的配置。

-   代表目标资源应用程序的服务主体在组织中不存在，或过去存在但已删除。 若要解决此问题，必须在组织中预配资源应用程序的服务主体，以便客户端应用程序可以请求其权限。 根据应用程序的类型，可以通过多种方式进行此操作，包括：

    -   获取资源应用程序（Microsoft 已发布的应用程序）的订阅

    -   对资源应用程序进行许可

    -   通过 Azure 门户授予应用程序权限

    -   从 Azure AD 应用程序库添加应用程序

## <a name="next-steps"></a>后续步骤 

[Azure Active Directory（v1 终结点）中的应用、权限和许可](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Azure Active Directory（v2.0 终结点）中的范围、权限和许可](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)




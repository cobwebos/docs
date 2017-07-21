---
title: "如何为自定义开发的应用程序查找所需的特定 API | Microsoft Docs"
description: "如何配置访问自定义开发的 Azure AD 应用程序中的特定 API 所需的权限"
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
ms.openlocfilehash: 98c247a03132ad1f5bf158d257cd5ba764162112
ms.contentlocale: zh-cn
ms.lasthandoff: 04/18/2017

---

<a id="how-to-find-a-specific-api-needed-for-a-custom-developed-application" class="xliff"></a>

# 如何为自定义开发的应用程序查找所需的特定 API

访问 API 需要配置访问作用域和访问角色。 如果想要将资源应用程序 Web API 公开至客户端应用程序，需要为 API 配置访问作用域和访问角色。 如果想要让客户端应用程序访问 Web API，需要配置权限以访问应用注册中的 API。

<a id="configuring-a-resource-application-to-expose-web-apis" class="xliff"></a>

## 将资源应用程序配置为公开 Web API

当 Web API 公开后，将权限添加到应用注册时，API 将显示在“选择 API”列表中。 若要添加访问作用域，请按照[将访问作用域添加到资源应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application)中概述的步骤操作。

<a id="configuring-a-client-application-to-access-web-apis" class="xliff"></a>

## 将客户端应用程序配置为访问 Web API

在将权限添加到应用注册时，可**添加 API 访问**到已公开的 Web API。 若要访问 Web API，请按照[添加凭据或权限以访问 Web API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis)中概述的步骤操作。

<a id="next-steps" class="xliff"></a>

## 后续步骤

-   [将应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [了解 Azure Active Directory 应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)




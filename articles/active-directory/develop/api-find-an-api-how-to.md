---
title: 如何为自定义开发的应用程序查找所需的特定 API | Microsoft Docs
description: 如何配置访问自定义开发的 Azure AD 应用程序中的特定 API 所需的权限
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c223df28f8082125d48f1e3619088de5cf2687
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74844594"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>如何为自定义开发的应用程序查找所需的特定 API

访问 API 需要配置访问作用域和访问角色。 如果想要将资源应用程序 Web API 公开至客户端应用程序，需要为 API 配置访问作用域和访问角色。 如果想要让客户端应用程序访问 Web API，需要配置权限以访问应用注册中的 API。

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>将资源应用程序配置为公开 Web API

当 Web API 公开后，将权限添加到应用注册时，API 会显示在“选择 API”列表中。 若要添加访问作用域，请按照[配置应用程序以公开 Web api](quickstart-configure-app-expose-web-apis.md)中所述的步骤进行操作。

## <a name="configuring-a-client-application-to-access-web-apis"></a>将客户端应用程序配置为访问 Web API

在将权限添加到应用注册时，可**添加 API 访问**到已公开的 Web API。 若要访问 web Api，请按照[配置客户端应用程序以访问 Web api](quickstart-configure-app-access-web-apis.md)中所述的步骤进行操作。

## <a name="next-steps"></a>后续步骤

- [了解 Azure Active Directory 应用程序清单](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)

---
title: Azure Active Directory 中的单租户和多租户应用
description: 了解 Azure AD 中的单租户和多租户应用的功能和差异。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8db590c8c79db9bc47edbbef69a11d3b0e4b51f6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540348"
---
# <a name="tenancy-in-azure-active-directory"></a>Azure Active Directory 中的租户

Azure Active Directory (Azure AD) 将用户和应用之类的对象组织到称为“租户”的组中。 租户允许管理员针对组织中的用户以及组织拥有的应用设置策略，以满足其安全和运营策略。 

## <a name="who-can-sign-in-to-your-app"></a>谁可以登录到你的应用？

在开发应用时，在 [Azure 门户](https://portal.azure.com)中注册应用期间，开发人员可以选择将其应用配置为单租户的还是多租户的。
* 单租户应用仅可在它们在其中注册的租户（也称为宿主租户）中使用。
* 多租户应用可供其宿主租户以及其他租户中的用户使用。

在 Azure 门户中，可以通过如下所述设置受众来将应用配置为单租户或多租户的。

| 目标受众 | 单/多租户 | 谁可以登录 | 
|----------|--------| ---------|
| 仅此目录中的帐户 | 单租户 | 目录中的所有用户和来宾帐户都可以使用应用程序或 API。<br>*如果目标受众在组织内部，请使用此选项。* |
| 任何 Azure AD 目录中的帐户 | 多租户 | 拥有 Microsoft 工作或学校帐户的所有用户和来宾都可以使用应用程序或 API。 这包括使用 Office 365 的学校和企业。<br>*如果目标受众是企业或教育行业客户，请使用此选项。* |
| 任何 Azure AD 目录中的帐户以及个人 Microsoft 帐户（例如 Skype、Xbox、Outlook.com） | 多租户 | 拥有工作或学校帐户或者个人 Microsoft 帐户的所有用户都可以使用应用程序或 API。 这包括使用 Office 365 的学校和企业以及用来登录 Xbox 和 Skype 等服务的个人帐户。<br>*若要面向最广泛的 Microsoft 帐户，请使用此选项。* | 

## <a name="best-practices-for-multi-tenant-apps"></a>适用于多租户应用的最佳做法

由于 IT 管理员可能会在其租户中设置大量的不同策略，因此，构建优秀的多租户应用可能很难。 如果你选择构建多租户应用，请遵循以下最佳做法：

* 在配置了[条件访问策略](conditional-access-dev-guide.md)的租户中测试应用。
* 遵循最小用户访问权限的原则，确保应用只请求它实际需要的权限。 避免请求需要管理员同意的权限，因为这可能会完全阻止某些组织中的用户访问应用。 
* 为作为应用的一部分公开的任何权限提供合适的名称和说明。 这可帮助用户和管理员了解当他们尝试使用应用的 API 时他们要同意什么。 有关详细信息，请参阅[权限指南](v1-permissions-and-consent.md)中的最佳做法部分。

## <a name="next-steps"></a>后续步骤

* [如何将应用转换为多租户应用](howto-convert-app-to-be-multi-tenant.md)

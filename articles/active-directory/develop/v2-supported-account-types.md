---
title: 支持的帐户类型 - Microsoft 标识平台 | Azure
description: 有关受众的概念文档以及应用程序中受支持的帐户类型
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d19381094e027bd567ffc503d32f9212ef56a948
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82583102"
---
# <a name="supported-account-types"></a>支持的帐户类型

本文介绍了 Microsoft 标识平台应用程序支持的帐户类型（有时称为“受众”）。

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>公有云中的帐户类型

在 Microsoft Azure 公有云中，大多数类型的应用均可通过任何受众登录用户：

- 如果你正在编写业务线 (LOB) 应用程序，则可以在自己的组织中登录用户。 此类应用程序有时称为“单租户”。
- 如果你是 ISV，可以编写一个应用程序来登录用户：

  - 在任何组织中。 这样的应用程序称为“多租户”Web 应用程序。 你有时会看到它使用工作或学校帐户登录用户。
  - 使用其工作或学校或个人 Microsoft 帐户。
  - 仅具有个人 Microsoft 帐户。
    
- 如果你正在编写企业到消费者的应用程序，你还可以使用 Azure Active Directory B2C （Azure AD B2C）通过其社交标识登录用户。

## <a name="account-type-support-in-authentication-flows"></a>身份验证流中的帐户类型支持

某些帐户类型不能与某些身份验证流程一起使用。 例如，在桌面、UWP 或守护程序应用程序中：

- 守护程序应用程序只能与 Azure AD 组织配合使用。 尝试使用守护程序应用程序来处理 Microsoft 个人帐户并无意义。 将永远不会授予管理员许可。
- 只能将集成身份验证流用于工作或学校帐户（在你的组织或任何组织中均可）。 集成 Windows 身份验证适用于域帐户，并且需要将计算机加入域或加入 Azure AD。 对于个人 Microsoft 帐户，此流程并无意义。
- [资源所有者密码凭据授予](./v2-oauth-ropc.md)（用户名/密码）不能与个人 Microsoft 帐户一起使用。 个人 Microsoft 帐户要求用户在每次登录时都同意访问个人资源。 这就是此行为与非交互式流不兼容的原因。
- 设备代码流不能与个人 Microsoft 帐户一起工作。

## <a name="account-types-in-national-clouds"></a>国家/地区云中的帐户类型

应用还可以在[国家/地区云](authentication-national-cloud.md)中登录用户。 但是，这些云不支持 Microsoft 个人帐户。 这就是将这些云的受支持的帐户类型减少到你的组织（单租户）或任何组织（多租户应用程序）的原因。

## <a name="next-steps"></a>后续步骤

- 了解有关 [Azure Active Directory 中的租赁](./single-and-multi-tenant-apps.md)的详细信息。
- 了解有关[国家/地区云](./authentication-national-cloud.md)的详细信息。

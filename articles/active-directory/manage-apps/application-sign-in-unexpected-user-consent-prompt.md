---
title: 登录到应用程序时出现的意外许可提示 | Microsoft Docs
description: 如何对用户遇到的与 Azure AD 集成的应用程序出现意外许可提示这一情形进行故障排除
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9559e22a8396de93bbe46f20b76855d9ff00f753
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "84763714"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>登录到应用程序时出现的意外许可提示

许多与 Azure Active Directory 集成的应用程序都需要各种资源的权限才能运行。 当这些资源也与 Azure Active Directory 集成时，使用 Azure AD 许可框架请求其访问权限。 

这会导致首次使用应用程序时显示许可提示，这通常是一次性操作。 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>用户看到许可提示的情况

可能在各种情况下看到其他提示：

* 应用程序所需的权限集已更改。

* 最初对应用程序进行许可的用户不是管理员，现在其他（非管理员）用户首次使用该应用程序。

* 最初对应用程序进行许可的用户是管理员，但他们未代表整个组织进行许可。

* 最初授予许可后，应用程序使用[增量许可和动态许可](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent)请求其他权限。 这通常会在以下情况下使用：应用程序的可选功能需要超出基线功能所需权限以外的附加权限。

* 已在最初授予许可后将其吊销。

* 开发人员已对应用程序作了如下配置：每次使用时，都需要许可提示（注意：这并非最佳做法）。

## <a name="next-steps"></a>后续步骤

-   [Azure Active Directory（v1.0 终结点）中的应用、权限和许可](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Azure Active Directory（v2.0 终结点）中的范围、权限和许可](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)



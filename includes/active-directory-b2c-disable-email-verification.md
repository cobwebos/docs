---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126740"
---
## <a name="disable-email-verification"></a>禁用电子邮件验证

默认情况下，Azure Active Directory B2C （Azure AD B2C）验证客户的本地帐户的电子邮件地址（使用电子邮件地址或用户名注册的用户的帐户）。 Azure AD B2C 通过要求客户在注册过程中对电子邮件地址进行验证，来确保电子邮件地址有效。 它还可防止恶意执行组件使用自动过程在您的应用程序中生成欺诈帐户。

某些应用程序开发人员希望在注册过程中跳过电子邮件验证，而让客户在以后验证其电子邮件地址。 要支持此功能，可将 Azure AD B2C 配置为禁用电子邮件验证。 这样做会产生更平稳的注册过程，并使开发人员能够灵活地区分已经验证其电子邮件地址的客户。

> [!WARNING]
> 在注册过程中禁用电子邮件验证可能会导致垃圾邮件。 如果禁用默认 Azure AD B2C 提供的电子邮件验证，则建议你实现替换验证系统。

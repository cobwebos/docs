---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126740"
---
## <a name="disable-email-verification"></a>禁用电子邮件验证

默认情况下，Azure Active Directory B2C (Azure AD B2C) 将验证客户的本地帐户（使用电子邮件地址或用户名注册的用户的帐户）的电子邮件地址。 Azure AD B2C 通过要求客户在注册过程中验证电子邮件地址来确保电子邮件地址有效。 它还可以防止恶意参与者使用自动进程在应用程序中生成欺诈帐户。

某些应用程序开发人员愿意在注册过程中跳过电子邮件验证，而让客户在以后验证电子邮件地址。 要支持此功能，可将 Azure AD B2C 配置为禁用电子邮件验证。 这样做可创建更流畅的注册过程，并使开发人员可以灵活地将已验证电子邮件地址的客户与尚未验证电子邮件地址的客户区分开来。

> [!WARNING]
> 在注册过程中禁用电子邮件验证可能会导致垃圾邮件。 如果禁用默认 Azure AD B2C 提供的电子邮件验证，我们建议你实现替换验证系统。

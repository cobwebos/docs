---
title: Azure Active Directory B2C 中的自助服务密码重置 | Microsoft Docs
description: 演示了如何在 Azure Active Directory B2C 中为客户设置自助密码重置
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: dd44e9e7018bf585adb2e731c064109ffc95e4f6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844069"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>为客户设置自助密码重置

借助自助密码重置功能，已注册了本地帐户的客户可以将密码重置为他们自己的密码。 这样可以显著减少支持人员的负担，尤其当有数百万客户定期使用应用程序时。 目前，使用已验证的电子邮件地址是唯一受支持的恢复方法。

> [!NOTE]
> 本文适用于在 V1 登录用户流的上下文中使用的自助服务密码重置，它将本地帐户登录用作标识提供者。 如果需要从应用程序调用完全可自定义的密码重置用户流，请参阅[本文](active-directory-b2c-reference-policies.md)。
> 
> 

默认情况下，目录未开启自助密码重置功能。 使用以下步骤启用该功能：

1. 以订阅管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 这是在注册目录时使用的同一工作或学校帐户，或同一 Microsoft 帐户。
2. 打开 **Azure Active Directory**（在左侧的导航栏中）。
4. 将“已启用自助服务密码重置”设置为“全部”。 
5. 单击页顶部的“保存”。 大功告成！

若要进行测试，请对任何具有作为标识提供者的本地帐户的登录用户流使用“立即运行”功能。 在本地帐户登录页上（可在其中输入电子邮件地址和密码，或用户名和密码），单击“无法访问帐户？”以验证客户体验。

> [!NOTE]
> 可使用[公司品牌打造功能](../active-directory/fundamentals/customize-branding.md)自定义自助密码重置页面。
> 
> 


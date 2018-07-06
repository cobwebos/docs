---
title: Azure Active Directory B2C 中的自助服务密码重置 | Microsoft Docs
description: 演示了如何在 Azure Active Directory B2C 中为客户设置自助密码重置
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3612e10df12e2b18f32caae55bdd83b12a4e24a6
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448778"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>为客户设置自助密码重置
借助自助密码重置功能，已注册了本地帐户的客户可以将密码重置为他们自己的密码。 这样可以显著减少支持人员的负担，尤其当有数百万客户定期使用应用程序时。 目前，使用已验证的电子邮件地址是唯一受支持的恢复方法。

> [!NOTE]
> 本文适用于登录策略上下文中使用的自助密码重置功能。 如果需要从应用程序调用完全可自定义的密码重置策略，请参阅[本文](active-directory-b2c-reference-policies.md#create-a-password-reset-policy)。
> 
> 

默认情况下，目录未开启自助密码重置功能。 使用以下步骤启用该功能：

1. 以订阅管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 这是在注册目录时使用的同一工作或学校帐户，或同一 Microsoft 帐户。
2. 打开 **Azure Active Directory**（在左侧的导航栏中）。
4. 将“已启用自助服务密码重置”设置为“全部”。 
5. 单击页顶部的“保存”。 大功告成！

若要进行测试，请对任何具有作为标识提供者的本地帐户的登录策略使用“立即运行”功能。 在本地帐户登录页上（可在其中输入电子邮件地址和密码，或用户名和密码），单击“无法访问帐户？”以验证客户体验。

> [!NOTE]
> 可使用[公司品牌打造功能](../active-directory/fundamentals/customize-branding.md)自定义自助密码重置页面。
> 
> 


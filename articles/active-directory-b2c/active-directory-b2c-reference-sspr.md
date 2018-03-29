---
title: Azure Active Directory B2C：自助密码重置 | Microsoft Docs
description: 本主题演示如何在 Azure Active Directory B2C 中为使用者设置自助密码重置
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.openlocfilehash: f38473989f90bfe6d35bffb17a02a892ad08cf5e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C：为使用者设置自助密码重置
借助自助密码重置功能，使用者（已注册本地帐户）可以将密码重置为他们自己的密码。 这样可以显著减少支持人员的负担，尤其当有数百万使用者定期使用应用程序时。 目前，仅支持使用已验证的电子邮件地址作为一种恢复方法。 以后我们将添加其他恢复方法（已验证的电话号码、安全问题等）。

> [!NOTE]
> 本文适用于登录策略上下文中使用的自助密码重置功能。 如果需要从应用程序调用完全可自定义的密码重置策略，请参阅[本文](active-directory-b2c-reference-policies.md#create-a-password-reset-policy)。
> 
> 

默认情况下，目录未开启自助密码重置功能。 使用以下步骤启用该功能：

1. 以订阅管理员身份登录到 [Azure 门户](https://portal.azure.com/)。 这是在注册目录时使用的同一工作或学校帐户，或同一 Microsoft 帐户。
2. 打开 Active Directory（在左侧的导航栏中）。
3. 选择“属性”。
4. 向下滚动到“启用自助密码重置”部分，然后切换到“所有”。 
5. 单击页顶部的“保存”。 大功告成！

若要进行测试，请对任何具有作为标识提供者的本地帐户的登录策略使用“立即运行”功能。 在本地帐户登录页上（可在其中输入电子邮件地址和密码，或用户名和密码），单击“无法访问帐户？”以验证使用者体验。

> [!NOTE]
> 可使用[公司品牌打造功能](../active-directory/customize-branding.md)自定义自助密码重置页面。
> 
> 


---
title: Azure Active Directory B2C 中的多重身份验证 | Microsoft Docs
description: 如何在由 Azure Active Directory B2C 保护的面向用户的应用程序中启用多重身份验证。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 25724ba82e57c5e3800fa1a989dd4f504df1c163
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189270"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中启用多重身份验证

Azure Active Directory B2C （Azure AD B2C）直接与[Azure 多重身份验证](../active-directory/authentication/multi-factor-authentication.md)集成，以便你可以在应用程序中添加另一个安全层来注册和登录体验。 无需编写一行代码便可启用多重身份验证。 如果已经创建了注册和登录用户流，则仍然可以启用多重身份验证。

此功能有助于应用程序处理以下方案：

- 不需要多重身份验证即可访问一个应用程序，但需要多重身份验证才能访问另一个应用程序。 例如，客户可以使用社交或本地帐户登录汽车保险应用程序，但是必须在访问在同一目录中注册的家庭保险应用程序之前验证电话号码。
- 通常不需要多重身份验证即可访问一个应用程序，但需要它才能访问其中的敏感部分。 例如，客户可以使用社交或本地帐户登录银行应用程序并查询帐户余额，但必须在尝试进行电子转账前验证电话号码。

## <a name="set-multi-factor-authentication"></a>设置多重身份验证

在创建用户流时，可以选择启用多重身份验证。

![设置多重身份验证](./media/custom-policy-multi-factor-authentication/add-policy.png)

将“多重身份验证”设置为“启用”。

可以使用“运行用户流”来验证体验。 确认以下场景：

在多重身份验证步骤发生之前，在租户中创建了一个客户帐户。 在执行此步骤期间，会要求客户提供一个电话号码并对其进行验证。 如果验证成功，则会将电话号码附加到帐户供以后使用。 即使客户取消或退出登录，也可能会要求客户在下次登录时再次验证电话号码（启用了多重身份验证时）。

## <a name="add-multi-factor-authentication"></a>添加多重身份验证

可以在之前创建的用户流上启用多重身份验证。

若要启用多重身份验证，请执行以下操作：

1. 打开用户流，然后选择“属性”。
2. 在“多重身份验证”旁边，选择“启用”。
3. 单击页顶部的“保存”。



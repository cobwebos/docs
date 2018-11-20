---
title: Azure Active Directory B2C 中的多重身份验证 | Microsoft Docs
description: 如何在由 Azure Active Directory B2C 保护的面向用户的应用程序中启用多重身份验证。
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: eabae0f3575719c6cb93affefe0a393dd13d1439
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014000"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中启用多重身份验证

Azure Active Directory (Azure AD) B2C 直接集成了 [Azure 多重身份验证](../active-directory/authentication/multi-factor-authentication.md)，因此你可以为应用程序中的注册和登录体验添加第二层安全性。 无需编写一行代码便可启用多重身份验证。 如果已经创建了注册和登录策略，则仍然可以启用多重身份验证。

此功能有助于应用程序处理以下方案：

- 不需要多重身份验证即可访问一个应用程序，但需要多重身份验证才能访问另一个应用程序。 例如，客户可以使用社交或本地帐户登录汽车保险应用程序，但是必须在访问在同一目录中注册的家庭保险应用程序之前验证电话号码。
- 通常不需要多重身份验证即可访问一个应用程序，但需要它才能访问其中的敏感部分。 例如，客户可以使用社交或本地帐户登录银行应用程序并查询帐户余额，但必须在尝试进行电子转账前验证电话号码。

## <a name="set-multi-factor-authentication"></a>设置多重身份验证

在创建策略时，可以选择启用多重身份验证。

![设置多重身份验证](./media/active-directory-b2c-reference-mfa/add-policy.png)

将“状态”设置为“开启”。

可以使用策略上的“立即运行”来验证体验。 确认以下场景：

在多重身份验证步骤发生之前，在租户中创建了一个客户帐户。 在执行此步骤期间，会要求客户提供一个电话号码并对其进行验证。 如果验证成功，则会将电话号码附加到帐户供以后使用。 即使客户取消或退出登录，也可能会要求客户在下次登录时再次验证电话号码（启用了多重身份验证时）。

## <a name="add-multi-factor-authentication"></a>添加多重身份验证

可以在之前创建的策略上启用多重身份验证。 

若要启用多重身份验证，请执行以下操作：

1. 打开策略，然后选择“编辑”。 
2. 选择“多重身份验证”
3. 将“状态”设置为“开启”。
4. 单击页顶部的“保存”。



---
title: "Azure Active Directory B2C：多重身份验证 | Microsoft Docs"
description: "如何在由 Azure Active Directory B2C 保护的面向用户的应用程序中启用多重身份验证"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 53ef86c4-1586-45dc-9952-dbbd62f68afc
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 4b13c040a15bef2f04d2cd2126e2270d061898bd
ms.openlocfilehash: 4226075affd1ae03c7bd258b244718f7bb72f127


---
# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C：启用面向用户的应用程序中的多重身份验证
Azure Active Directory (Azure AD) B2C 直接集成了 [Azure 多重身份验证](../multi-factor-authentication/multi-factor-authentication.md)，以便可以在面向消费者的应用程序中将第二层安全性添加到注册和登录体验。 你可以这样操作而无需编写单行代码。 目前我们支持电话和短信验证。 如果已经创建了注册和登录策略，则仍然可以启用多重身份验证。

> [!NOTE]
> 除了可以通过编辑现有策略之外，还可以在创建注册和登录策略时启用多重身份验证。
> 
> 

此功能有助于应用程序处理以下方案：

* 你不需要多重身份验证即可访问一个应用程序，但需要多重身份验证才能访问另一个应用程序。 例如，使用者可以用社交或本地帐户登录汽车保险应用程序，但是必须在访问在同一目录中注册的家庭保险应用程序之前验证电话号码。
* 通常你不需要多重身份验证即可访问一个应用程序，但你需要它才能访问其中的敏感部分。 例如，使用者可以使用社交或本地帐户登录银行应用程序并查询帐户余额，但必须在尝试进行电子转账前验证电话号码。

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>修改注册策略以启用多重身份验证
1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“注册策略”。
3. 单击注册政策（例如，“B2C_1_SiUp”）以将其打开。
4. 单击“多重身份验证”，然后将“状态”设置为“开启”。 单击 **“确定”**。
5. 单击边栏选项卡顶部的“保存”。

可以使用策略上的“立即运行”功能来验证用户体验。 确认符合以下条件：

在执行多重身份验证步骤之前，将在你的目录中创建一个使用者帐户。 在步骤中，会要求使用者提供他/她的电话号码并且进行验证。 如果验证成功，则电话号码将附加到使用者帐户以供日后使用。 即使消费者取消或退出登录，也可能要求他/她在下次登录时再次验证电话号码（启用多重身份验证）。

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>修改登录策略以启用多重身份验证
1. [请按照以下步骤导航到 Azure 门户上的 B2C 功能边栏选项卡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 单击“登录策略”。
3. 单击登录策略（例如，“B2C_1_SiIn”）以将其打开。 单击边栏选项卡顶部的“编辑”。
4. 单击“多重身份验证”，然后将“状态”设置为“开启”。 单击 **“确定”**。
5. 单击边栏选项卡顶部的“保存”。

可以使用策略上的“立即运行”功能来验证用户体验。 确认符合以下条件：

当使用者登录（使用社交或本地帐户）时，如果验证的电话号码附加到使用者帐户，则会要求他/她进行验证。 如果没有附加电话号码，则要求使用者提供一个电话号码并对其进行验证。 验证成功后，则电话号码将附加到使用者帐户以供日后使用。

## <a name="multi-factor-authentication-on-other-policies"></a>针对其他策略的多重身份验证
如上面的注册和登录策略所述，你还可以在注册或登录策略和密码重置策略中启用多重身份验证。 它很快就可用于配置文件编辑策略。




<!--HONumber=Dec16_HO5-->



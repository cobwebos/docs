---
title: "Azure Active Directory B2C：自助密码重置 | Microsoft Docs"
description: "本主题演示如何在 Azure Active Directory B2C 中为使用者设置自助密码重置"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: curtand
ms.assetid: c87ed86e-1520-42b1-8c31-46cd44ed5310
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
ms.openlocfilehash: beaf7dc6260db7509b2202c7801bcc0d2dd2c69e
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>Azure Active Directory B2C：为使用者设置自助密码重置
借助自助密码重置功能，使用者（已注册本地帐户）可以将密码重置为他们自己的密码。 这样可以显著减少支持人员的负担，尤其当有数百万使用者定期使用应用程序时。 目前，仅支持使用已验证的电子邮件地址作为一种恢复方法。 以后我们将添加其他恢复方法（已验证的电话号码、安全问题等）。

> [!NOTE]
> 本文适用于登录策略上下文中使用的自助密码重置功能。 如果需要从应用程序调用完全可自定义的密码重置策略，请参阅[本文](active-directory-b2c-reference-policies.md#create-a-password-reset-policy)。
> 
> 

默认情况下，目录未开启自助密码重置功能。 使用以下步骤启用该功能：

1. 以订阅管理员身份登录到 [Azure 经典门户](https://manage.windowsazure.com/)。 这是在注册目录时使用的同一工作或学校帐户，或同一 Microsoft 帐户。
2. 导航到左侧导航栏上的 Active Directory 扩展。
3. 在“目录”选项卡下找到目录，然后单击。
4. 单击“配置”  选项卡。
5. 向下滚动至“用户密码重置策略”部分，将“为用户启用密码重置”选项切换到“是”。 请注意，“备用电子邮件地址”选项处于选中状态；请将它保留原样。
   
    ![自助密码重置](./media/active-directory-b2c-reference-sspr/sspr.png)
6. 单击页面底部的“保存”  。 大功告成！

若要进行测试，请对任何具有作为标识提供者的本地帐户的登录策略使用“立即运行”功能。 在本地帐户登录页上（可在其中输入电子邮件地址和密码，或用户名和密码），单击“无法访问帐户？”以验证使用者体验。

> [!NOTE]
> 可使用[公司品牌打造功能](../active-directory/customize-branding.md)自定义自助密码重置页面。
> 
> 


---
title: 电话号码的 SMS 登录用户体验（预览版）-Azure AD
description: 详细了解新的或现有的电话号码的 SMS 登录用户体验
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: e5369247fc1e31bafb158e83a54bf8badf721d0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378829"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>使用你的电话号码作为用户名（预览）

通过注册设备，你可以访问你的组织的服务，而不允许你的组织访问你的手机。 如果你是管理员，可以在[配置和启用基于 SMS 的身份验证的用户](../authentication/howto-authentication-sms-signin.md)中找到详细信息。

如果你的组织尚未提供 SMS 登录，则在向你的帐户注册手机时，你将不会看到此选项。  

## <a name="when-you-have-a-new-phone-number"></a>如果你有新的电话号码

如果收到新的电话号码或新号码，并将其注册到了 SMS 登录可用的组织中，你将会遇到正常的手机注册过程：

1. 选择 "**添加方法**"。
1. 选择 "**电话**"。
1. 输入电话号码，然后选择 "**向我发送代码文本**"。
1. 输入代码后，选择 "**下一步**"。
1. 你将看到一条提示 "SMS 已验证"。 您的电话已成功注册。 "

> [!Important]
> 由于预览版中的已知问题，在短时间内添加电话号码将不会为 SMS 登录注册号码。 你必须使用添加的号码登录，然后按照提示为 SMS 登录注册号码。

### <a name="when-the-phone-number-is-in-use"></a>使用电话号码的时间

如果你尝试使用组织中的其他人所使用的电话号码，你会看到以下消息：

![已使用的电话号码时出现错误消息](media/sms-sign-in-explainer/sms-sign-in-error.png)

联系你的管理员以解决此问题。

## <a name="when-you-have-an-existing-number"></a>如果拥有现有数量

如果你已在组织中使用电话号码，并使用你的电话号码作为用户名可用，则可以通过以下步骤来登录。

1. 当 SMS 登录可用时，会显示一条横幅，询问你是否要为 SMS 登录启用电话号码：

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. 此外，如果您在 phone 方法磁贴上选择插入符号，则会出现 "**启用**" 按钮：

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 若要启用方法，请选择 "**启用**"。 系统将提示你确认该操作：

    ![用于启用电话号码的 SMS 登录的确认对话框](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. 选择“启用”****。

## <a name="when-you-remove-your-phone-number"></a>删除电话号码时

1. 若要删除电话号码，请在 "SMS 登录电话方法" 磁贴上选择 "删除" 按钮。

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 系统提示确认操作时，请选择 **"确定"**。

你无法删除用作默认登录方法的电话号码。 若要删除该号码，则必须更改默认登录方法，然后再次删除电话号码。

---
title: 电话号码（预览）的短信登录用户体验 - Azure AD
description: 了解有关新或现有电话号码的 SMS 登录用户体验
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378829"
---
# <a name="use-your-phone-number-as-a-user-name-preview"></a>使用电话号码作为用户名（预览）

注册设备使您的手机可以访问组织的服务，并且不允许您的组织访问您的手机。 如果您是管理员，您可以在["配置"中找到更多信息，并启用用户进行基于 SMS 的身份验证](../authentication/howto-authentication-sms-signin.md)。

如果您的组织尚未提供 SMS 登录，则在帐户中注册手机时，您看不到该选项。  

## <a name="when-you-have-a-new-phone-number"></a>当您有新电话号码时

如果您收到新电话或新号码，并且将其注册到有 SMS 登录的组织，则您可能会遇到正常的电话注册过程：

1. 选择 **"添加方法**"。
1. 选择**电话**。
1. 输入电话号码并选择 **"给我发短信"代码**。
1. 输入代码后，选择 **"下一步**"。
1. 您将看到一个提示，提示显示"已验证的 SMS"。 您的手机已成功注册。

> [!Important]
> 由于预览中出现已知问题，在短时间内添加电话号码将不会注册 SMS 登录号码。 您必须使用添加的号码登录，然后按照提示注册 SMS 登录号码。

### <a name="when-the-phone-number-is-in-use"></a>使用电话号码时

如果您尝试使用组织中其他人正在使用的电话号码，您将看到以下消息：

![已使用电话号码时出现错误消息](media/sms-sign-in-explainer/sms-sign-in-error.png)

联系您的管理员以修复问题。

## <a name="when-you-have-an-existing-number"></a>当您有现有号码时

如果您已经在组织中使用电话号码，并且将电话号码用作用户名将变为可用，以下步骤可以帮助您登录。

1. 当 SMS 登录可用时，将显示一个横幅，询问您是否要启用 SMS 登录的电话号码：

    [![](media/sms-sign-in-explainer/sms-sign-in-banner.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-banner.png#lightbox)

1. 此外，如果在电话方法磁贴上选择"**启用**"按钮，则会出现启用按钮：

    [![](media/sms-sign-in-explainer/sms-sign-in-phone-method.png "Banner to enable SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-phone-method.png#lightbox)

1. 要启用该方法，请选择 **"启用**"。 系统将提示您确认操作：

    ![确认对话框，用于为电话号码启用 SMS 登录](media/sms-sign-in-explainer/sms-sign-in-confirmation.png)

1. 选择“启用”****。

## <a name="when-you-remove-your-phone-number"></a>删除电话号码时

1. 要删除电话号码，请选择 SMS 登录电话方法磁贴上的删除按钮。

    [![](media/sms-sign-in-explainer/sms-sign-in-delete-method.png "Banner to delete SMS sign-in for a phone number")](media/sms-sign-in-explainer/sms-sign-in-delete-method.png#lightbox)

2. 当提示确认操作时，选择 **"确定**"。

不能删除用作默认登录方法的电话号码。 要删除该号码，必须更改默认登录方法，然后再次删除电话号码。

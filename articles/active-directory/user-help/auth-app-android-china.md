---
title: Microsoft Authenticator 在中国 Android 中的可用性和限制 | Microsoft Docs
description: 了解如何获取 Microsoft Authenticator 应用在中国的可用性
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 05/06/2020
ms.author: curtand
ms.openlocfilehash: 3ba1b41859a543ab5e0393c5715dc49d452ea73c
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892233"
---
# <a name="microsoft-authenticator-for-android-in-the-public-cloud-in-china"></a>中国公有云中的适用于 Android 的 Microsoft Authenticator

适用于 Android 的 Microsoft Authenticator 应用可在中国下载。 Google Play 商店在中国不可用，因此必须从其他中文应用商店下载该应用。 适用于 Android 的 Microsoft Authenticator 应用目前在中国的以下应用商店中提供：

- [百度](https://shouji.baidu.com/software/26638379.html)
- [Lenovo](https://www.lenovomm.com/appdetail/com.azure.authenticator/20197724)
- [Huawei](https://appgallery.cloud.huawei.com/uowap/index.html#/detailApp/C100262999?source=appshare&subsource=C100262999&shareTo=weixin&locale=zh_CN)
- [Samsung Galaxy 应用商店](http://apps.samsung.com/appquery/appDetail.as?appId=com.azure.authenticator)

该应用的最新内部版本在 Google Play 商店中提供，但我们会尽快在所有其他应用商店中更新该应用。 由于没有自定义 Android 应用程序包 (APK) 部署到任何应用商店，因此可以从以下位置之一对该应用进行无缝更新：

- 已从中下载该应用的应用商店
- Google Play 商店（如果用户跨区域）

## <a name="limitations"></a>限制

适用于 Android 的 Microsoft Authenticator 应用使用 Google Firebase Cloud Messaging 系统和 Google Play Services 来接收推送通知。 由于这两种服务在中国均不可用，因此该应用的功能存在一些限制：

- 不能将 Authenticator 应用注册为一种使用推送通知的多重身份验证 (MFA) 方法。

- 无法设置[手机登录](../authentication/howto-authentication-sms-signin.md)。 它要求用户将该应用设置为一种使用推送通知的 MFA 方法，而这目前无法做到。

如果用户以前使用该应用成功设置了手机登录或多重身份验证，则可在该应用中手动检查通知请求并将其用于身份验证。

## <a name="multi-factor-authentication-workaround"></a>多重身份验证解决方案

用户可以将自己的 MFA 设置为在设备上接收可用于验证其身份的验证码，而不是使用推送通知进行多重身份验证。 这些验证码的有效期为 30 秒。若要使用它们，管理员必须让租户能够使用基于时间的一次性密码 (TOTP) 验证码执行验证。

## <a name="availability"></a>可用性

Microsoft Authenticator 功能 | 在中国的可用性
------------------------------- | ---------------------
使用推送通知进行 MFA 注册 | 否
预先存在的 MFA 帐户使用推送通知验证身份 | 否
预先存在的 MFA 帐户通过手动检查来获取通知 | 是
仅使用 TOTP/验证码进行 MFA 注册/身份验证 | 是
手机登录注册 | 否
现有手机登录使用推送通知 | 否
通过手动检查是否存在身份验证请求对现有手机登录进行验证 | 是

## <a name="next-steps"></a>后续步骤

- [下载并安装 Microsoft Authenticator 应用](user-help-auth-app-download-install.md)

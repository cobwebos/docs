---
title: 设置沉浸式读者 Cookie 策略
titleSuffix: Azure Cognitive Services
description: 本文将演示如何设置沉浸式读者的 cookie 策略。
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91332366"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>如何设置沉浸式读取器的 cookie 策略

默认情况下，沉浸式读取器将禁用 cookie。 如果启用 cookie，则沉浸式读者可以使用 cookie 来维护用户首选项并跟踪功能的使用情况。 如果在沉浸式阅读器中启用 cookie，请考虑欧盟 Cookie 符合性策略的要求。 主机应用程序负责根据欧盟 Cookie 符合性策略获取任何必需的用户同意。

Cookie 策略可通过沉浸式读者 [选项](../reference.md#options)进行设置。

## <a name="enable-cookie-usage"></a>启用 Cookie 用法

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>禁用 Cookie 用法

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 快速入门](../quickstarts/client-libraries.md?pivots=programming-language-nodejs)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Android 教程](../tutorial-android.md)，了解通过 Java 或 Kotlin for Android 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [iOS 教程](../tutorial-ios.md)，了解通过 Swift for iOS 使用沉浸式阅读器 SDK 可执行的其他操作
* 查看 [Python 教程](../tutorial-python.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../reference.md)
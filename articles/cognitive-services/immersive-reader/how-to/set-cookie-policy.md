---
title: 设置沉浸式阅读器 Cookie 策略
titleSuffix: Azure Cognitive Services
description: 本文将向您展示如何为沉浸式阅读器设置 Cookie 策略。
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946107"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>如何为沉浸式阅读器设置 Cookie 策略

默认情况下，沉浸式阅读器将禁用 Cookie 使用。 如果您启用 Cookie 使用，则沉浸式阅读器可能会使用 Cookie 来维护用户首选项并跟踪功能使用情况。 如果您在沉浸式阅读器中启用 Cookie 使用，请考虑欧盟 Cookie 合规政策的要求。 根据欧盟 Cookie 合规政策，主机应用程序有责任获得任何必要的用户同意。

Cookie 策略可以通过沉浸式阅读器[选项](../reference.md#options)进行设置。 有关详细信息[，请参阅 CookiePolicy 枚举](../reference.md#cookiepolicy-enum)。

## <a name="enable-cookie-usage"></a>启用 Cookie 使用

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>禁用 Cookie 使用

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>后续步骤

* 查看 [Node.js 快速入门](../quickstart-nodejs.md)，了解通过 Node.js 使用沉浸式阅读器 SDK 还可以做什么
* 查看 [Python 教程](../tutorial-python.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 查看[Swift 教程](../tutorial-ios-picture-immersive-reader.md)，了解使用 Swift 的沉浸式读取器 SDK 还可以执行哪些操作
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../reference.md)
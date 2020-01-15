---
title: 设置沉浸式读者 Cookie 策略
titleSuffix: Azure Cognitive Services
description: 本文将演示如何设置沉浸式读者的 cookie 策略。
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75946107"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>如何设置沉浸式读取器的 cookie 策略

默认情况下，沉浸式读取器将禁用 cookie。 如果启用 cookie，则沉浸式读者可以使用 cookie 来维护用户首选项并跟踪功能的使用情况。 如果在沉浸式阅读器中启用 cookie，请考虑欧盟 Cookie 符合性策略的要求。 主机应用程序负责根据欧盟 Cookie 符合性策略获取任何必需的用户同意。

Cookie 策略可通过沉浸式读者[选项](../reference.md#options)进行设置。 有关详细信息，请参阅[CookiePolicy 枚举](../reference.md#cookiepolicy-enum)。

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

* 查看[node.js 快速入门](../quickstart-nodejs.md)，了解使用 node.js 可以通过沉浸式读者 SDK 执行的其他操作
* 查看 [Python 教程](../tutorial-python.md)，了解通过 Python 使用沉浸式阅读器 SDK 还可以做什么
* 查看[swift 教程](../tutorial-ios-picture-immersive-reader.md)，了解如何使用 swift 来处理沉浸式读者 SDK
* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../reference.md)
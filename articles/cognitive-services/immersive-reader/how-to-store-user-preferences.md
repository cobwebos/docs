---
title: 存储用户首选项
titleSuffix: Azure Cognitive Services
description: 本文将演示如何存储用户的首选项。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ddae4a99964e438c003fe0ff0db91c5808fa7631
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761101"
---
# <a name="how-to-store-user-preferences"></a>如何存储用户首选项

本文演示如何通过[-首选项](./reference.md#options)和[-OnPreferencesChanged](./reference.md#options)沉浸式读者 SDK 选项来存储用户的 UI 设置（正式称为**用户首选项**）。

当 [CookiePolicy](./reference.md#cookiepolicy-options) SDK 选项设置为 " *已启用*" 时，沉浸式读取器应用程序将 **用户首 (选项** 设置为 cookie 中的) （在特定浏览器和设备本地）。 用户每次在同一浏览器和设备上启动沉浸式阅读器时，都将在该设备上的最后一个会话中打开用户的首选项。 但是，如果用户在不同的浏览器或设备上打开沉浸式阅读器，则最初将使用沉浸式读者的默认设置来配置这些设置，并且用户必须再次设置其首选项，并在每个设备上使用。 `-preferences` `-onPreferencesChanged` 使用沉浸式读者 SDK 选项，应用程序可以在各种浏览器和设备之间漫游用户的首选项，使用户在使用该应用程序的任何位置都拥有一致的体验。

首先，通过在 `-onPreferencesChanged` 启动沉浸式读取器应用程序时提供回调 SDK 选项，沉浸式读取器会将 `-preferences` 字符串发送回主机应用程序，每次用户在沉浸式读卡器会话期间更改其首选项。 然后，主机应用程序负责在其自己的系统中存储用户首选项。 然后，当该用户再次启动沉浸式读者时，主机应用程序可以从存储中检索该用户的首选项，并 `-preferences` 在启动沉浸式阅读器应用程序时将其作为字符串 SDK 选项提供，以便还原用户的首选项。

如果使用 cookie 不理想或不可行，则可以使用此功能作为存储 **用户首选项** 的替代方法。

> [!CAUTION]
> **重要提示** 不要尝试以编程方式更改 `-preferences` 发送到沉浸式读取器应用程序的字符串的值，因为这可能会导致意外行为，导致用户的用户体验下降。 主机应用程序不应将自定义值分配给或操作该 `-preferences` 字符串。 使用 `-preferences` 字符串选项时，请仅使用从回调选项返回的确切值 `-onPreferencesChanged` 。

## <a name="how-to-enable-storing-user-preferences"></a>如何启用存储用户首选项

沉浸式读者 SDK [launchAsync](./reference.md#launchasync) `options` 参数包含 `-onPreferencesChanged` 回调。 每当用户更改其首选项时，都将调用此函数。 `value`参数包含一个字符串，该字符串表示用户的当前首选项。 然后，由主机应用程序为该用户存储此字符串。

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>如何将用户首选项加载到沉浸式阅读器

使用选项将用户的首选项传递给沉浸式读者 `-preferences` 。 存储和加载用户首选项的一个简单示例如下所示：

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)
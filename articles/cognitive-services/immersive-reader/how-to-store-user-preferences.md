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
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486898"
---
# <a name="how-to-store-user-preferences"></a>如何存储用户首选项

本文演示如何存储用户的首选项。 此功能旨在作为替代方法，用于存储用户的首选项，而不需要使用 cookie。

## <a name="how-to-enable-storing-user-preferences"></a>如何启用存储用户首选项

`options`参数包含 `onPreferencesChanged` 回调。 无论用户何时更改首选项，都将调用此函数（例如，更改文本大小、主题颜色、字体等）。 `value`参数包含一个字符串，该字符串表示用户的当前首选项。 可以使用所需的任何机制来存储此字符串。

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>如何将用户首选项加载到沉浸式阅读器

使用参数将用户的首选项传递给沉浸式读者 `preferences` 。 存储和加载用户首选项的一个简单示例如下所示：

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)
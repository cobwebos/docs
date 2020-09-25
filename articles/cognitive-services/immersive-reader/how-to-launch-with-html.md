---
title: 启动沉浸式阅读器以显示 HTML 内容
titleSuffix: Azure Cognitive Services
description: 本文将演示如何通过 HTML 内容启动沉浸式阅读器。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.custom: devx-track-js
ms.openlocfilehash: f7f55d1d349fc7d02c2ac53f7a50f9d649688cbd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326756"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>如何用 HTML 内容启动沉浸式阅读器

本文演示如何通过 HTML 内容启动沉浸式阅读器。

## <a name="prepare-the-html-content"></a>准备 HTML 内容

将想要呈现的内容置于容器元素内的沉浸式读取器中。 确保容器元素具有唯一的 `id` 。 沉浸式读取器提供对基本 HTML 元素的支持，有关详细信息，请参阅 [参考](./reference.md#html-support) 。

```html
<div id='immersive-reader-content'>
    <b>Bold</b>
    <i>Italic</i>
    <u>Underline</u>
    <strike>Strikethrough</strike>
    <code>Code</code>
    <sup>Superscript</sup>
    <sub>Subscript</sub>
    <ul><li>Unordered lists</li></ul>
    <ol><li>Ordered lists</li></ol>
</div>
```

## <a name="get-the-html-content-in-javascript"></a>获取 JavaScript 中的 HTML 内容

使用 `id` 容器元素的来获取 JavaScript 代码中的 HTML 内容。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>用 HTML 内容启动沉浸式阅读器

调用时 `ImmersiveReader.launchAsync` ，将区块的 `mimeType` 属性设置为， `text/html` 以启用呈现 HTML。

```javascript
const data = {
    chunks: [{
        content: htmlContent,
        mimeType: 'text/html'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)
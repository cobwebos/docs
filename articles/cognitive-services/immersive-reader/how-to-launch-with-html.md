---
title: 启动沉浸式阅读器以显示 HTML 内容
titleSuffix: Azure Cognitive Services
description: 本文将向您展示如何启动包含 HTML 内容的沉浸式阅读器。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946239"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>如何启动包含 HTML 内容的沉浸式阅读器

本文演示如何启动包含 HTML 内容的沉浸式阅读器。

## <a name="prepare-the-html-content"></a>准备 HTML 内容

将要呈现的内容放在容器元素的沉浸式读取器中。 确保容器元素具有唯`id`一的 。 沉浸式阅读器支持基本 HTML 元素，有关详细信息，请参阅[参考](./reference.md#html-support)。

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

`id`使用 容器元素获取 JavaScript 代码中的 HTML 内容。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>使用 HTML 内容启动沉浸式阅读器

调用`ImmersiveReader.launchAsync`时，将块的属性`mimeType``text/html`设置为启用呈现 HTML。

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
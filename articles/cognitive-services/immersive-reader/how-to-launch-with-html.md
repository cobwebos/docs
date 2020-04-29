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
ms.openlocfilehash: bc7ab46113e1b819fc71a9f6e8a18400f8acfbef
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946239"
---
# <a name="how-to-launch-the-immersive-reader-with-html-content"></a>如何用 HTML 内容启动沉浸式阅读器

本文演示如何通过 HTML 内容启动沉浸式阅读器。

## <a name="prepare-the-html-content"></a>准备 HTML 内容

将想要呈现的内容置于容器元素内的沉浸式读取器中。 确保容器元素具有唯一`id`的。 沉浸式读取器提供对基本 HTML 元素的支持，有关详细信息，请参阅[参考](./reference.md#html-support)。

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

`id`使用容器元素的来获取 JavaScript 代码中的 HTML 内容。

```javascript
const htmlContent = document.getElementById('immersive-reader-content').innerHTML;
```

## <a name="launch-the-immersive-reader-with-your-html-content"></a>用 HTML 内容启动沉浸式阅读器

调用`ImmersiveReader.launchAsync`时，将区块的`mimeType`属性设置为`text/html` ，以启用呈现 HTML。

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
---
title: 在沉浸式阅读器中显示数学
titleSuffix: Azure Cognitive Services
description: 本文将向您展示如何在沉浸式阅读器中显示数学。
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: pasta
ms.openlocfilehash: e01cc9e8cedb6c38da0b56e04419c706d5d0566e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946118"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>如何在沉浸式阅读器中显示数学

沉浸式读取器可以显示数学，当提供数学标记语言 （[MathML](https://developer.mozilla.org/docs/Web/MathML)）.
MIME 类型可以通过沉浸式读取器[块](../reference.md#chunk)进行设置。 有关详细信息[，请参阅支持的 MIME 类型](../reference.md#supported-mime-types)。

## <a name="send-math-to-the-immersive-reader"></a>将数学发送到沉浸式阅读器
为了将数学发送到沉浸式读取器，请提供包含 MathML 的块，并将 MIME```application/mathml+xml```类型设置为 ;

例如，如果您的内容如下：

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

然后，您可以使用以下 JavaScript 显示内容。

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

当您启动沉浸式阅读器时，您应该看到：

![沉浸式阅读器中的数学](../media/how-tos/1-math.png)

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK ](https://github.com/microsoft/immersive-reader-sdk)和[沉浸式阅读器 SDK 参考](../reference.md)
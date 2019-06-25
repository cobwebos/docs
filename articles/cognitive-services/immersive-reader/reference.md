---
title: 令人着迷的读取器 SDK 参考
titlesuffix: Azure Cognitive Services
description: 令人着迷的读取器 SDK 的参考
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: c128608b3c4a8e1155c3ac962bcfd07f589fbf23
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311784"
---
# <a name="immersive-reader-sdk-reference"></a>令人着迷的读取器 SDK 参考

令人着迷的读取器 SDK 是一个 JavaScript 库，可将令人着迷的读取器集成到 web 应用程序。

## <a name="functions"></a>函数

SDK 公开一个单一的函数， `ImmersiveReader.launchAsync(token, resourceName, content, options)`。

### <a name="launchasync"></a>launchAsync

将令人着迷的读取器中启动`iframe`web 应用程序中。

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>parameters

| 名称 | 类型 | 描述 |
| ---- | ---- |------------ |
| `token` | string | 通过调用获取的访问令牌`issueToken`终结点。 |
| `resourceName` | string | 保留。 必须设置为 `null`。 |
| `content` | [内容](#content) | 包含要在沉浸式读取器中显示的内容的对象。 |
| `options` | [选项](#options) | 用于配置沉浸式读取器的某些行为的选项。 可选。 |

#### <a name="returns"></a>返回

返回`Promise<HTMLDivElement>`它解析沉浸式读取器加载时。 `Promise`解析为`div`元素的唯一子级是`iframe`元素，其中包含令人着迷的读取器页。

#### <a name="exceptions"></a>例外

返回`Promise`将被拒绝，并[ `Error` ](#error)对象沉浸式读取器无法加载。 有关详细信息，请参阅[错误代码](#error-codes)。

## <a name="types"></a>类型

### <a name="content"></a>内容

包含要在沉浸式读取器中显示的内容。

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>支持的 MIME 类型

| MIME 类型 | 描述 |
| --------- | ----------- |
| text/plain | 纯文本。 |
| application/mathml+xml | 数学标记语言 (MathML)。 [了解详细信息](https://developer.mozilla.org/en-US/docs/Web/MathML)。

### <a name="options"></a>选项

包含配置的沉浸式的读取器的某些行为的属性。

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>错误

包含有关错误的信息。

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>错误代码

| 代码 | 描述 |
| ---- | ----------- |
| BadArgument | 提供的参数无效，请参阅`message`有关详细信息。 |
| 超时 | 令人着迷的读取器加载指定的超时时间内失败。 |
| TokenExpired| 提供的令牌已过期。 |

## <a name="launching-the-immersive-reader"></a>启动沉浸式读取器

该 SDK 提供用于启动沉浸式读取器按钮的默认样式。 使用`immersive-reader-button`类属性，以启用此样式设置。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>可选属性

使用以下属性来配置该按钮的外观。

| 特性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 设置按钮的样式。 可以是 `icon`、`text` 或 `iconAndText`。 默认为 `icon`。 |
| `data-locale` | 将区域设置，例如`en-US`， `fr-FR`。 默认值为英语。 |
| `data-icon-px-size` | 以像素为单位设置图标的大小。 默认值为 20px。 |

## <a name="browser-support"></a>浏览器支持

使用以下浏览器的最新版本的沉浸式读取器的最佳体验。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>后续步骤

* 探索[沉浸式读取器在 GitHub 上的 SDK](https://github.com/Microsoft/immersive-reader-sdk)
* [快速入门：创建 web 应用，它启动了令人着迷的读取器 (C#)](./quickstart.md)
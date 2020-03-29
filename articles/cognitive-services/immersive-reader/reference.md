---
title: 沉浸式读取器 SDK 参考
titleSuffix: Azure Cognitive Services
description: 沉浸式阅读器 SDK 包含一个 JavaScript 库，允许您将沉浸式阅读器集成到应用程序中。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156397"
---
# <a name="immersive-reader-sdk-reference-guide"></a>沉浸式读卡器 SDK 参考指南

沉浸式阅读器 SDK 包含一个 JavaScript 库，允许您将沉浸式阅读器集成到应用程序中。

## <a name="functions"></a>函数

SDK 公开功能：

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>发射Async

在 Web 应用程序中启动`iframe`沉浸式阅读器。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>参数

| “属性” | 类型 | 说明 |
| ---- | ---- |------------ |
| `token` | 字符串 | Azure AD 身份验证令牌。 |
| `subdomain` | 字符串 | Azure 中沉浸式读取器资源的自定义子域。 |
| `content` | [内容](#content) | 包含要在沉浸式阅读器中显示的内容的对象。 |
| `options` | [选项](#options) | 用于配置沉浸式读取器的某些行为的选项。 可选。 |

### <a name="returns"></a>返回

返回`Promise<LaunchResponse>`一个，该解析在加载沉浸式读取器时。 对`Promise`[`LaunchResponse`](#launchresponse)对象的解析。

### <a name="exceptions"></a>异常

如果沉`Promise`浸式读取器无法[`Error`](#error)加载，则返回的对象将被拒绝。 有关详细信息，请参阅[错误代码](#error-codes)。

## <a name="close"></a>关闭

关闭沉浸式读取器。

此函数的示例用例是，如果退出按钮通过在```hideExitButton: true```[选项](#options)中设置而隐藏。 然后，当单击该按钮时，可以调用其他```close```按钮（例如移动标头的后箭头）。

```typescript
close(): void;
```

## <a name="renderbuttons"></a>渲染按钮

此功能可样式并更新文档的沉浸式读取器按钮元素。 如果```options.elements```提供，则此函数将在 中```options.elements```呈现按钮。 否则，按钮将在具有 类```immersive-reader-button```的文档元素中呈现。

当窗口加载时，SDK 会自动调用此功能。

有关更多渲染选项[，请参阅可选属性](#optional-attributes)。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>参数

| “属性” | 类型 | 说明 |
| ---- | ---- |------------ |
| `options` | [渲染按钮选项](#renderbuttonsoptions) | 用于配置呈现按钮函数的某些行为的选项。 可选。 |

## <a name="types"></a>类型

### <a name="content"></a>内容

包含要在沉浸式阅读器中显示的内容。

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>区块

单个数据块，将传递到沉浸式读取器的内容中。

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>启动响应

包含从调用 到`ImmersiveReader.launchAsync`的响应。

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Cookie 政策枚举

用于设置沉浸式阅读器 Cookie 使用策略的枚举。 请参阅[选项](#options)。

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>支持的 MIME 类型

| MIME 类型 | 描述 |
| --------- | ----------- |
| text/plain | 纯文本。 |
| text/html | HTML 内容。 [了解详细信息](#html-support)|
| 应用程序/mathml_xml | 数学标记语言（数学ML）。 [了解详情](./how-to/display-math.md)。
| 应用程序/vnd.openxml 格式-办公室文档.字处理ml.document | 微软 Word .docx 格式文档。

### <a name="html-support"></a>HTML 支持

| HTML | 支持的内容 |
| --------- | ----------- |
| 字体样式 | 粗体、斜体、下划线、代码、打击、超编、下标 |
| 无序列表 | 光盘， 圆， 方形， 圆， 圆， 圆， 圆， 圆， 圆 |
| 已订购列表 | 十进制， 上阿尔法， 下阿尔法， 上罗马， 下罗马 |
| 超链接 | 即将支持 |

不支持的标记将呈现比较。 当前不支持图像和表。

### <a name="options"></a>选项

包含配置沉浸式读取器某些行为的属性。

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>渲染按钮选项

用于渲染沉浸式读取器按钮的选项。

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
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
| BadArgument | 提供的参数无效，请参阅`message`详细信息。 |
| 超时 | 沉浸式读取器无法在指定的超时内加载。 |
| TokenExpired | 提供的令牌已过期。 |
| 已中止 | 已超出呼叫速率限制。 |

## <a name="launching-the-immersive-reader"></a>启动沉浸式阅读器

SDK 为启动沉浸式读取器的按钮提供默认样式。 使用`immersive-reader-button`类属性启用此样式。 有关详细信息，请参阅[此文章](./how-to-customize-launch-button.md)。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>可选属性

使用以下属性配置按钮的外观。

| 特性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 设置按钮的样式。 可以是 `icon`、`text` 或 `iconAndText`。 默认为 `icon`。 |
| `data-locale` | 设置区域设置。 例如，`en-US` 或 `fr-FR`。 默认为英语`en`。 |
| `data-icon-px-size` | 设置以像素为单位的图标大小。 默认值为 20px。 |

## <a name="browser-support"></a>浏览器支持

使用以下浏览器的最新版本，获得沉浸式阅读器的最佳体验。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>后续步骤

* 探索 [GitHub 上的沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [快速入门：创建启动沉浸式阅读器 （C#） 的 Web 应用](./quickstart.md)

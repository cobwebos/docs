---
title: 沉浸式读者 SDK 参考
titleSuffix: Azure Cognitive Services
description: 沉浸式读者 SDK 参考
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 1908ed916d61c7a65b1f0061c0fe8d8a08b5e41c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388096"
---
# <a name="immersive-reader-sdk-reference-guide"></a>沉浸式读者 SDK 参考指南

沉浸式读者 SDK 是一个 JavaScript 库，可用于将沉浸式读者集成到 web 应用程序中。

# <a name="functions"></a>Functions

SDK 公开函数：

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

在 web 应用程序的 @no__t 0 中启动沉浸式读取器。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

### <a name="parameters"></a>parameters

| 名称 | Type | 描述 |
| ---- | ---- |------------ |
| `token` | 字符串 | Azure AD 身份验证令牌。 请参阅[Azure AD authentication 操作方法](./azure-active-directory-authentication.md)。 |
| `subdomain` | 字符串 | Azure 中沉浸式读者资源的自定义子域。 请参阅[Azure AD authentication 操作方法](./azure-active-directory-authentication.md)。 |
| `content` | [内容](#content) | 一个对象，该对象包含要在沉浸式读取器中显示的内容。 |
| `options` | [选项](#options) | 用于配置沉浸式读者的某些行为的选项。 可选。 |

### <a name="returns"></a>返回值

返回一个 `Promise<HTMLDivElement>`，它可解析沉浸式读取器的加载时间。 @No__t-0 解析为 @no__t 1 元素，该元素的子元素是包含沉浸式读者页的 @no__t 2 元素。

### <a name="exceptions"></a>例外

如果无法加载沉浸式读取器，则将使用[@no__t 2](#error)对象拒绝返回的 @no__t。 有关详细信息，请参阅[错误代码](#error-codes)。

## <a name="close"></a>关闭

关闭沉浸式阅读器。

此函数的一个示例用例是通过在[选项](#options)中设置 ```hideExitButton: true``` 来隐藏 "退出" 按钮。 然后，在单击时，另一个按钮（例如，移动标头的后退箭头）可以调用此 ```close``` 函数。

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

此函数将样式和更新文档的沉浸式读者按钮元素。 如果提供 ```options.elements```，则此函数将呈现 @no__t 中的按钮。 否则，这些按钮将在具有类 ```immersive-reader-button``` 的文档元素中呈现。

当窗口加载时，SDK 会自动调用此函数。

有关更多呈现选项，请参阅[可选属性](#optional-attributes)。

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>parameters

| 名称 | Type | 描述 |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | 用于配置 renderButtons 函数的某些行为的选项。 可选。 |

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

单个数据块，将传入沉浸式读取器的内容。

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

#### <a name="supported-mime-types"></a>支持的 MIME 类型

| MIME 类型 | 描述 |
| --------- | ----------- |
| text/plain | 纯文本。 |
| text/html | HTML 内容。 [了解详细信息](#html-support)|
| application/mathml + xml | 数学标记语言（MathML）。 [了解详细信息](https://developer.mozilla.org/en-US/docs/Web/MathML)。
| application/vnd.apple.mpegurl. vnd.openxmlformats-officedocument.spreadsheetml.sheet. wordprocessingml | Microsoft Word .docx 格式的文档。

### <a name="html-support"></a>HTML 支持
| HTML | 支持的内容 |
| --------- | ----------- |
| 字体样式 | 粗体、斜体、下划线、代码、删除线、上标、下标 |
| 无序列表 | 光盘、圆形、方形 |
| 排序列表 | Decimal，大写字母，小写字母，上罗马，小写罗马字 |
| 中超 | 即将推出 |

不支持的标记将呈现为同等的。 当前不支持映像和表。

### <a name="options"></a>选项

包含配置沉浸式读者的某些行为的属性。

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
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

用于呈现沉浸式读者按钮的选项。

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
| BadArgument | 提供的参数无效，有关详细信息，请参阅 `message`。 |
| 超时 | 沉浸式读取器无法在指定的超时内加载。 |
| TokenExpired | 提供的令牌已过期。 |
| 受到 | 超出了调用速率限制。 |

## <a name="launching-the-immersive-reader"></a>启动沉浸式阅读器

SDK 为启动沉浸式阅读器的按钮提供默认样式。 使用 @no__t 类特性来启用此样式设置。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>可选属性

使用以下属性来配置按钮的外观。

| 属性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 设置按钮的样式。 可以是 `icon`、`text` 或 `iconAndText`。 默认为 `icon`。 |
| `data-locale` | 设置区域设置。 例如 `en-US` 或 `fr-FR`。 默认值为英语 `en`。 |
| `data-icon-px-size` | 设置图标的大小（以像素为单位）。 默认值为20px。 |

## <a name="browser-support"></a>浏览器支持

使用以下浏览器的最新版本，以获取沉浸式读者的最佳体验。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>后续步骤

* 探索 [GitHub 上的沉浸式阅读器 SDK](https://github.com/microsoft/immersive-reader-sdk)
* [快速入门：创建用于启动沉浸式读者（C#）的 web 应用](./quickstart.md)
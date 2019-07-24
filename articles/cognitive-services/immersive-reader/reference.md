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
ms.openlocfilehash: 67da7d67a3165583a872c2b435c3cdca9763d4dd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443804"
---
# <a name="immersive-reader-sdk-reference"></a>沉浸式读者 SDK 参考

沉浸式读者 SDK 是一个 JavaScript 库, 可用于将沉浸式读者集成到 web 应用程序中。

## <a name="functions"></a>函数

SDK 公开单一函数`ImmersiveReader.launchAsync(token, subdomain, content, options)`。

### <a name="launchasync"></a>launchAsync

`iframe`在 web 应用程序中的内启动沉浸式读取器。

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parameters

| 名称 | 类型 | 描述 |
| ---- | ---- |------------ |
| `token` | string | Azure AD 身份验证令牌。 请参阅[Azure AD authentication 教程](./azure-active-directory-authentication.md)。 |
| `subdomain` | string | Azure 中沉浸式读者资源的自定义子域。 请参阅[Azure AD authentication 操作方法](./azure-active-directory-authentication.md)。 |
| `content` | [内容](#content) | 一个对象, 该对象包含要在沉浸式读取器中显示的内容。 |
| `options` | [选项](#options) | 用于配置沉浸式读者的某些行为的选项。 可选。 |

#### <a name="returns"></a>返回

返回一个`Promise<HTMLDivElement>`可解析沉浸式读取器的。 解析为一个`div`元素, 该元素的子元素`iframe`为包含沉浸式读者页的元素。 `Promise`

#### <a name="exceptions"></a>Exceptions

如果沉浸`Promise`式读取器加载失败[`Error`](#error) , 将使用对象拒绝返回的。 有关详细信息, 请参阅[错误代码](#error-codes)。

## <a name="types"></a>类型

### <a name="content"></a>内容

包含要在沉浸式阅读器中显示的内容。

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
| 文本/无格式 | 纯文本。 |
| application/mathml+xml | 数学标记语言 (MathML)。 [了解详细信息](https://developer.mozilla.org/en-US/docs/Web/MathML)。

### <a name="options"></a>选项

包含配置沉浸式读者的某些行为的属性。

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Error

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
| BadArgument | 提供的参数无效, 有关`message`详细信息, 请参阅。 |
| 超时 | 沉浸式读取器无法在指定的超时内加载。 |
| TokenExpired| 提供的令牌已过期。 |

## <a name="launching-the-immersive-reader"></a>启动沉浸式阅读器

SDK 为启动沉浸式阅读器的按钮提供默认样式。 `immersive-reader-button`使用 class 特性启用此样式设置。

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>可选属性

使用以下属性来配置按钮的外观。

| 特性 | 描述 |
| --------- | ----------- |
| `data-button-style` | 设置按钮的样式。 可以是 `icon`、`text` 或 `iconAndText`。 默认为 `icon`。 |
| `data-locale` | 设置区域设置, 例如`en-US`。 `fr-FR` 默认为英语。 |
| `data-icon-px-size` | 设置图标的大小 (以像素为单位)。 默认值为20px。 |

## <a name="browser-support"></a>浏览器支持

使用以下浏览器的最新版本, 以获取沉浸式读者的最佳体验。

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>后续步骤

* 探索 [GitHub 上的沉浸式阅读器 SDK](https://github.com/Microsoft/immersive-reader-sdk)
* [快速入门：创建用于启动沉浸式读者 (C#) 的 web 应用](./quickstart.md)
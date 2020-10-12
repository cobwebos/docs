---
title: 配置翻译
titleSuffix: Azure Cognitive Services
description: 本文将介绍如何配置各种翻译选项。
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ea066fab713100309103a040d309bac5b984fb99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85486902"
---
# <a name="how-to-configure-translation"></a>如何配置翻译

本文演示如何配置沉浸式阅读器中的各种翻译选项。

## <a name="configure-translation-language"></a>配置转换语言

`options`参数包含可用于配置转换的所有标志。 将 `language` 参数设置为要转换为的语言。 有关支持的语言的完整列表，请参阅 [语言支持](./language-support.md) 。

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>加载时自动翻译文档

设置 `autoEnableDocumentTranslation` 为 `true` 以启用当沉浸式读取器加载时自动翻译整个文档。

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>自动启用 word 翻译

设置 `autoEnableWordTranslation` 为 `true` 以启用单字翻译。

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>后续步骤

* 浏览[沉浸式阅读器 SDK 参考](./reference.md)
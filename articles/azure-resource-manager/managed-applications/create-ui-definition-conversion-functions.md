---
title: 创建 UI 定义转换函数
description: 介绍在数据类型和编码之间转换值时要使用的函数。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095981"
---
# <a name="createuidefinition-conversion-functions"></a>创建 UI 定义转换函数

可以使用这些函数在 JSON 数据类型和编码之间转换值。

## <a name="bool"></a>bool

将参数转换为布尔值。 此函数支持数字、字符串和布尔类型的参数。 与 JavaScript 中的布尔值相似，`0` 或 `'false'` 以外的任何值都返回 `true`。

以下示例返回 `true`：

```json
"[bool(1)]"
```

以下示例返回 `false`：

```json
"[bool(0)]"
```

以下示例返回 `true`：

```json
"[bool(true)]"
```

以下示例返回 `true`：

```json
"[bool('true')]"
```

## <a name="decodebase64"></a>decodeBase64

将参数从 base-64 编码的字符串中解码。 此函数仅支持字符串类型的参数。

以下示例返回 `"Contoso"`：

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

将参数从 URL 编码的字符串中解码。 此函数仅支持字符串类型的参数。

以下示例返回 `"https://portal.azure.com/"`：

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

将参数编码为 base-64 编码的字符串。 此函数仅支持字符串类型的参数。

以下示例返回 `"Q29udG9zbw=="`：

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

将参数编码为 URL 编码的字符串。 此函数仅支持字符串类型的参数。

以下示例返回 `"https%3A%2F%2Fportal.azure.com%2F"`：

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

将参数转换为浮点。 此函数支持数字和字符串类型的参数。

以下示例返回 `1.0`：

```json
"[float('1.0')]"
```

以下示例返回 `2.9`：

```json
"[float(2.9)]"
```

## <a name="int"></a>int

将参数转换为整数。 此函数支持数字和字符串类型的参数。

以下示例返回 `1`：

```json
"[int('1')]"
```

以下示例返回 `2`：

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

将参数转换为本机类型。 换而言之，此函数是 `string()` 函数的反函数。 此函数仅支持字符串类型的参数。

以下示例返回 `1`：

```json
"[parse('1')]"
```

以下示例返回 `true`：

```json
"[parse('true')]"
```

以下示例返回 `[1,2,3]`：

```json
"[parse('[1,2,3]')]"
```

以下示例返回 `{"type":"webapp"}`：

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>string

将参数转换为字符串。 此函数支持所有 JSON 数据类型的参数。

以下示例返回 `"1"`：

```json
"[string(1)]"
```

以下示例返回 `"2.9"`：

```json
"[string(2.9)]"
```

以下示例返回 `"[1,2,3]"`：

```json
"[string([1,2,3])]"
```

以下示例返回 `"{"type":"webapp"}"`：

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。

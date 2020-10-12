---
title: 创建 UI 定义字符串函数
description: 介绍为 Azure 托管应用程序构造 UI 定义时要使用的字符串函数
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095974"
---
# <a name="createuidefinition-string-functions"></a>创建 UI 定义字符串函数

这些函数可以与 JSON 字符串配合使用。

## <a name="concat"></a>concat

连接一个或多个字符串。

例如，如果 `element1` 的输出值是 `"Contoso"`，则此示例返回字符串 `"Demo Contoso app"`：

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

确定某个字符串是否以某个值结尾。

下面的示例返回 true。

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>GUID

生成全局唯一字符串 (GUID)。

下面的示例将返回一个值，如 `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`：

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

返回字符串中某个值的第一个位置，或者在找不到该值的情况下返回 -1。

下面的示例返回 2。

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

返回字符串中某个值的最后一个位置，或者在找不到该值的情况下返回 -1。

下面的示例返回 3。

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>replace

返回一个字符串，其中，当前字符串中出现的所有指定字符串均替换为另一个字符串。

以下示例返回 `"Contoso.com web app"`：

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

确定某个字符串是否以某个值开头。

下面的示例返回 true。

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

返回指定字符串的子字符串。 该子字符串在指定的索引处开始，并且具有指定的长度。

以下示例返回 `"web"`：

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

返回转换成小写形式的字符串。

以下示例返回 `"contoso"`：

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

返回转换成大写形式的字符串。

以下示例返回 `"CONTOSO"`：

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。


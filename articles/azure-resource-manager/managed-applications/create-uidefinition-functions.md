---
title: 创建 UI 定义函数
description: 介绍为 Azure 托管应用程序构造 UI 定义时要使用的函数
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: e4255f0d42e28a72ad55d9b7f81d0dc49b2950cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87040986"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition 函数

本文概述性介绍 CreateUiDefinition 支持函数。

## <a name="function-syntax"></a>函数语法

要使用函数，请将调用放在方括号内。 例如：

```json
"[function()]"
```

可将字符串和其他函数作为函数的参数进行引用，但字符串必须放在单引号内。 例如：

```json
"[fn1(fn2(), 'demo text')]"
```

如果适用，可以使用点运算符来引用函数的输出属性。 例如：

```json
"[func().prop1]"
```

## <a name="collection-functions"></a>集合函数

这些函数可用于集合，如 JSON 字符串、数组和对象。

* [contains](create-ui-definition-collection-functions.md#contains)
* [empty](create-ui-definition-collection-functions.md#empty)
* [filter](create-ui-definition-collection-functions.md#filter)
* [first](create-ui-definition-collection-functions.md#first)
* [last](create-ui-definition-collection-functions.md#last)
* [length](create-ui-definition-collection-functions.md#length)
* [map](create-ui-definition-collection-functions.md#map)
* [skip](create-ui-definition-collection-functions.md#skip)
* [split](create-ui-definition-collection-functions.md#split)
* [take](create-ui-definition-collection-functions.md#take)

## <a name="conversion-functions"></a>转换函数

可以使用这些函数在 JSON 数据类型和编码之间转换值。

* [bool](create-ui-definition-conversion-functions.md#bool)
* [decodeBase64](create-ui-definition-conversion-functions.md#decodebase64)
* [decodeUriComponent](create-ui-definition-conversion-functions.md#decodeuricomponent)
* [encodeBase64](create-ui-definition-conversion-functions.md#encodebase64)
* [encodeUriComponent](create-ui-definition-conversion-functions.md#encodeuricomponent)
* [float](create-ui-definition-conversion-functions.md#float)
* [int](create-ui-definition-conversion-functions.md#int)
* [parse](create-ui-definition-conversion-functions.md#parse)
* [string](create-ui-definition-conversion-functions.md#string)

## <a name="date-functions"></a>日期函数

* [addHours](create-ui-definition-date-functions.md#addhours)
* [addMinutes](create-ui-definition-date-functions.md#addminutes)
* [addSeconds](create-ui-definition-date-functions.md#addseconds)
* [utcNow](create-ui-definition-date-functions.md#utcnow)

## <a name="logical-functions"></a>逻辑函数

可以在条件语句中使用这些函数。 某些函数可能不支持所有的 JSON 数据类型。

* [and](create-ui-definition-logical-functions.md#and)
* [coalesce](create-ui-definition-logical-functions.md#coalesce)
* [equals](create-ui-definition-logical-functions.md#equals)
* [greater](create-ui-definition-logical-functions.md#greater)
* [greaterOrEquals](create-ui-definition-logical-functions.md#greaterorequals)
* [if](create-ui-definition-logical-functions.md#if)
* [less](create-ui-definition-logical-functions.md#less)
* [lessOrEquals](create-ui-definition-logical-functions.md#lessorequals)
* [not](create-ui-definition-logical-functions.md#not)
* [or](create-ui-definition-logical-functions.md#or)

## <a name="math-functions"></a>数学函数

* [add](create-ui-definition-math-functions.md#add)
* [ceil](create-ui-definition-math-functions.md#ceil)
* [div](create-ui-definition-math-functions.md#div)
* [floor](create-ui-definition-math-functions.md#floor)
* [max](create-ui-definition-math-functions.md#max)
* [min](create-ui-definition-math-functions.md#min)
* [mod](create-ui-definition-math-functions.md#mod)
* [mul](create-ui-definition-math-functions.md#mul)
* [rand](create-ui-definition-math-functions.md#rand)
* [range](create-ui-definition-math-functions.md#range)
* [sub](create-ui-definition-math-functions.md#sub)

## <a name="referencing-functions"></a>引用函数

* [basics](create-ui-definition-referencing-functions.md#basics)
* [location](create-ui-definition-referencing-functions.md#location)
* [resourceGroup](create-ui-definition-referencing-functions.md#resourcegroup)
* [steps](create-ui-definition-referencing-functions.md#steps)
* [subscription](create-ui-definition-referencing-functions.md#subscription)

## <a name="string-functions"></a>字符串函数

* [concat](create-ui-definition-string-functions.md#concat)
* [endsWith](create-ui-definition-string-functions.md#endswith)
* [guid](create-ui-definition-string-functions.md#guid)
* [indexOf](create-ui-definition-string-functions.md#indexof)
* [lastIndexOf](create-ui-definition-string-functions.md#lastindexof)
* [replace](create-ui-definition-string-functions.md#replace)
* [startsWith](create-ui-definition-string-functions.md#startswith)
* [substring](create-ui-definition-string-functions.md#substring)
* [toLower](create-ui-definition-string-functions.md#tolower)
* [toUpper](create-ui-definition-string-functions.md#toupper)

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。

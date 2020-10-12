---
title: 创建 UI 定义数学函数
description: 介绍进行数学运算时使用的函数。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095976"
---
# <a name="createuidefinition-math-functions"></a>创建 UI 定义数学函数

可以通过这些函数进行数学运算。

## <a name="add"></a>add

将两个数字相加并返回结果。

以下示例返回 `3`：

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

返回大于或等于指定数字的最大整数。

以下示例返回 `4`：

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

用第一个数字除以第二个数字并返回结果。 结果始终是整数。

以下示例返回 `2`：

```json
"[div(6, 3)]"
```

## <a name="floor"></a>floor

返回小于或等于指定数字的最大整数。

以下示例返回 `3`：

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

返回两个数字中的较大数。

以下示例返回 `2`：

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

返回两个数字中的较小数。

以下示例返回 `1`：

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

用第一个数字除以第二个数字并返回余数。

以下示例返回 `0`：

```json
"[mod(6, 3)]"
```

以下示例返回 `2`：

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

将两个数字相乘并返回结果。

以下示例返回 `6`：

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

生成指定范围内的随机整数。 此函数不生成经过加密的安全随机数字。

以下示例可能返回 `42`：

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

生成指定范围内的整数序列。

以下示例返回 `[1,2,3]`：

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

从第一个数字中减去第二个数字并返回结果。

以下示例返回 `1`：

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>后续步骤

* 有关 Azure 资源管理器的简介，请参阅 [Azure 资源管理器概述](../management/overview.md)。

---
title: 常规命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: aahi
ms.openlocfilehash: 693a81cfb15407541311d7ab053bb2ab6a267b29
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799703"
---
## <a name="general-entity-types"></a>常规实体类型：

### <a name="person"></a>人员
文本中识别的名称和其他人员。
语言：
* 公共预览版： `English`

| 子类型名称 | 说明             |
|--------------|-------------------------|
| 不适用          | 识别的名称，例如 `Bill Gates`、`Marie Curie` |

### <a name="location"></a>位置

自然和人工特征点、结构和地理功能。

语言：


* 公共预览版： `English`

| 子类型名称 | 说明                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| 不适用          | 位置，例如 `Atlantic Ocean`、`library`、`Eiffel Tower``Statue of Liberty` |

### <a name="organization"></a>组织  

认识到的组织、公司、机构和其他人组。 例如：公司、政治组、音乐带、运动俱乐部、政府机构和公共组织。 各族和 religions 不包含在此实体类型中。 语言： 

* 公共预览版： `English`

| 子类型名称 | 说明                                                                                      |
|--------------|--------------------------------------------------------------------------------------------------|
| 不适用          | 组织（例如 `Microsoft``NASA` `National Oceanic and Atmospheric Administration` |

### <a name="phone-number"></a>电话号码

电话号码。 

语言：


* 公共预览版： `English`

| 子类型名称 | 说明                                  |
|----------|----------------------------------------------|
| 不适用         | 电话号码，例如 `+1 123-123-123`。 |

### <a name="email"></a>电子邮件

电子邮件地址。 

语言：


* 公共预览版： `English`

| 子类型名称 | 说明                                  |
|----------|----------------------------------------------|
| 不适用         | 电子邮件地址，例如 `support@contoso.com` |

### <a name="url"></a>URL

Internet Url。

语言：


* 公共预览版： `English`

| 子类型名称 | 说明                                           |
|----------|-------------------------------------------------------|
| 不适用         | 网站的 Url，例如 `https://www.bing.com`。 |

###  <a name="number"></a>Number

数字和数字。 

语言：


* 公共预览版： `English`

| 子类型名称    | 示例                     |
|-------------|------------------------------|
| 不适用         | `6`、`six`                   |
| 百分比  | `50%`、`fifty percent`       |
| Ordinal     | `2nd`、`second`              |
| 货币    | `$10.99`、`€30.00`           |
| 维度   | `10 miles`、`40 cm`          |
| 温度 | `32 degrees`、`10°C`         |

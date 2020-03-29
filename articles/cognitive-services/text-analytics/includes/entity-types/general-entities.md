---
title: 一般命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77211426"
---
## <a name="general-entity-types"></a>常规实体类型：

### <a name="person"></a>人员

在文本中识别人员姓名。

语言：
* 公共预览： `Arabic` `Czech`、 `Chinese-Simplified` `Danish`、 `Dutch` `English`、 `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish``Turkish`

| 子类型名称 | 描述                                                      | 可从模型版本开始 |
|--------------|------------------------------------------------------------------|---------------------------------------|
| 空值          | 识别人员姓名，例如`Bill Gates`，`Marie Curie` | `2019-10-01`                          | 

### <a name="persontype"></a>人员类型
工作类型或人员担任的角色。

语言：
* 公共预览：`English`

| 子类型名称 | 描述                                                                                | 可从模型版本开始 |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| 空值          | 作业类型，例如`civil engineer` `salesperson`， `chef` `librarian`， ， 、 、 、`nursing aide` | `2020-02-01`                           |

### <a name="location"></a>位置

自然和人为的地标、结构、地理特征和地缘政治实体。

语言：

* 公共预览： `Arabic` `Czech`、 `Chinese-Simplified` `Danish`、 `Dutch` `English`、 `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish``Turkish`

| 子类型名称              | 描述                                                                              | 可从模型版本开始 |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| 空值                       | 位置，例如`Atlantic Ocean`， `library` `Eiffel Tower`，`Statue of Liberty`  | `2019-10-01`                           |
| 地缘政治实体 （GPE） - 仅限英语| 城市、国家/地区、州`Seattle`等`Pennsylvania` `South Africa`， 、`Tokyo` | `2020-02-01`                           |

### <a name="organization"></a>组织  

公认的组织、公司、机构和其他群体。 例如：公司、政治团体、乐队、体育俱乐部、政府机构和公共组织。 民族和宗教不包括在此实体类型中。 

语言： 

* 公共预览： `Arabic` `Czech`、 `Chinese-Simplified` `Danish`、 `Dutch` `English`、 `Finnish` `French` `German` `Hungarian` `Italian` `Japanese` `Korean` `Norwegian (Bokmål)` `Polish`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 `Portuguese (Portugal)` `Portuguese (Brazil)` `Russian` `Spanish` `Swedish``Turkish`

| 子类型名称 | 描述                                                                                             | 可从模型版本开始 |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| 空值          | 组织，例如`Microsoft`， `NASA` `National Oceanic and Atmospheric Administration``VOA` | `2019-10-01`                           |

### <a name="event"></a>事件  

历史、社会和自然事件。  

语言： 

* 公共预览：`English`

| 子类型名称 | 描述                                                            | 可从模型版本开始 |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| 空值          | 事件，如`wedding` `hurricane`， `car accident` `solar eclipse`， ，`American Revolution` | `2020-02-01`                           |

### <a name="product"></a>Products  

不同类别的物理对象。  

语言： 

* 公共预览：`English`

| 子类型名称 | 描述                                                                        | 可从模型版本开始 |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| 空值          | `Microsoft Surface laptop`例如， `sunglasses`、 `motorcycle`、 `bag`、 、`Xbox` | `2020-02-01`                           |
| 计算    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>技能  

描述能力或专业知识的实体。  

语言： 

* 公共预览：`English`

| 子类型名称 | 描述                                                                 | 可从模型版本开始 |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| 空值          | `nursing`, `data mining`, `linguistics`, `critical thinking`, `photography` | `2020-02-01`                           |

### <a name="phone-number"></a>电话号码

电话号码（仅限美国电话号码）。 

语言：

* 公共预览：`English`

| 子类型名称 | 描述                                    | 可从模型版本开始 |
|--------------|------------------------------------------------|----------------------------------------|
| 空值          | 美国电话号码，例如`(312) 555-0176` | `2019-10-01`                           |

### <a name="email"></a>电子邮件

电子邮件地址。 

语言：

* 公共预览：`English`

| 子类型名称 | 描述                                      | 可从模型版本开始 |
|--------------|--------------------------------------------------|----------------------------------------|
| 空值          | 电子邮件地址，例如`support@contoso.com` | `2019-10-01`                           |

### <a name="url"></a>代码

互联网网址。

语言：

* 公共预览：`English`

| 子类型名称 | 描述                                          | 可从模型版本开始 |
|--------------|------------------------------------------------------|----------------------------------------|
| 空值          | 网址到网站，例如`https://www.bing.com` | `2019-10-01`                           |

### <a name="ip-address"></a>IP 地址

互联网协议地址

语言：

* 公共预览：`English`

| 子类型名称 | 描述                              | 可从模型版本开始 |
|--------------|------------------------------------------|----------------------------------------|
| 空值          | 例如网络地址`10.0.0.101` | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

日期和时间实体。 

* 可从模型版本开始`2019-10-01`

语言：

* 公共预览： `Chinese-Simplified` `English`、 `French` `German` 、 和`Spanish`

| 子类型名称    | 示例                     |
|-------------|------------------------------|
| 空值         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| 时间     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| 设置 | `every Saturday`         |

###  <a name="quantity"></a>数量

数字和数字数量。 

* 可从模型版本开始`2019-10-01`

语言：

* 公共预览： `Chinese-Simplified` `English`、 `French` `German` 、 和`Spanish`

| 子类型名称    | 示例                     |
|-------------|------------------------------|
| Number         | `6`, `six`                   |
| 百分比  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| 年龄         | `90 day old`, `30 years old` |
| 货币    | `$10.99`, `€30.00`           |
| 维度   | `10 miles`, `40 cm`          |
| 温度 | `32 degrees`, `10°C`         |

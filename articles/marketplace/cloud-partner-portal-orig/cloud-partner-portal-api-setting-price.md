---
title: 虚拟机产品/服务的定价 | Microsoft Docs
description: 说明指定虚拟机产品/服务定价的三种方法。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: a029477dfd8046863ebfe34cd839562a0b1f3d87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094448"
---
<a name="pricing-for-virtual-machine-offers"></a>虚拟机产品/服务的定价
==================================

指定虚拟机产品/服务定价的方法有三种：自定义核心定价、按核心定价和电子表格定价。


<a name="customized-core-pricing"></a>自定义核心定价
-----------------------

定价针对每个区域和核心组合而定。 销售列表中的每个区域都必须在定义的 **virtualMachinePricing**/**regionPrices** 节中指定。  在请求中对每个[区域](#regions)使用正确的货币代码。  以下示例演示了这些要求：

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 2,
                    "1core": 2,
                    "2core": 3,
                    "4core": 4,
                    "6core": 5,
                    "8core": 2,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 4,
                    "36core": 4,
                    "40core": 4,
                    "64core": 4,
                    "128core": 4
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>按核心定价
----------------

在这种情况下，发布者会为其 SKU 指定一个美元价格，并自动生成所有其他价格。 每个核心的价格在请求的 **single** 参数中指定。

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>电子表格定价
-------------------

发布者还可以将其定价电子表格上传到临时存储位置，然后像其他文件项目一样在请求中包含 URI。 然后上传电子表格，进行转换以评估指定的价格表，最后使用定价信息更新产品/服务。 对产品/服务的后续 GET 请求将返回电子表格 URI 和该区域的评估价格。

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="regions"></a>区域
-------

下表显示了可以为自定义核心定价指定的不同区域及其对应的货币代码。

| **区域** | **名称**             | **货币代码** |
|------------|----------------------|-------------------|
| DZ         | 阿尔及利亚              | DZD               |
| AR         | 阿根廷            | ARS               |
| AU         | 澳大利亚            | AUD               |
| AT         | 奥地利              | EUR               |
| BH         | 巴林              | BHD               |
| BY         | 白俄罗斯              | RUB               |
| BE         | 比利时              | EUR               |
| BR         | 巴西               | USD               |
| BG         | 保加利亚             | BGN               |
| CA         | 加拿大               | CAD               |
| CL         | 智利                | CLP               |
| CO         | 哥伦比亚             | COP               |
| CR         | 哥斯达黎加           | CRC               |
| HR         | 克罗地亚              | HRK               |
| CY         | 塞浦路斯               | EUR               |
| CZ         | 捷克共和国       | CZK               |
| DK         | 丹麦              | DKK               |
| DO         | 多米尼加共和国   | USD               |
| EC         | 厄瓜多尔              | USD               |
| EG         | 埃及                | EGP               |
| SV         | 萨尔瓦多          | USD               |
| EE         | 爱沙尼亚              | EUR               |
| FI         | 芬兰              | EUR               |
| FR         | 法国               | EUR               |
| DE         | 德国              | EUR               |
| GR         | 希腊               | EUR               |
| GT         | 危地马拉            | GTQ               |
| HK         | 中国香港特别行政区        | HKD               |
| HU         | 匈牙利              | HUF               |
| IS         | 冰岛              | ISK               |
| IN         | 印度                | INR               |
| ID         | 印度尼西亚            | IDR               |
| IE         | 爱尔兰              | EUR               |
| IL         | 以色列               | ILS               |
| IT         | 意大利                | EUR               |
| JP         | 日本                | JPY               |
| JO         | 约旦               | JOD               |
| KZ         | 哈萨克斯坦           | KZT               |
| KE         | 肯尼亚                | KES               |
| KR         | 韩国                | KRW               |
| KW         | 科威特               | KWD               |
| LV         | 拉脱维亚               | EUR               |
| LI         | 列支敦士登        | CHF               |
| LT         | 立陶宛            | EUR               |
| LU         | 卢森堡           | EUR               |
| MK         | 北马其顿      | MKD               |
| MY         | 马来西亚             | MYR               |
| MT         | 马耳他                | EUR               |
| MX         | 墨西哥               | MXN               |
| ME         | 黑山           | EUR               |
| MA         | 摩洛哥              | MAD               |
| NL         | 荷兰          | EUR               |
| NZ         | 新西兰          | NZD               |
| NG         | 尼日利亚              | NGN               |
| 否         | 挪威               | NOK               |
| OM         | 阿曼                 | OMR               |
| PK         | 巴基斯坦             | PKR               |
| PA         | 巴拿马               | USD               |
| PY         | 巴拉圭             | PYG               |
| PE         | 秘鲁                 | PEN               |
| PH         | 菲律宾          | PHP               |
| PL         | 波兰               | PLN               |
| PT         | 葡萄牙             | EUR               |
| PR         | 波多黎各          | USD               |
| QA         | 卡塔尔                | QAR               |
| RO         | 罗马尼亚              | RON               |
| RU         | 俄罗斯               | RUB               |
| SA         | 沙特阿拉伯         | SAR               |
| RS         | 塞尔维亚               | RSD               |
| SG         | 新加坡            | SGD               |
| SK         | 斯洛伐克             | EUR               |
| SI         | 斯洛文尼亚             | EUR               |
| ZA         | 南非         | ZAR               |
| ES         | 西班牙                | EUR               |
| LK         | 斯里兰卡            | USD               |
| SE         | 瑞典               | SEK               |
| CH         | 瑞士          | CHF               |
| TW         | 中国台湾               | TWD               |
| TH         | 泰国             | THB               |
| TT         | 特立尼达和多巴哥  | TTD               |
| TN         | 突尼斯              | TND               |
| TR         | 土耳其               | TRY               |
| UA         | 乌克兰              | UAH               |
| AE         | 阿拉伯联合酋长国 | EUR               |
| GB         | 英国       | GBP               |
| 美国         | 美国        | USD               |
| UY         | 乌拉圭              | UYU               |
| VE         | 委内瑞拉            | USD               |
|  |  |  |

---
title: 虚拟机产品/服务的定价-Azure Marketplace
description: 介绍用于指定虚拟机产品/服务的定价的三种方法。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: fe8024c60e204a4ec72002c878f7753ae35aa00d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393813"
---
# <a name="pricing-for-virtual-machine-offers"></a>虚拟机套餐的定价

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

指定虚拟机套餐定价的方法有三种：自定义核心定价、按核心定价和电子表格定价。

## <a name="customized-core-pricing"></a>自定义核心定价

定价针对每个区域和核心组合而定。 销售列表中的每个区域都必须在定义的**virtualMachinePricing** / **regionPrices**部分中指定。  在请求中对每个[区域](#regions)使用正确的货币代码。  以下示例演示了这些要求：

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```

## <a name="per-core-pricing"></a>按核心定价

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

## <a name="spreadsheet-pricing"></a>电子表格定价

发布者还可以将其定价电子表格上传到临时存储位置，然后像其他文件项目一样在请求中包含 URI。 然后上传电子表格，进行转换以评估指定的价格表，最后使用定价信息更新套餐。 对产品/服务的后续 GET 请求将返回电子表格 URI 和该区域的评估价格。

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

## <a name="new-core-sizes-added-on-722019"></a>7/2/2019 上增加了新的核心大小

VM 发布者已在2019年7月2日向添加新的 Azure 虚拟机大小增加了新价格， (基于) 的内核数。  新价格适用于核心大小10、44、48、60、120、208和416。  对于现有 VM，将根据当前价格自动计算这些核心大小的新价格。 发布者截至2019年8月1日，以查看其他价格并进行任何所需的更改。  在此日期之后，如果发布者尚未重新发布，则这些新的核心大小的自动计算价格将生效。

## <a name="regions"></a>区域

下表显示了可以为自定义核心定价指定的不同区域及其对应的货币代码。

| **区域** | **Name**             | **货币代码** |
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
| CA         | Canada               | CAD               |
| CL         | 智利                | CLP               |
| CO         | 哥伦比亚             | COP               |
| CR         | 哥斯达黎加           | CRC               |
| HR         | 克罗地亚              | HRK               |
| CY         | 塞浦路斯               | EUR               |
| CZ         | 捷克       | CZK               |
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
| HK         | 香港特别行政区        | HKD               |
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
| MY         | 马来西亚             | MYR               |
| MT         | 马耳他                | EUR               |
| MX         | 墨西哥               | MXN               |
| ME         | 黑山           | EUR               |
| MA         | 摩洛哥              | MAD               |
| NL         | 荷兰          | EUR               |
| NZ         | 新西兰          | NZD               |
| NG         | 尼日利亚              | NGN               |
| MK         | 北马其顿      | MKD               |
| 是         | 挪威               | NOK               |
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
| GB         | United Kingdom       | GBP               |
| 美国         | United States        | USD               |
| UY         | 乌拉圭              | UYU               |
| VE         | 委内瑞拉            | USD               |
|  |  |  |

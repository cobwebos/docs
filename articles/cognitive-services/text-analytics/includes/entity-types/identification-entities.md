---
title: 标识实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140903"
---
此实体类别包括财务信息和官方形式的标识。 从模型版本开始可用 `2019-10-01` 。 下面列出了子类型。 

### <a name="financial-account-identification"></a>财务帐户标识

| 子类型名称               | 说明                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA 路由号码        | 美国银行家协会（ABA）传输路由号。                  |
| SWIFT 代码                 | 付款说明信息的 SWIFT 代码。                           |
| 信用卡                | 信用卡号。                                                       |
| 国际银行帐号 (IBAN)                  | 付款说明信息的 IBAN 编码。                            |


### <a name="government-and-countryregion-specific-identification"></a>政府和国家/地区特定标识

> [!NOTE]
> 以下财务和国家/地区特定的实体不随参数一起返回 `domain=phi` ：
> * 护照号码
> * 税 Id

以下实体按国家/地区进行分组和列出：

阿根廷
* 阿根廷民族标识（DNI）号

澳大利亚
* 澳大利亚登记卡号码
* 澳大利亚税金文件编号
* 澳大利亚驾照号码
* 澳大利亚医疗帐号
* 澳大利亚银行帐号

比利时
* 比利时国号

巴西 
* 巴西法定实体编号（CNPJ）
* 巴西 CPF 号
* 巴西国家/地区 ID 卡（RG）

加拿大
* 加拿大社会保险电话号码
* 加拿大驾照号码
* 加拿大银行帐号
* 加拿大 Passport 号码
* 加拿大个人健康标识号（PHIN）
* 加拿大运行状况服务号

智利
* 标识卡号 

中国
* 中国居民标识卡（中国）号

克罗地亚
* 克罗地亚标识卡号
* 克罗地亚个人标识（OIB）编号

捷克共和国
* 捷克语个人标识号

丹麦
* 丹麦个人识别码

欧盟（EU）
* 欧盟身份证号码
* 欧盟护照号码
* 欧盟驾驶执照号码
* 欧盟社会安全号码 (SSN) 或等效 ID
* 欧盟纳税标识号 (TIN)
* 欧盟借记卡号

芬兰
* 芬兰国 ID
* 芬兰护照号码

法国
* 法国国家/地区 ID 卡（CNI）
* 法国社会保障号（INSEE）
* 法国 Passport 号码
* 法国驾照号码

德国
* 德国标识卡号
* 德语 Passport 号码
* 德语驾照号码

希腊 
* 希腊国家/地区 ID 卡号

香港特别行政区
* 香港特别行政区标识卡（HKID）编号

印度
* 印度永久帐号（PAN）
* 印度唯一标识（Aadhaar）编号

印度尼西亚
* 印度尼西亚标识卡（KTP）编号

爱尔兰
* 爱尔兰个人公共服务（PPS）号码

以色列
* 以色列国家 ID
* 以色列银行帐号

意大利
* 意大利驱动程序的许可证 ID

日本
* 日本居民注册号
* 日本居住证号码
* 日本驾照号码
* 社交保险号码（SIN）
* 日本护照号码
* 日本银行帐号

马来西亚
* 马来西亚标识卡号

荷兰
* 荷兰公民服务（BSN）编号

新西兰
* 新西兰的健康部

挪威
* 挪威标识编号

菲律宾
* 菲律宾统一多用途 ID 号

波兰
* 波兰标识卡
* 波兰国 ID （PESEL）
* 波兰 Passport

葡萄牙 
* 葡萄牙公民卡编号

沙特阿拉伯
* 沙特阿拉伯国家 ID

新加坡
* 新加坡国家注册 ID 卡（NRIC）编号

南非
* 南非识别号

韩国
* 韩国居民注册号

西班牙 
* 西班牙社会保障号（SSN）

瑞典
* 瑞典国家 ID
* 瑞典 Passport 号码

台湾 
* 中国台湾地区 ID
* 台湾居民证书（ARC/TARC）
* 台湾护照号码

泰国
* 泰国人口标识代码

英国
* Passport ID
* 英国 驾照号码
* 英国 国家保险编号（NINO）
* 英国 国家运行状况服务号
* 英国 选举滚动编号
* 美国/英国 Passport 号码

美国
* U.S. 社会保障号（SSN）
* 美国驾照号码
* 美国/英国 Passport 号码
* 美国单独的纳税人标识号（某种）
* 药品强制机构（DEA）编号
* 美国银行帐号

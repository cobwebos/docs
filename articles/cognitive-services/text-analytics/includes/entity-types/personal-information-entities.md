---
title: 个人信息的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 6e46d58b3f1ef33902ab44043e954d9718d865af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508041"
---
## <a name="personal-information-entity-types"></a>个人信息实体类型：

### <a name="person"></a>人员
文本中识别的名称和其他人员。
语言：
* 公共预览版： `English`

| 子类型名称 | 说明             |
|--------------|-------------------------|
| 不适用          | 识别的名称，例如 `Bill Gates`、`Marie Curie` |

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

| 子类型名称           | 说明                                           |
|------------------------|-------------------------------------------------------|
| 不适用                    | 电话号码，例如 `+1 123-123-123`。          |
| 欧盟电话号码        | 欧盟的特定电话号码。         |
| 欧盟移动电话号码 | 与欧盟相关的移动电话号码。 |

### <a name="eu-gps-coordinates"></a>欧盟 GPS 坐标

 欧盟内位置的 GPS 坐标。 

语言：

* 公共预览版： `English`

| 子类型名称 | 说明                               |
|--------------|-------------------------------------------|
| 不适用          | 欧盟坐标 |

### <a name="azure-information"></a>Azure 信息

可识别的 Azure 信息，包括身份验证信息和连接字符串。 

语言：

* 公共预览版： `English`

| 子类型名称                          | 说明                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 身份验证密钥             | Azure DocumentDB 服务器的授权密钥。                           |
| Azure IAAS 数据库连接字符串 | Azure 基础结构即服务（IaaS）数据库的连接字符串。 |
| Azure SQL 连接字符串           | 用于 Azure SQL 数据库的连接字符串。                                |
| Azure IoT 连接字符串           | 适用于物联网（IoT）的连接字符串。                        |
| Azure 发布设置密码        | Azure 发布设置的密码。                                        |
| Azure Redis 缓存连接字符串   | 用于 Redis 的 Azure 缓存的连接字符串。                             |
| Azure SAS                             | Azure 软件即服务（SAS）的连接字符串。                     |
| Azure 服务总线连接字符串   | Azure 服务总线的连接字符串。                                |
| Azure 存储帐户密钥             | Azure 存储帐户的帐户密钥。                                   |
| Azure 存储帐户密钥（通用）   | Azure 存储帐户的通用帐户密钥。                           |
| SQL Server 连接字符串          | SQL server 的连接字符串。                                         |

### <a name="identification"></a>识别

语言：

* 公共预览版： `English`

#### <a name="financial-account-identification"></a>财务帐户标识

| 子类型名称               | 说明                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA 路由号码        | 美国银行家协会（ABA）传输路由号。                  |
| SWIFT 代码                 | 付款说明信息的 SWIFT 代码。                           |
| 信用卡                | 信用卡号。                                                       |
| IBAN 代码                  | 付款说明信息的 IBAN 编码。                            |

#### <a name="government-and-country-specific-identification"></a>政府和国家/地区特定的标识

以下实体按国家/地区进行分组和列出：

阿根廷
* 国家标识（DNI）号

澳大利亚
* 税号文件 
* 驱动程序的许可证 ID
* Passport ID
* 医疗帐号
* 银行帐号（例如支票、储蓄和借方科目）

比利时
* 国家/地区

巴西
* 法律实体编号（CNPJ）
* CPF 号
* 国家/地区 ID 卡（RG）

加拿大
* Passport ID
* 驱动程序的许可证 ID
* 健康保险编号
* 个人健康 ID 号（PHIN）
* 社会保障号
* 银行帐号（例如支票、储蓄和借方科目）

智利
* 标识卡号 

中国
* 标识卡号
* 居民 ID 卡（PRC）编号

克罗地亚
* ID 卡号
* 个人 ID （OIB）号

捷克共和国
* 国家/地区 ID 卡号

丹麦
* 个人 ID 号

欧盟（EU）
* 国家/地区 ID 号
* Passport ID
* 驱动程序的许可证 ID
* 社会保险号（SSN）或等效 ID
* 欧盟税务标识号（TIN）
* 欧盟借记卡号

芬兰
* 国家/地区 ID 号
* Passport ID

法国
* 国家/地区 ID 卡（CNI）
* 社会保障号（INSEE）
* Passport ID
* 驱动程序的许可证 ID

德国
* ID 卡号
* Passport ID
* 驱动程序的许可证 ID

希腊 
* 国家/地区 ID 卡号

香港
* ID 卡（HKID）编号

印度
* 永久帐号（PAN）
* 唯一 ID （Aadhaar）编号

印度尼西亚
* ID 卡号（KTP）

爱尔兰
* 个人公共服务（PPS）号码

以色列
* 国家/地区 ID
* 银行帐号（例如支票、储蓄和借方科目）

意大利
* 驱动程序的许可证 ID

日本
* 居民注册号
* 居住卡号
* 驱动程序的许可证 ID
* 社交保险号码（SIN）
* Passport ID
* 银行帐号（例如支票、储蓄和借方科目）

马来西亚
* ID 卡号

荷兰
* 公民服务（BSN）编号

新西兰
* 健康部

挪威
* ID 卡号

菲律宾
* 统一的多用途 ID 号

波兰
* ID 卡号
* 国家/地区 ID （PESEL）
* Passport ID

葡萄牙 
* 公民卡号

沙特阿拉伯
* 国家/地区 ID

新加坡
* 国家注册 ID 卡（NRIC）编号

南非
* ID 号
* 居民注册号

韩国
* 居民注册号

西班牙 
* 社会安全号码 (SSN)

瑞典
* 国家/地区 ID
* Passport ID

台湾 
* 国家/地区 ID
* 居民证书（ARC/TARC）编号
* Passport ID

泰国
* 人口标识代码

英国
* Passport ID
* 驱动程序的许可证 ID
* 国家保险编号（NINO）
* 国家运行状况服务（NHS）号

美国
* 社会安全号码 (SSN)
* 驱动程序的许可证 ID
* Passport ID
* 选举滚动编号
* 个人税款 ID 号（某种）
* 药品强制机构（DEA）编号
* 银行帐号（例如支票、储蓄和借方科目）
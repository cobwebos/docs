---
title: 个人信息的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: eedfe4f24797a9b564479b2f8d3f4d04b0751272
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779199"
---
> [!NOTE]
> 若要检测 (PHI) 受保护的运行状况信息，请使用 `domain=phi` 参数和模型版本 `2020-04-01` 或更高版本。
>
> 例如： `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi&model-version=2020-07-01`
 
将请求发送到终结点时，将返回以下实体类别 `/v3.1-preview.2/entities/recognition/pii` 。

| 类别   | Subcategory | 说明                          | 开始模型版本 | 注意 |
|------------|-------------|--------------------------------------|------------------------|---|
| 人员     | 不适用         | 人员姓名。  | `2019-10-01`  | 还返回了 `domain=phi` 。 |
| PersonType | 不适用         | 人员持有的作业类型或角色。 | `2020-02-01` | |
| PhoneNumber | 不适用 | 电话号码仅)  (美国和欧盟电话号码。 | `2019-10-01` | 还返回了 `domain=phi` 。 |
|组织  | 不适用 | 公司、政治组、音乐带、运动俱乐部、政府机构和公共组织。  | `2019-10-01` | 各族和 religions 不包含在此实体类型中。  |
|组织 | 医疗 | 医疗公司和团队。 | `2020-04-01` |  |
|组织 | 股票兑换 | 股票兑换组。 | `2020-04-01` |  |
| 组织 | 体育游戏 | 与体育相关的组织。 | `2020-04-01` |  |
| 地址 | 不可用 | 完整的邮件地址。  | `2020-04-01` | 还返回了 `domain=phi` 。 |
| 欧盟 GPS 坐标 | 不适用 | 欧盟内位置的 GPS 坐标。  | `2019-10-01` |  |
| 电子邮件 | 不适用 | 电子邮件地址。 | `2019-10-01` | 还返回了 `domain=phi` 。   |
| URL | 不适用 | 指向网站的 Url。 | `2019-10-01` | 还返回了 `domain=phi` 。 |
| IP | 不适用 | 网络 IP 地址。 | `2019-10-01` | 还返回了 `domain=phi` 。 |
| DateTime | 不适用 | 日期和时间。 | `2019-10-01` |  | 
| DateTime | Date | 日历日期。 | `2019-10-01` | 还返回了 `domain=phi` 。 |
| 数量 | 不适用 | 数字和数字。 | `2019-10-01` |  |
| 数量 | Age | 年龄段. | `2019-10-01` | | |

## <a name="azure-information"></a>Azure 信息

此实体类别包含可识别的 Azure 信息，包括身份验证信息和连接字符串。 从模型版本开始可用 `2019-10-01` 。 不随参数一起返回 `domain=phi` 。

| Subcategory                           | 说明                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 身份验证密钥             | Azure Cosmos DB 服务器的授权密钥。                           |
| Azure IAAS 数据库连接字符串和 Azure SQL 连接字符串 | Azure 基础结构即服务 (IaaS) 数据库的连接字符串和 SQL 连接字符串。 |
| Azure SQL 连接字符串           | Azure SQL 数据库中的数据库的连接字符串。                                |
| Azure IoT 连接字符串           | Azure IoT 的连接字符串。                        |
| Azure 发布设置密码        | Azure 发布设置的密码。                                        |
| Azure Redis 缓存连接字符串   | Redis 缓存的连接字符串。                             |
| Azure SAS                             | Azure 软件即服务 (SaaS) 的连接字符串。                     |
| Azure 服务总线连接字符串   | Azure 服务总线的连接字符串。                                 |
| Azure 存储帐户密钥             | Azure 存储帐户的帐户密钥。                                   |
| Azure 存储帐户密钥（通用）   | Azure 存储帐户的通用帐户密钥。                           |
| SQL Server 连接字符串          | 运行 SQL Server 的计算机的连接字符串。                                         |

## <a name="identification"></a>识别

[!INCLUDE [supported identification entities](./identification-entities.md)]

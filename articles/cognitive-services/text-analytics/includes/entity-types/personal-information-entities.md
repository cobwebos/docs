---
title: 个人信息的命名实体
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/30/2020
ms.author: aahi
ms.openlocfilehash: 81ed10f0b3b2a8042f0766f89bb99d7cad950fca
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140905"
---
> [!NOTE]
> 若要检测 `PHI` ，请使用 `domain=phi` 参数和模型版本 `2020-04-01` 或更高版本。
>
> 例如：`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
将请求发送到终结点时，将返回以下实体类别 `/v3.1-preview.1/entities/recognition/pii` 。

| Category   | 子类别 | 说明                          | 开始模型版本 | 注释 |
|------------|-------------|--------------------------------------|------------------------|---|
| 人员     | 空值         | 人员姓名。  | `2019-10-01`  | 还返回了 `domain=phi` 。 |
| PersonType | 空值         | 人员持有的作业类型或角色。 | `2020-02-01` | |
| PhoneNumber | 空值 | 电话号码（仅限美国和欧盟电话号码）。 | `2019-10-01` | 还返回`domain=phi` |
|组织  | 空值 | 公司、政治组、音乐带、运动俱乐部、政府机构和公共组织。  | `2019-10-01` | 各族和 religions 不包含在此实体类型中。  |
|组织 | 医疗 | 医疗公司和团队。 | `2020-04-01` | 还返回了 `domain=phi` 。 |
|组织 | 股票兑换 | 股票兑换组。 | `2020-04-01` | 还返回了 `domain=phi` 。 |
| 组织 | 体育游戏 | 与体育相关的组织。 | `2020-04-01` | 还返回了 `domain=phi` 。 |
| 地址 | 空值 | 完整的邮件地址。  | `2020-04-01` | 还返回了 `domain=phi` 。 |
| 欧盟 GPS 坐标 | 空值 | 欧盟内位置的 GPS 坐标。  | `2019-10-01` |  |
| 电子邮件 | 空值 | 电子邮件地址。 | `2019-10-01` | 还返回了 `domain=phi` 。   |
| URL | 空值 | 指向网站的 Url。 | `2019-10-01` | 还返回了 `domain=phi` 。 |
| IP | 空值 | 网络 IP 地址。 | `2019-10-01` | |
| DateTime | 空值 | 日期和时间。 | `2019-10-01` |  | 
| DateTime | Date | 日历日期。 | `2019-10-01` | 还返回了 `domain=phi` 。 |
| 数量 | 空值 | 数字和数字。 | `2019-10-01` |  |
| 数量 | Age | 年龄段. | `2019-10-01` | | |
| 疾病的国际分类（ICD-10-CM） | 空值 | 与国际分类疾病，第九版相关的实体。   | `2020-04-01` | 还返回了 `domain=phi` 。 |
| 疾病的国际分类（ICD-10-CM） | 空值 | 与国际分类疾病，第10个版本相关的实体。    | `2020-04-01` | 还返回了 `domain=phi` 。 |

## <a name="azure-information"></a>Azure 信息

此实体类别包含可识别的 Azure 信息，包括身份验证信息和连接字符串。 从模型版本开始可用 `2019-10-01` 。 不随参数一起返回 `domain=phi` 。

| 子类别                           | 说明                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB 身份验证密钥             | Azure DocumentDB 服务器的授权密钥。                           |
| Azure IAAS 数据库连接字符串和 Azure SQL 连接字符串 | Azure 基础结构即服务（IaaS）数据库的连接字符串和 SQL 连接字符串。 |
| Azure SQL 连接字符串           | 用于 Azure SQL 数据库的连接字符串。                                |
| Azure IoT 连接字符串           | 适用于物联网（IoT）的连接字符串。                        |
| Azure 发布设置密码        | Azure 发布设置的密码。                                        |
| Azure Redis 缓存连接字符串   | 用于 Redis 的 Azure 缓存的连接字符串。                             |
| Azure SAS                             | Azure 软件即服务（SAS）的连接字符串。                     |
| Azure 服务总线连接字符串   | Azure 服务总线的连接字符串。                                 |
| Azure 存储帐户密钥             | Azure 存储帐户的帐户密钥。                                   |
| Azure 存储帐户密钥（通用）   | Azure 存储帐户的通用帐户密钥。                           |
| SQL Server 连接字符串          | SQL server 的连接字符串。                                         |

## <a name="identification"></a>识别

[!INCLUDE [supported identification entities](./identification-entities.md)]
---
title: "Azure Database for MySQL 中的限制 | Microsoft Docs"
description: "说明 Azure Database for MySQL 中的预览限制。"
services: mysql
author: jasonh
ms.author: kamathsun
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c19304ec2605faa3d69e82ac41d26ec54fee6ac7
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="limitations-in-azure-database-for-mysql-preview"></a>Azure Database for MySQL（预览版）中的限制
Azure Database for MySQL 服务目前为公共预览版。 下列各部分介绍数据库服务中的容量和功能限制。

## <a name="service-tier-maximums"></a>服务层最大值
创建服务器时，Azure Database for MySQL 中提供多个可供选择的服务层。 有关详细信息，请参阅[了解每个服务层中有哪些可用资源](concepts-service-tiers.md)。  

在服务预览版期间，每个服务层中的连接数、计算单元数和存储均有最大值，如下所示： 

|                            |                   |
| :------------------------- | :---------------- |
| 最大连接数        |                   |
| 基本 50 个计算单元     | 50 个连接    |
| 基本 100 个计算单元    | 100 个连接   |
| 标准 100 个计算单元 | 200 个连接   |
| 标准 200 个计算单元 | 300 个连接   |
| 标准 400 个计算单元 | 400 个连接   |
| 标准 800 个计算单元 | 500 个连接   |
| 最大计算单元数      |                   |
| 基本服务层         | 100 个计算单元 |
| 标准服务层      | 800 个计算单元 |
| 最大存储            |                   |
| 基本服务层         | 1 TB              |
| 标准服务层      | 1 TB              |

连接数过多时，可能会收到以下错误：
> 错误 1040 (08004): 连接过多

## <a name="preview-functional-limitations"></a>预览版功能限制：
### <a name="scale-operations"></a>缩放操作：
1.    目前不支持跨服务层动态缩放服务器。 也就是说，不能在基本和标准服务层之间进行切换。
2.    目前不支持在预先创建的服务器上按需动态增加存储大小。
3.    不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级：
- 目前不支持在主要数据库引擎版本之间进行自动迁移。

### <a name="subscription-management"></a>订阅管理：
- 目前不支持跨订阅和资源组动态移动预先创建的服务器。

### <a name="point-in-time-restore"></a>时间点还原：
1.    不允许还原到不同的服务层和/或计算单元和存储大小。
2.    不支持还原已删除的服务器。

## <a name="next-steps"></a>后续步骤：
[每个服务层中有哪些可用资源](concepts-service-tiers.md)
[支持的 MySQL 数据库版本](concepts-supported-versions.md)


---
title: Azure SQL 数据库托管实例时区 | Microsoft Docs
description: 了解有关 Azure SQL 数据库托管实例时区的具体信息
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: e81ae2fc563300402339fc40893fbbdbbd326dcd
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70233240"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Azure SQL 数据库托管实例中的时区

协调世界时 (UTC) 是云解决方案数据层的建议时区。 Azure SQL 数据库托管实例还提供时区的选项，来满足存储日期和时间值以及使用特定时区的隐式上下文调用日期和时间函数的现有应用程序的需求。

[GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) 等 T-SQL 函数或 CLR 代码会观察在实例级别设置的时区。 SQL Server 代理作业也会根据实例的时区遵循计划。

  >[!NOTE]
  > 托管实例是支持时区设置的 Azure SQL 数据库的唯一部署选项。 其他部署选项始终遵循 UTC。
如果需要解释非 UTC 时区中的日期和时间信息，请在单一和共用 SQL 数据库中使用 [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)。

## <a name="supported-time-zones"></a>支持的时区

支持的时区集继承自托管实例的底层操作系统。 它会定期更新，以获取新的时区定义并反映对现有时区所做的更改。

[夏时制/时区更改策略](https://aka.ms/time)保证了 2010 年以来的历史记录的准确性。

受支持时区名称的列表是通过 [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) 系统视图公开的。

## <a name="set-a-time-zone"></a>设置时区

只能在创建实例期间设置托管实例的时区。 默认时区为 UTC。

  >[!NOTE]
  > 无法更改现有托管实例的时区。

### <a name="set-the-time-zone-through-the-azure-portal"></a>通过 Azure 门户设置时区

输入新实例的参数时，请从受支持时区列表中选择一个时区。
  
![在创建实例期间设置时区](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

创建实例期间，在[资源管理器模板](https://aka.ms/sql-mi-create-arm-posh)中指定 timezoneId 属性来设置时区。

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

本文末尾提供了 timezoneId 属性的支持值列表。

如果未指定该属性，时区将设置为 UTC。

## <a name="check-the-time-zone-of-an-instance"></a>检查实例的时区

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) 函数返回实例时区的显示名称。

## <a name="cross-feature-considerations"></a>跨功能注意事项

### <a name="restore-and-import"></a>还原和导入

可以从使用不同时区设置的实例或服务器还原备份文件或者将数据导入托管实例。 请务必谨慎执行此操作。 分析应用程序的行为以及查询和报告的结果，如同在使用不同时区设置的两个 SQL Server 实例之间传输数据时一样。

### <a name="point-in-time-restore"></a>时间点还原

执行时间点还原时，要还原到的时间将解释为 UTC 时间。 这样, 就可以避免因夏令时而产生的任何歧义, 并避免其发生更改。

### <a name="auto-failover-groups"></a>自动故障转移组

不强制要求在故障转移组中的主要和辅助实例之间使用相同的时区，但我们强烈建议这样做。

  >[!WARNING]
  > 我们强烈建议对故障转移组中的主要和辅助实例使用相同的时区。 由于某些罕见用例不会强制实施跨主实例和辅助实例的相同时区。 必须知道，在手动或自动故障转移时，辅助实例将保留其原始时区。

## <a name="limitations"></a>限制

- 无法更改现有托管实例的时区。
- 从 SQL Server 代理作业启动的外部进程不会观察实例的时区。

## <a name="list-of-supported-time-zones"></a>支持的时区列表

| **时区 ID** | **时区显示名称** |
| --- | --- |
| 国际日期变更线标准时间 | (UTC-12:00) 国际日期变更线以西 |
| UTC-11 | (UTC-11:00) 协调世界时-11 |
| 阿留申群岛标准时间 | (UTC-10:00) 阿留申群岛 |
| 夏威夷标准时间 | (UTC-10:00)夏威夷 |
| 马克萨斯群岛标准时间 | (UTC-09:30) 马克萨斯群岛 |
| 阿拉斯加标准时间 | (UTC-09:00) 阿拉斯加 |
| UTC-09 | (UTC-09:00) 协调世界时-09 |
| 太平洋标准时间（墨西哥） | (UTC-08:00) 下加利福尼亚 |
| UTC-08 | (UTC-08:00) 协调世界时-08 |
| 太平洋标准时间 | (UTC-08:00) 太平洋时间（美国和加拿大） |
| 美国山地标准时间 | (UTC-07:00) 亚利桑那 |
| 山地标准时间（墨西哥） | (UTC-07:00) 奇瓦瓦，拉巴斯，马萨特兰 |
| 山地标准时间 | (UTC-07:00) 山地时间（美国和加拿大） |
| 中美洲标准时间 | (UTC-06:00) 中美洲 |
| 中部标准时间 | (UTC-06:00) 中部时间（美国和加拿大） |
| 复活节岛标准时间 | (UTC-06:00) 复活节岛 |
| 中部标准时间（墨西哥） | (UTC-06:00) 瓜达拉哈拉，墨西哥市，蒙特雷 |
| 加拿大中部标准时间 | (UTC-06:00) 萨斯喀彻温 |
| 南美洲太平洋标准时间 | (UTC-05:00) 波哥大，利马，基多，里约布兰科 |
| 东部标准时间（墨西哥） | (UTC-05:00) 切图马尔 |
| 东部标准时间 | (UTC-05:00) 东部时间（美国和加拿大） |
| 海地标准时间 | (UTC-05:00) 海地 |
| 古巴标准时间 | (UTC-05:00) 哈瓦那 |
| 美国东部标准时间 | (UTC-05:00) 印地安那（东部） |
| 特克斯和凯科斯群岛标准时间 | (UTC-05:00) 特克斯和凯科斯群岛 |
| 巴拉圭标准时间 | (UTC-04:00) 亚松森 |
| 大西洋标准时间 | (UTC-04:00) 大西洋时间（加拿大） |
| 委内瑞拉标准时间 | (UTC-04:00) 加拉加斯 |
| 巴西中部标准时间 | (UTC-04:00) 库亚巴 |
| 南美洲西部标准时间 | (UTC-04:00) 乔治敦，拉巴斯，马瑙斯，圣胡安 |
| 太平洋南美洲标准时间 | (UTC-04:00) 圣地亚哥 |
| 纽芬兰标准时间 | (UTC-03:30) 纽芬兰 |
| 托坎廷斯标准时间 | (UTC-03:00) 阿拉瓜伊纳 |
| E. 南美洲标准时间 | (UTC-03:00) 巴西利亚 |
| 南美洲东部标准时间 | (UTC-03:00) 卡宴，福塔雷萨 |
| 阿根廷标准时间 | (UTC-03:00) 布宜诺斯艾利斯 |
| 格陵兰标准时间 | (UTC-03:00) 格陵兰 |
| 蒙得维的亚标准时间 | (UTC-03:00) 蒙得维的亚 |
| 麦哲伦标准时间 | (UTC-03:00) 蓬塔阿雷纳斯 |
| 圣皮埃尔标准时间 | (UTC-03:00) 圣皮埃尔和密克隆 |
| 巴伊亚标准时间 | (UTC-03:00) 萨尔瓦多 |
| UTC-02 | (UTC-02:00) 协调世界时-02 |
| 中大西洋标准时间 | (UTC-02:00) 中大西洋 - 旧称 |
| 亚速尔群岛标准时间 | (UTC-01:00) 亚速尔群岛 |
| 佛得角标准时间 | (UTC-01:00) 佛得角群岛 |
| UTC | (UTC) 协调世界时 |
| GMT 标准时间 | (UTC+00:00) 都柏林，爱丁堡，里斯本，伦敦 |
| 格林威治标准时间 | (UTC+00:00) 蒙罗维亚，雷克雅未克 |
| 西欧 标准时间 | (UTC+01:00) 阿姆斯特丹，柏林，伯尔尼，罗马，斯德哥尔摩，维也纳 |
| 中欧标准时间 | (UTC+01:00) 贝尔格莱德，布拉迪斯拉发，布达佩斯，卢布尔雅那，布拉格 |
| 罗马标准时间 | (UTC+01:00) 布鲁塞尔，哥本哈根，马德里，巴黎 |
| 摩洛哥标准时间 | (UTC+01:00) 卡萨布兰卡 |
| 圣多美标准时间 | (UTC+01:00) 圣多美 |
| 中欧标准时间 | (UTC+01:00) 萨拉热窝，斯科普里，华沙，萨格勒布 |
| 中非 西部标准时间 | (UTC+01:00) 中非西部 |
| 约旦标准时间 | (UTC+02:00) 安曼 |
| GTB 标准时间 | (UTC+02:00) 雅典，布加勒斯特 |
| 中东标准时间 | (UTC+02:00) 贝鲁特 |
| 埃及标准时间 | (UTC+02:00) 开罗 |
| E. 标准时间 | (UTC+02:00) 基希讷乌 |
| 叙利亚标准时间 | (UTC+02:00) 大马士革 |
| 约旦河西岸标准时间 | (UTC+02:00) 加沙，希伯伦 |
| 南非标准时间 | (UTC+02:00) 哈拉雷，比勒陀利亚 |
| FLE 标准时间 | (UTC+02:00) 赫尔辛基，基辅，里加，索非亚，塔林，维尔纽斯 |
| 以色列标准时间 | (UTC+02:00) 耶路撒冷 |
| 加里宁格勒标准时间 | (UTC+02:00) 加里宁格勒 |
| 苏丹标准时间 | (UTC+02:00) 喀土木 |
| 利比亚标准时间 | (UTC+02:00) 的黎波里 |
| 纳米比亚标准时间 | (UTC+02:00) 温得和克 |
| 阿拉伯 (Arabic) 标准时间 | (UTC+03:00) 巴格达 |
| 土耳其标准时间 | (UTC+03:00) 伊斯坦布尔 |
| 阿拉伯 (Arab) 标准时间 | (UTC+03:00) 科威特，利雅得 |
| 白俄罗斯标准时间 | (UTC+03:00) 明斯克 |
| 俄罗斯标准时间 | (UTC+03:00) 莫斯科，圣彼得堡 |
| E. 东非标准时间 | (UTC+03:00) 内罗毕 |
| 伊朗标准时间 | (UTC+03:30) 德黑兰 |
| 阿拉伯半岛标准时间 | (UTC+04:00) 阿布扎比，马斯喀特 |
| 阿斯特拉罕标准时间 | (UTC+04:00) 阿斯特拉罕州，乌里扬诺夫斯克州 |
| 阿塞拜疆标准时间 | (UTC+04:00) 巴库 |
| 俄罗斯的时区 3 | (UTC+04:00) 伊热夫斯克，萨马拉 |
| 毛里求斯标准时间 | (UTC+04:00) 路易港 |
| 萨拉托夫标准时间 | (UTC+04:00) 萨拉托夫 |
| 格鲁吉亚标准时间 | (UTC+04:00) 第比利斯 |
| 伏尔加格勒标准时间 | (UTC+04:00) 伏尔加格勒 |
| 高加索标准时间 | (UTC+04:00) 埃里温 |
| 阿富汗标准时间 | (UTC+04:30) 喀布尔 |
| 西亚标准时间 | (UTC+05:00) 阿什哈巴德，塔什干 |
| 叶卡捷琳堡标准时间 | (UTC+05:00) 叶卡捷琳堡 |
| 巴基斯坦标准时间 | (UTC+05:00) 伊斯兰堡，卡拉奇 |
| 印度标准时间 | (UTC+05:30) 钦奈，加尔各答，孟买，新德里 |
| 斯里兰卡标准时间 | (UTC+05:30) 斯里加亚渥登普拉 |
| 尼泊尔标准时间 | (UTC+05:45) 加德满都 |
| 中亚标准时间 | (UTC+06:00) 阿斯塔纳 |
| 孟加拉标准时间 | (UTC+06:00) 达卡 |
| 鄂木斯克标准时间 | (UTC+06:00) 鄂木斯克 |
| 缅甸标准时间 | (UTC+06:30) 仰光 |
| 东南亚标准时间 | (UTC+07:00) 曼谷，河内，雅加达 |
| 阿尔泰标准时间 | (UTC+07:00) 巴尔瑙尔，戈尔诺-阿尔泰斯克 |
| 西 蒙古标准时间 | (UTC+07:00) 科布多 |
| 北亚标准时间 | (UTC+07:00) 克拉斯诺亚尔斯克 |
| 中北 亚标准时间 | (UTC+07:00) 新西伯利亚 |
| 托木斯克标准时间 | (UTC+07:00) 托木斯克 |
| 中国标准时间 | (UTC+08:00) 北京，重庆，香港特别行政区，乌鲁木齐 |
| 东北亚标准时间 | (UTC+08:00) 伊尔库次克 |
| 新加坡标准时间 | (UTC+08:00) 吉隆坡，新加坡 |
| 西 澳大利亚标准时间 | (UTC+08:00) 珀斯 |
| 台北标准时间 | (UTC+08:00) 台北 |
| 乌兰巴托标准时间 | (UTC+08:00) 乌兰巴托 |
| 澳大利亚中西部标准时间 | (UTC+08:45) 尤克拉 |
| 外贝加尔标准时间 | (UTC+09:00) 赤塔 |
| 东京标准时间 | (UTC+09:00) 大阪，札幌，东京 |
| 朝鲜标准时间 | (UTC + 09:00) 平壤 |
| 韩国标准时间 | (UTC+09:00) 首尔 |
| 雅库茨克标准时间 | (UTC+09:00) 雅库茨克 |
| 中部 澳大利亚标准时间 | (UTC+09:30) 阿德莱德 |
| 澳大利亚中部标准时间 | (UTC+09:30) 达尔文 |
| E. 澳大利亚标准时间 | (UTC+10:00) 布里斯班 |
| 澳大利亚东部标准时间 | (UTC+10:00) 堪培拉，墨尔本，悉尼 |
| 太平洋西部标准时间 | (UTC+10:00) 关岛，莫尔兹比港 |
| 塔斯马尼亚岛标准时间 | (UTC+10:00) 霍巴特 |
| 符拉迪沃斯托克标准时间 | (UTC+10:00) 符拉迪沃斯托克 |
| 豪勋爵岛标准时间 | (UTC+10:30) 豪勋爵岛 |
| 布干维尔岛标准时间 | (UTC+11:00) 布干维尔岛 |
| 俄罗斯时区 10 | (UTC+11:00) 乔库尔达赫 |
| 马加丹标准时间 | (UTC+11:00) 马加丹 |
| 诺福克岛标准时间 | (UTC+11:00) 诺福克岛 |
| 萨哈林标准时间 | (UTC+11:00) 库页岛 |
| 太平洋中部标准时间 | (UTC+11:00) 所罗门群岛，新喀里多尼亚 |
| 俄罗斯时区 11 | (UTC+12:00) 阿纳德尔、堪察加彼得罗巴甫洛夫斯克 |
| 新西兰标准时间 | (UTC+12:00) 奥克兰，惠灵顿 |
| UTC+12 | (UTC+12:00) 协调世界时+12 |
| 斐济标准时间 | (UTC+12:00) 斐济 |
| 堪察加标准时间 | (UTC+12:00) 彼得罗巴甫洛夫斯克-堪察加 - 旧称 |
| 查塔姆群岛标准时间 | (UTC+12:45) 查塔姆群岛 |
| UTC+13 | (UTC+13:00) 协调世界时+13 |
| 汤加标准时间 | (UTC+13:00) 努库阿洛法 |
| 萨摩亚群岛标准时间 | (UTC+13:00) 萨摩亚 |
| 莱恩群岛标准时间 | (UTC+14:00) 基里巴斯岛 |

## <a name="see-also"></a>请参阅 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

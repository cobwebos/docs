---
title: 托管实例时区
description: 了解 Azure SQL 数据库托管实例的时区细节
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79256089"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Azure SQL 数据库托管实例中的时区

协调世界时（UTC）是云解决方案数据层的建议时区。 Azure SQL 数据库托管实例还提供了一系列时区选项，以满足存储日期和时间值的现有应用程序的需求，并使用特定时区的隐式上下文调用日期和时间函数。

T-sql 函数[（如 GETDATE （）](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)或 CLR 代码）观察在实例级别上设置的时区。 SQL Server 代理作业还根据实例的时区遵循计划。

  >[!NOTE]
  > 托管实例是支持时区设置的 Azure SQL 数据库的唯一部署选项。 其他部署选项始终遵循 UTC。
如果需要在非 UTC 时区中解释日期和时间信息，请在单个和共用的 SQL 数据库中使用[AT](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)时区。

## <a name="supported-time-zones"></a>支持的时区

一组受支持的时区从托管实例的基础操作系统继承而来。 定期对其进行更新以获取新的时区定义，并反映现有的更改。

[夏令时/时区更改策略](https://aka.ms/time)保证了2010前的历史准确性。

具有受支持时区名称的列表通过[sys.databases. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)系统视图公开。

## <a name="set-a-time-zone"></a>设置时区

只能在创建实例期间设置托管实例的时区。 默认时区为 UTC。

  >[!NOTE]
  > 不能更改现有托管实例的时区。

### <a name="set-the-time-zone-through-the-azure-portal"></a>通过 Azure 门户设置时区

为新实例输入参数时，请从受支持的时区列表中选择一个时区。
  
![在实例创建过程中设置时区](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

指定[资源管理器模板](https://aka.ms/sql-mi-create-arm-posh)中的 timezoneId 属性，以便在实例创建过程中设置时区。

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

本文末尾提供了 timezoneId 属性的受支持值列表。

如果未指定，则将时区设置为 UTC。

## <a name="check-the-time-zone-of-an-instance"></a>检查实例的时区

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)函数返回实例时区的显示名称。

## <a name="cross-feature-considerations"></a>跨功能注意事项

### <a name="restore-and-import"></a>还原和导入

您可以还原备份文件或将数据从具有不同时区设置的实例或服务器导入到托管实例。 请务必小心。 分析应用程序行为和查询和报表的结果，就像在两个具有不同时区设置的 SQL Server 实例之间传输数据时。

### <a name="point-in-time-restore"></a>时点还原

执行时点还原时，还原到的时间将解释为 UTC 时间。 这样，就可以避免因夏令时而产生的任何歧义，并避免其发生更改。

### <a name="auto-failover-groups"></a>自动故障转移组

不会强制对故障转移组中的主实例和辅助实例使用同一时区，但我们强烈建议这样做。

  >[!WARNING]
  > 强烈建议对故障转移组中的主实例和辅助实例使用相同的时区。 由于某些罕见用例不会强制实施跨主实例和辅助实例的相同时区。 必须了解的是，对于手动或自动故障转移，辅助实例将保留其原始时区。

## <a name="limitations"></a>限制

- 不能更改现有托管实例的时区。
- 从 SQL Server 代理作业启动的外部进程不会观察到该实例的时区。

## <a name="list-of-supported-time-zones"></a>支持的时区列表

| **时区 ID** | **时区显示名称** |
| --- | --- |
| 国际日期变更线标准时间 | （UTC-12:00）国际日期行西部 |
| UTC-11 | （UTC-11:00）协调世界时-11 |
| 阿留申标准时间 | （UTC-10:00）阿留申群岛 |
| 夏威夷标准时间 | （UTC-10:00）夏威夷 |
| 马克萨斯标准时间 | （UTC-09:30）马克萨斯群岛 |
| 阿拉斯加标准时间 | （UTC-09:00）阿拉斯加 |
| UTC-09 | （UTC-09:00）协调世界时-09 |
| 太平洋标准时间（墨西哥） | （UTC-08:00）上加利福尼亚 |
| UTC-08 | （UTC-08:00）协调世界时-08 |
| 太平洋标准时间 | （UTC-08:00）太平洋时间（美国 &，加拿大） |
| 美国山地标准时间 | （UTC-07:00）亚利桑那 |
| 山地标准时间（墨西哥） | （UTC-07:00）奇瓦瓦、La 巴斯、马萨特兰 |
| 山地标准时间 | （UTC-07:00）山地时间（美国 &，加拿大） |
| 中美洲标准时间 | （UTC-06:00）中美洲 |
| 中部标准时间 | （UTC-06:00）中央时间（加拿大 &） |
| 复活节岛标准时间 | （UTC-06:00）复活节岛 |
| 中部标准时间（墨西哥） | （UTC-06:00）瓜达拉哈拉，墨西哥城，蒙特雷 |
| 加拿大中部标准时间 | （UTC-06:00）喀 |
| 南美洲太平洋标准时间 | （UTC-05:00）波哥大，利马，基多，Rio 布朗库 |
| 东部标准时间（墨西哥） | （UTC-05:00）切图马尔 |
| 东部标准时间 | （UTC-05:00）东部时间（美国 &） |
| 海地标准时间 | （UTC-05:00）海地 |
| 古巴标准时间 | （UTC-05:00）哈瓦那 |
| 美国东部标准时间 | （UTC-05:00）印地安那州（东部） |
| 特克斯和凯科斯群岛标准时间 | （UTC-05:00）特克斯和凯科斯群岛 |
| 巴拉圭标准时间 | （UTC-04:00）亚松森 |
| 大西洋标准时间 | （UTC-04:00）大西洋时间（加拿大） |
| 委内瑞拉标准时间 | （UTC-04:00）加拉加斯 |
| 巴西中部标准时间 | （UTC-04:00）库亚巴 |
| 南美洲西部标准时间 | （UTC-04:00）乔治敦、La 巴斯、马瑙斯、San Juan |
| 太平洋南美洲标准时间 | （UTC-04:00）圣地亚哥 |
| 纽芬兰标准时间 | （UTC-03:30）纽芬兰 |
| Tocantins 标准时间 | （UTC-03:00）Araguaina |
| E. 南美标准时间 | （UTC-03:00）巴西利亚 |
| 南美洲东部标准时间 | （UTC-03:00）卡宴、Fortaleza |
| 阿根廷标准时间 | （UTC-03:00）布宜诺斯艾利斯 |
| 格陵兰标准时间 | （UTC-03:00）格陵兰 |
| 蒙得维的亚标准时间 | （UTC-03:00）蒙得维的亚 |
| Magallanes 标准时间 | （UTC-03:00）蓬纳斯 |
| 圣皮埃尔标准时间 | （UTC-03:00）圣皮埃尔和密克隆群岛 |
| 巴伊亚标准时间 | （UTC-03:00）萨尔瓦多 |
| UTC-02 | （UTC-02:00）协调世界时-02 |
| 中大西洋标准时间 | （UTC-02:00）中大西洋-旧 |
| 亚速尔群岛标准时间 | （UTC-01:00）亚速尔群岛 |
| 佛得角群岛标准时间 | （UTC-01:00）佛得角 |
| UTC | (UTC)协调世界时 |
| GMT 标准时间 | （UTC + 00：00）都柏林，爱丁堡，里斯本，伦敦 |
| 格林威治标准时间 | （UTC + 00：00）蒙罗维亚，雷克雅未克 |
| W. 欧洲标准时间 | （UTC + 01：00）阿姆斯特丹，柏林，伯尔尼，罗马，斯德哥尔摩，维也纳 |
| 中欧标准时间 | （UTC + 01：00）贝尔格莱德，Bratislava，布达佩斯，卢布尔雅那，布拉格 |
| 罗马标准时间 | （UTC + 01：00）布鲁塞尔，哥本哈根，马德里，巴黎 |
| 摩洛哥标准时间 | （UTC + 01：00）Casablanca |
| 圣多美圣多美标准时间 | （UTC + 01：00）圣多美圣多美 |
| 中欧的标准时间 | （UTC + 01：00）萨拉热窝，斯科普里，华沙，萨格勒布 |
| W. 中非洲标准时间 | （UTC + 01：00）中北部非洲 |
| 约旦标准时间 | （UTC + 02：00）安曼 |
| GTB 标准时间 | （UTC + 02：00）雅典，布加勒斯特 |
| 中东标准时间 | （UTC + 02：00）贝鲁特 |
| 埃及标准时间 | （UTC + 02：00）Cairo |
| E. 欧洲标准时间 | （UTC + 02：00）Chisinau |
| 叙利亚标准时间 | （UTC + 02：00）大马士革 |
| 西部银行标准时间 | （UTC + 02：00）加沙、希伯伦 |
| 南非标准时间 | （UTC + 02：00）哈拉雷、比勒陀利亚 |
| FLE 标准时间 | （UTC + 02：00）赫尔辛基，基辅，里加，索非亚，塔林，维尔纽斯 |
| 以色列标准时间 | （UTC + 02：00）十字 |
| 加里格勒标准时间 | （UTC + 02：00）加 |
| 苏丹标准时间 | （UTC + 02：00）喀土木 |
| 利比亚标准时间 | （UTC + 02：00）的黎波里 |
| 纳米比亚标准时间 | （UTC + 02：00）Windhoek |
| 阿拉伯标准时间 | （UTC + 03：00）巴格达 |
| 土耳其标准时间 | （UTC + 03：00）伊斯坦布尔 |
| 阿拉伯标准时间 | （UTC + 03：00）科威特，利雅得 |
| 白俄罗斯标准时间 | （UTC + 03：00）明斯克 |
| 俄罗斯标准时间 | （UTC + 03：00）莫斯科，圣彼得堡 |
| E. 非洲标准时间 | （UTC + 03：00）内罗毕 |
| 伊朗标准时间 | （UTC + 03：30）德黑兰 |
| 阿拉伯半岛标准时间 | （UTC + 04：00）阿尔阿布扎比，马斯喀特 |
| 阿斯特拉罕标准时间 | （UTC + 04：00）阿斯特拉罕、Ulyanovsk |
| 阿塞拜疆标准时间 | （UTC + 04：00）巴库 |
| 俄罗斯时间区域3 | （UTC + 04：00）Izhevsk、萨马拉 |
| 毛里求斯标准时间 | （UTC + 04：00）端口港 |
| 萨拉托夫标准时间 | （UTC + 04：00）萨拉托夫 |
| 格鲁吉亚标准时间 | （UTC + 04：00）第比利斯 |
| 伏尔加格勒标准时间 | （UTC + 04：00）伏尔加格勒 |
| 高加索标准时间 | （UTC + 04：00）埃里温 |
| 阿富汗标准时间 | （UTC + 04：30）喀布尔 |
| 西亚标准时间 | （UTC + 05：00）Ashgabat，塔什干 |
| 叶卡捷琳堡标准时间 | （UTC + 05：00）卡 |
| 巴基斯坦标准时间 | （UTC + 05：00）伊斯兰堡，卡拉奇 |
| 印度标准时间 | （UTC + 05：30）钦奈，加尔各答，孟买，新德里 |
| 斯里兰卡标准时间 | （UTC + 05：30）斯里兰卡 Jayawardenepura |
| 尼泊尔标准时间 | （UTC + 05：45）加德满都 |
| 中亚标准时间 | （UTC + 06：00）阿斯塔纳 |
| 孟加拉国标准时间 | （UTC + 06：00）达卡 |
| 鄂木斯克标准时间 | （UTC + 06：00）鄂木斯克 |
| 缅甸标准时间 | （UTC + 06：30）仰光（仰光） |
| 东南亚标准时间 | （UTC + 07：00）曼谷，河内，雅加达 |
| 阿尔泰标准时间 | （UTC + 07：00）巴尔瑙尔，戈尔诺-阿尔泰斯克 |
| W. 蒙古标准时间 | （UTC + 07：00）科布多 |
| 北亚标准时间 | （UTC + 07：00）亚 |
| N. 中亚标准时间 | （UTC + 07：00）新西伯利亚 |
| 托木斯克标准时间 | （UTC + 07：00）托木斯克 |
| 中国标准时间 | （UTC + 08：00）北京，重庆，香港特别行政区，乌鲁木齐 |
| 北亚东部标准时间 | （UTC + 08：00）伊尔库茨克 |
| 新加坡标准时间 | （UTC + 08：00）吉隆坡，新加坡 |
| W. 澳大利亚标准时间 | （UTC + 08：00）思 |
| 台北标准时间 | （UTC + 08：00）台北 |
| 乌兰巴托标准时间 | （UTC + 08：00）乌兰巴托 |
| 澳大利亚中西部标准时间 | （UTC + 08：45）尤克拉 |
| 外贝加尔标准时间 | （UTC + 09：00）赤塔 |
| 东京标准时间 | （UTC + 09：00）大阪、札幌、东京 |
| 朝鲜标准时间 | （UTC + 09：00）平壤 |
| 韩国标准时间 | （UTC + 09：00）首尔 |
| 雅库茨克标准时间 | （UTC + 09：00）茨 |
| 中部 澳大利亚标准时间 | （UTC + 09：30）阿德莱德 |
| 澳大利亚中部标准时间 | （UTC + 09：30）达尔文 |
| E. 澳大利亚标准时间 | （UTC + 10：00）布里斯班 |
| 澳大利亚东部标准时间 | （UTC + 10：00）堪培拉，墨尔本，悉尼 |
| 太平洋西部标准时间 | （UTC + 10：00）关岛，港 |
| 塔斯马尼亚岛标准时间 | （UTC + 10：00）霍巴特 |
| 符拉迪沃斯托克(海参崴)标准时间 | （UTC + 10：00）海参崴 |
| 豪勋爵标准时间 | （UTC + 10：30）豪勋爵岛 |
| 布干维尔标准时间 | （UTC + 11：00）布干维尔岛 |
| 俄罗斯时区10 | （UTC + 11：00）Chokurdakh |
| 马加丹标准时间 | （UTC + 11：00）加 |
| 诺福克标准时间 | （UTC + 11：00）诺福克岛 |
| 萨哈林标准时间 | （UTC + 11：00）萨哈林 |
| 太平洋中部标准时间 | （UTC + 11：00）所罗门群岛，新喀里多尼亚 |
| 俄罗斯时区11 | （UTC + 12：00）阿纳德尔，彼得-甫洛夫 |
| 新西兰标准时间 | （UTC + 12：00）奥克兰，惠灵顿 |
| UTC + 12 | （UTC + 12：00）协调世界时 + 12 |
| 斐济标准时间 | （UTC + 12：00）斐济 |
| 堪察加标准时间 | （UTC + 12：00）彼得-甫洛夫-Old |
| 查塔姆群岛标准时间 | （UTC + 12：45）查塔姆群岛 |
| UTC + 13 | （UTC + 13：00）协调世界时 + 13 |
| 汤加标准时间 | （UTC + 13：00）Nuku'alofa |
| 萨摩亚群岛标准时间 | （UTC + 13：00）萨摩亚 |
| 行岛标准时间 | （UTC + 14：00）圣诞岛 |

## <a name="see-also"></a>另请参阅 

- [CURRENT_TIMEZONE （Transact-sql）](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE （Transact-sql）](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys. time_zone_info （Transact-sql）](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

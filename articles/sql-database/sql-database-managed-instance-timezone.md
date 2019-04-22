---
title: Azure SQL 数据库托管实例所在的时区 |Microsoft Docs"
description: 了解 Azure SQL 数据库托管实例所在的时区具体情况
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/10/2019
ms.openlocfilehash: 23314e97051da95ab164baeab6e9d089f486351a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489684"
---
# <a name="time-zone-in-azure-sql-database-managed-instance-preview"></a>Azure SQL 数据库托管实例 （预览版） 中的时区

尽管使用协调世界时 (UTC) 的云解决方案的数据层推荐的做法是，Azure SQL 数据库托管实例提供时区的选择，以满足存储日期和时间值和呼叫日期的现有应用程序的需求和与特定时区的隐式上下文的时间函数。

T-SQL 的功能类似于[getdate （)](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql)或 CLR 代码观察级别的实例上设置的时区。 SQL 代理作业按照计划根据实例的时区。

  >[!NOTE]
  > 托管的实例是支持的时区设置的 Azure SQL 数据库的仅限的部署选项。 其他部署选项应始终遵循 UTC。
使用[AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)中单一数据库与入池 SQL 数据库如果您需要解释在非 UTC 时区的日期和时间信息。

## <a name="supported-time-zones"></a>受支持的时区

受支持的时区的一组继承自托管实例的基础操作系统，它正在定期更新，以获取新的时区定义并反映对现有的更改。

通过公开的受支持的时区名称的列表[sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)系统视图。

## <a name="setting-time-zone"></a>设置时区

可以只创建实例期间设置的托管实例的时区。 默认时区为协调世界时 (UTC)。

  >[!NOTE]
  > 不能更改现有的托管实例的时区。

### <a name="setting-the-time-zone-through-azure-portal"></a>通过 Azure 门户的时区设置

输入参数的新实例，同时从受支持的时间区域列表中选择时区:
  
![在创建实例期间设置时区](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure 资源管理器模板

指定在 timezoneId 属性您[资源管理器模板](https://aka.ms/sql-mi-create-arm-posh)来创建实例期间设置时区。

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

可以在本文末尾找到 timezoneId 属性支持的值的列表。

如果未指定，将会设置时区为 UTC。

## <a name="checking-the-time-zone-of-instance"></a>检查实例的时区

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)函数返回的实例的时区的显示名称。

## <a name="cross-feature-considerations"></a>跨功能注意事项

### <a name="restore-and-import"></a>还原和导入

可以还原的备份文件或数据导入到托管实例从实例或服务器具有不同的时区设置。 但是，请务必谨慎使用此，并分析应用程序行为和结果的查询和报表，就像在一样具有不同的时区设置的两个 SQL Server 实例之间传输数据时。

### <a name="point-in-time-restore"></a>时间点还原

执行时间点还原时，若要还原到的时间被解释为 UTC 时间，以避免由于夏时制和其潜在更改任何多义性。

### <a name="auto-failover-groups"></a>自动故障转移组

在故障转移中的主要和次要实例使用相同的时区组不强制执行，但强烈建议。
  >[!IMPORTANT]
  > 虽然有上具有不同的时区的有效方案异地辅助数据库实例用于仅读取缩放，请注意，在手动或自动故障转移到辅助实例的情况下它将保留其原始时区。

## <a name="limitations"></a>限制

- 不能更改现有的托管实例的时区。
- 从 SQL 代理作业启动外部进程确实发现该实例的时区。
- 托管的实例的本地[新建 AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance) PowerShell cmdlet 不支持的传递时间区域参数尚未。 具有使用 PowerShell 包装[资源管理器模板](https://aka.ms/sql-mi-create-arm-posh)相反。
- CLI 命令[az sql mi 创建](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-create)尚不支持时区参数。

## <a name="list-of-supported-time-zones"></a>受支持的时区列表

| **时区 ID** | **时区显示名称** |
| --- | --- |
| 国际日期变更线标准时间 | (UTC-12:00) 国际日期变更线西 |
| UTC-11 | (UTC-11:00) 协调世界时-11 |
| 阿留申群岛标准时间 | (UTC-10:00) 阿留申群岛 |
| 夏威夷标准时间 | (UTC-10:00) 夏威夷州 |
| 马克萨斯群岛标准时间 | (UTC-09:30) 马克萨斯群岛 |
| 阿拉斯加标准时间 | (UTC-09:00) 阿拉斯加州 |
| UTC-09 | (UTC-09:00) 协调世界时-09 |
| 太平洋标准时间(墨西哥) | (UTC-08:00) 下加利福尼亚 |
| UTC-08 | (UTC-08:00) 协调世界时-08 |
| 太平洋标准时间 | (UTC-08:00) 太平洋时间（美国和加拿大） |
| 美国山地标准时间 | (UTC-07:00) 亚利桑那州 |
| 山地标准时间(墨西哥) | (UTC-07:00) 奇瓦瓦，拉巴斯，马扎特兰 |
| 山地标准时间 | (UTC-07:00) 山地时间（美国和加拿大） |
| 中美洲标准时间 | (UTC-06:00) 中美洲 |
| 中部标准时间 | (UTC-06:00) 中部时间（美国和加拿大） |
| 复活节岛标准时间  | (UTC-06:00) 复活节岛 |
| 中部标准时间(墨西哥) | (UTC-06:00) 瓜达拉哈拉，墨西哥城，蒙特雷 |
| 加拿大中部标准时间 | (UTC-06:00) 萨斯喀彻温省 |
| 南美洲太平洋标准时间 | (UTC-05:00) 波哥大，利马，基多，里奥布朗库 |
| 东部标准时间(墨西哥) | (UTC-05:00) 切图马尔 |
| 东部标准时间 | (UTC-05:00) 东部时间（美国和加拿大） |
| 海地标准时间 | (UTC-05:00) 海地 |
| 古巴标准时间 | (UTC-05:00) 哈瓦那 |
| 美国东部标准时间 | (UTC-05:00) 印地安那州（东部） |
| 特克斯和凯科斯群岛标准时间 | (UTC-05:00)特克斯和凯科斯群岛 |
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
| 阿根廷标准时间 | (UTC-03:00) 布宜诺斯艾利斯市 |
| 格陵兰标准时间 | (UTC-03:00) 格陵兰 |
| 蒙得维的亚标准时间 | (UTC-03:00) 蒙得维的亚 |
| 麦哲伦标准时间 | (UTC-03:00)蓬塔阿雷纳斯 |
| 圣皮埃尔标准时间 | (UTC-03:00) 圣皮埃尔和密克隆群岛 |
| 巴伊亚标准时间 | (UTC-03:00) 萨尔瓦多 |
| UTC-02 | (UTC-02:00) 协调世界时-02 |
| 中大西洋标准时间 | (UTC-02:00) 中大西洋 - 旧称 |
| 亚速尔群岛标准时间 | (UTC-01:00) 亚速尔群岛 |
| 佛得角群岛标准时间 | (UTC-01:00) 佛得角 |
| UTC | (UTC) 协调世界时 |
| GMT 标准时间 | (UTC+00:00) 都柏林，爱丁堡，里斯本，伦敦 |
| 格林威治标准时间 | (UTC+00:00) 蒙罗维亚，雷克雅未克 |
| W. 欧标准时间 | (UTC+01:00) 阿姆斯特丹，柏林，伯尔尼，罗马，斯德哥尔摩，维也纳 |
| 中欧标准时间 | (UTC+01:00) 贝尔格莱德，布拉迪斯拉发，布达佩斯，卢布尔雅那，布拉格 |
| 罗马标准时间 | (UTC+01:00) 布鲁塞尔，哥本哈根，马德里，巴黎 |
| 摩洛哥标准时间 | (UTC+01:00)卡萨布兰卡 |
| 圣多美标准时间 | (UTC+01:00)圣多美 |
| 中欧标准时间 | (UTC+01:00) 萨拉热窝，斯科普里，华沙，萨格勒布 |
| W. 非标准时间 | (UTC+01:00) 中非西部 |
| 约旦标准时间 | (UTC+02:00) 安曼 |
| GTB 标准时间 | (UTC+02:00) 雅典，布加勒斯特 |
| 中东标准时间 | (UTC+02:00) 贝鲁特 |
| 埃及标准时间 | (UTC+02:00) 开罗 |
| E. 欧标准时间 | (UTC+02:00) 基希讷乌 |
| 叙利亚标准时间 | (UTC+02:00) 大马士革 |
| 约旦河西岸标准时间 | (UTC+02:00) 加沙，希伯伦 |
| 南非标准时间 | (UTC+02:00) 哈拉雷，比勒陀利亚 |
| FLE 标准时间 | (UTC+02:00) 赫尔辛基，基辅，里加，索非亚，塔林，维尔纽斯 |
| 以色列标准时间 | (UTC+02:00) 耶路撒冷 |
| 加里宁格勒标准时间 | (UTC+02:00) 加里宁格勒 |
| 苏丹标准时间 | (UTC+02:00)喀土木 |
| 利比亚标准时间 | (UTC+02:00) 的黎波里 |
| 纳米比亚标准时间 | (UTC+02:00)温得和克 |
| 阿拉伯(Arabic)标准时间 | (UTC+03:00) 巴格达 |
| 土耳其标准时间 | (UTC+03:00)伊斯坦布尔 |
| 阿拉伯(Arab)标准时间 | (UTC+03:00) 科威特，利雅得 |
| 白俄罗斯标准时间 | (UTC+03:00) 明斯克 |
| 俄罗斯标准时间 | (UTC+03:00)莫斯科，圣彼得堡 |
| E. 非标准时间 | (UTC+03:00) 内罗毕 |
| 伊朗标准时间 | (UTC+03:30) 德黑兰 |
| 阿拉伯半岛标准时间 | (UTC+04:00) 阿布扎比市，马斯喀特 |
| 阿斯特拉罕标准时间 | (UTC+04:00) 阿斯特拉罕，乌里扬诺夫斯克 |
| 阿塞拜疆标准时间 | (UTC+04:00) 巴库 |
| 俄罗斯的时区 3 | (UTC+04:00) 伊热夫斯克，萨马拉 |
| 毛里求斯标准时间 | (UTC+04:00) 路易港 |
| 萨拉托夫标准时间 | (UTC+04:00)萨拉托夫 |
| 格鲁吉亚标准时间 | (UTC+04:00) 第比利斯 |
| 伏尔加格勒标准时间 | (UTC+04:00)伏尔加格勒 |
| 高加索标准时间 | (UTC+04:00) 埃里温 |
| 阿富汗标准时间 | (UTC+04:30) 喀布尔 |
| 西亚标准时间 | (UTC+05:00) 阿什哈巴德，塔什干 |
| 堡标准时间 | (UTC+05:00) 叶卡捷琳堡 |
| 巴基斯坦标准时间 | (UTC+05:00) 伊斯兰堡，卡拉奇 |
| 印度标准时间 | (UTC+05:30) 钦奈，加尔各答，孟买，新德里 |
| 斯里兰卡标准时间 | (UTC+05:30) 斯里加亚渥登普拉 |
| 尼泊尔标准时间 | (UTC+05:45) 加德满都 |
| 中亚标准时间 | (UTC+06:00) 阿斯塔纳 |
| 孟加拉标准时间 | (UTC+06:00) 达卡 |
| 鄂木斯克标准时间 | (UTC+06:00)鄂木斯克 |
| 缅甸标准时间 | (UTC+06:30) 仰光 |
| 东南亚标准时间 | (UTC+07:00) 曼谷，河内，雅加达 |
| 阿尔泰标准时间 | (UTC+07:00) 巴尔瑙尔，戈尔诺-阿尔泰斯克 |
| W. 蒙古标准时间 | (UTC+07:00) 科布多 |
| 北亚标准时间 | (UTC+07:00) 克拉斯诺亚尔斯克 |
| N。 中亚标准时间 | (UTC+07:00)新西伯利亚 |
| 托木斯克标准时间 | (UTC+07:00) 托木斯克 |
| 中国标准时间 | (UTC+08:00) 北京，重庆，香港特別行政区，乌鲁木齐 |
| 北亚东部标准时间 | (UTC+08:00) 伊尔库茨克 |
| 新加坡标准时间 | (UTC+08:00) 吉隆坡，新加坡 |
| W. 澳大利亚标准时间 | (UTC+08:00) 珀斯 |
| 台北标准时间 | (UTC+08:00) 台北 |
| 乌兰巴托标准时间 | (UTC+08:00) 乌兰巴托 |
| 澳大利亚中西部标准时间 | (UTC+08:45) 尤克拉 |
| 外贝加尔标准时间 | (UTC+09:00) 赤塔市 |
| 东京标准时间 | (UTC+09:00) 大坂，札幌，东京 |
| 朝鲜标准时间 | (UTC + 09:00)平壤 |
| 韩国标准时间 | (UTC+09:00) 首尔 |
| 雅库茨克标准时间 | (UTC+09:00) 雅库茨克 |
| Cen。 澳大利亚标准时间 | (UTC+09:30) 阿德莱德 |
| 澳大利亚中部标准时间 | (UTC+09:30) 达尔文 |
| E. 澳大利亚标准时间 | (UTC+10:00) 布里斯班 |
| 澳大利亚东部标准时间 | (UTC+10:00) 堪培拉，墨尔本，悉尼 |
| 太平洋西部标准时间 | (UTC+10:00) 关岛，莫尔兹比港 |
| 塔斯马尼亚岛标准时间 | (UTC+10:00) 霍巴特 |
| 符拉迪沃斯托克 （海参崴） 标准时间 | (UTC+10:00) 符拉迪沃斯托克 |
| 豪勋爵岛标准时间 | (UTC+10:30) 豪勋爵岛 |
| 布干维尔岛标准时间 | (UTC+11:00) 布干维尔岛 |
| 俄罗斯的时区 10 | (UTC+11:00) 乔库尔达赫 |
| 马加丹标准时间 | (UTC+11:00) 马加丹 |
| 诺福克岛标准时间 | (UTC+11:00) 诺福克岛 |
| 萨哈林标准时间  | (UTC+11:00) 萨哈林 |
| 太平洋中部标准时间 | (UTC+11:00) 所罗门群岛，新喀里多尼亚 |
| 俄罗斯的时区 11 | (UTC+12:00) 阿纳德尔，彼得罗巴甫洛夫斯克-堪察加 |
| 新西兰标准时间 | (UTC+12:00) 奥克兰，惠灵顿 |
| UTC+12 | (UTC+12:00) 协调世界时+12 |
| 斐济标准时间 | (UTC+12:00) 斐济 |
| 堪察加标准时间 | (UTC+12:00) 彼得罗巴甫洛夫斯克-堪察加 - 旧用 |
| 查塔姆群岛标准时间 | (UTC+12:45) 查塔姆群岛 |
| UTC+13 | (UTC+13:00)协调世界时+13 |
| 汤加标准时间 | (UTC+13:00) 努库阿洛法 |
| 萨摩亚群岛标准时间 | (UTC+13:00) 萨摩亚 |
| 莱恩群岛标准时间 | (UTC+14:00) 基里巴斯岛 |

## <a name="see-also"></a>另请参阅

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [AT TIME ZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
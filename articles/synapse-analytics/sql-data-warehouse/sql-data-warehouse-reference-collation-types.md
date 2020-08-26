---
title: 数据仓库排序规则类型
description: Azure Synapse Analytics SQL 池支持的排序规则类型。
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 202bbaf4ea53dd6ba285e79dfa9e6ce782c0903e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80633091"
---
# <a name="database-collation-support-for-azure-synapse-analytics-sql-pool"></a>Azure Synapse Analytics SQL 池的数据库排序规则支持

新建 Azure Synapse SQL 池数据库时，可以从 Azure 门户更改默认数据库排序规则。 利用此功能，可以更轻松地使用 3800 个受支持的数据库排序规则之一创建新数据库。

排序规则为基于字符的数据类型提供区域设置、代码页、排序顺序和字符敏感度规则。 选择以后，需要排序规则信息的所有列和表达式会从数据库设置中继承所选排序规则。 可以重写默认继承，只需为基于字符的数据类型显式声明另一个排序规则即可。

## <a name="changing-collation"></a>更改排序规则

若要更改默认排序规则，请在预配体验中更新“排序规则”字段。

例如，如需将默认排序规则更改为区分大小写，只需将“排序规则”从 SQL_Latin1_General_CP1_CI_AS 重命名为 SQL_Latin1_General_CP1_CS_AS 即可。

## <a name="list-of-unsupported-collation-types"></a>不受支持的排序规则类型的列表

* Japanese_Bushu_Kakusu_140_BIN
* Japanese_Bushu_Kakusu_140_BIN2
* Japanese_Bushu_Kakusu_140_CI_AI_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
* Japanese_Bushu_Kakusu_140_CI_AI
* Japanese_Bushu_Kakusu_140_CI_AI_WS
* Japanese_Bushu_Kakusu_140_CI_AI_KS
* Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CI_AS
* Japanese_Bushu_Kakusu_140_CI_AS_WS
* Japanese_Bushu_Kakusu_140_CI_AS_KS
* Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AI
* Japanese_Bushu_Kakusu_140_CS_AI_WS
* Japanese_Bushu_Kakusu_140_CS_AI_KS
* Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
* Japanese_Bushu_Kakusu_140_CS_AS
* Japanese_Bushu_Kakusu_140_CS_AS_WS
* Japanese_Bushu_Kakusu_140_CS_AS_KS
* Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
* Japanese_XJIS_140_BIN
* Japanese_XJIS_140_BIN2
* Japanese_XJIS_140_CI_AI_VSS
* Japanese_XJIS_140_CI_AI_WS_VSS
* Japanese_XJIS_140_CI_AI_KS_VSS
* Japanese_XJIS_140_CI_AI_KS_WS_VSS
* Japanese_XJIS_140_CI_AS_VSS
* Japanese_XJIS_140_CI_AS_WS_VSS
* Japanese_XJIS_140_CI_AS_KS_VSS
* Japanese_XJIS_140_CI_AS_KS_WS_VSS
* Japanese_XJIS_140_CS_AI_VSS
* Japanese_XJIS_140_CS_AI_WS_VSS
* Japanese_XJIS_140_CS_AI_KS_VSS
* Japanese_XJIS_140_CS_AI_KS_WS_VSS
* Japanese_XJIS_140_CS_AS_VSS
* Japanese_XJIS_140_CS_AS_WS_VSS
* Japanese_XJIS_140_CS_AS_KS_VSS
* Japanese_XJIS_140_CS_AS_KS_WS_VSS
* Japanese_XJIS_140_CI_AI
* Japanese_XJIS_140_CI_AI_WS
* Japanese_XJIS_140_CI_AI_KS
* Japanese_XJIS_140_CI_AI_KS_WS
* Japanese_XJIS_140_CI_AS
* Japanese_XJIS_140_CI_AS_WS
* Japanese_XJIS_140_CI_AS_KS
* Japanese_XJIS_140_CI_AS_KS_WS
* Japanese_XJIS_140_CS_AI
* Japanese_XJIS_140_CS_AI_WS
* Japanese_XJIS_140_CS_AI_KS
* Japanese_XJIS_140_CS_AI_KS_WS
* Japanese_XJIS_140_CS_AS
* Japanese_XJIS_140_CS_AS_WS
* Japanese_XJIS_140_CS_AS_KS
* Japanese_XJIS_140_CS_AS_KS_WS
* SQL_EBCDIC1141_CP1_CS_AS
* SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>查看当前的排序规则

若要查看数据库的当前排序规则，可运行以下 T-SQL 代码片段：

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```

将“Collation”作为属性参数传递时，DatabasePropertyEx 函数将返回指定数据库的当前排序规则。 有关详细信息，请参阅 [DatabasePropertyEx](/sql/t-sql/functions/databasepropertyex-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

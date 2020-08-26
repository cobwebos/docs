---
title: Azure 数据工厂连接器概述
description: 了解数据工厂中支持的连接器。
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 334d5b5113dba17c5abc2b4f2520bde0d16e4c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007431"
---
# <a name="azure-data-factory-connector-overview"></a>Azure 数据工厂连接器概述

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂支持以下数据存储和格式： "复制"、"数据流"、"查找"、"获取元数据" 和 "删除活动"。 单击每个数据存储以了解详细信息中支持的功能和相应的配置。

## <a name="supported-data-stores"></a>支持的数据存储

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>支持的文件格式

Azure 数据工厂支持以下文件格式。 请参阅每一篇介绍基于格式的设置的文章。

- [Avro 格式](format-avro.md)
- [二进制格式](format-binary.md)
- [Common Data Model 格式](format-common-data-model.md)
- [带分隔符的文本格式](format-delimited-text.md)
- [增量格式](format-delta.md)
- [Excel 格式](format-excel.md)
- [JSON 格式](format-json.md)
- [ORC 格式](format-orc.md)
- [Parquet 格式](format-parquet.md)
- [XML 格式](format-xml.md)

## <a name="next-steps"></a>后续步骤

- [复制活动](copy-activity-overview.md)
- [映射数据流](concepts-data-flow-overview.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [删除活动](delete-activity.md)

---
title: include 文件
description: include 文件
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 28ecac39d991754cfadeb87479c336a6c6086fd7
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532462"
---
现在可以在 Azure 门户中使用数据资源管理器工具来创建数据库和集合。 

1. 单击“数据资源管理器” > “新建集合”。 
    
    “添加集合”区域显示在最右侧，可能需要向右滚动才能看到它。

    ![Azure 门户“数据资源管理器”，“添加集合”窗格](./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png)

2. 在“添加集合”页上，输入新集合的设置。

    设置|建议的值|Description
    ---|---|---
    数据库 ID|任务|输入 *Tasks* 作为新数据库的名称。 数据库名称必须包含 1 到 255 个字符，不能包含 `/, \\, #, ?` 或尾随空格。
    集合 ID|Items|输入 *Items* 作为新集合的名称。 集合 ID 与数据库名称的字符要求相同。
    分区键| `<Your partition key>`| 输入分区键，例如 /userid。
    Throughput|400 RU|将吞吐量更改为每秒 400 个请求单位 (RU/s)。 如果想要减少延迟，以后可以增加吞吐量。 
    
    除了前面的设置，还可以选择为集合添加“唯一键”。 在此示例中，请将此字段留空。 开发人员可以使用唯一键向数据库添加一层数据完整性。 创建集合时，通过创建唯一键策略，可确保每个分区键的一个或多个值的唯一性。 若要了解详细信息，请参阅 [Azure Cosmos DB 中的唯一键](../articles/cosmos-db/unique-keys.md)一文。
    
    单击“确定”。

    数据资源管理器将显示新的数据库和集合。

    ![显示新的数据库和集合的 Azure 门户数据资源管理器](./media/cosmos-db-create-collection/azure-cosmos-db-new-collection.png)
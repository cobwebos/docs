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
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854651"
---
现在可以在 Azure 门户中使用数据资源管理器工具来创建数据库和表。 

1. 依次选择“数据资源管理器”   > “新建表”  。 
    
    此时，最右侧将显示“添加表”  区域，可能需要向右滚动才能看到。

    ![Azure 门户中的数据资源管理器](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. 在“添加表”  页上，输入新表的设置。

    设置|建议的值|说明
    ---|---|---
    表 ID|sample-table|新表的 ID。 表名称与数据库 ID 的字符要求相同。 数据库名称的长度必须为 1 到 255 个字符，不能包含 `/ \ # ?` 或尾随空格。
    Throughput|400 RU|将吞吐量更改为每秒 400 个请求单位 (RU/s)。 如果想要减少延迟，以后可以增加吞吐量。

3. 选择“确定”  。

4. 数据资源管理器将显示新的数据库和表。

   ![显示新的数据库和集合的 Azure 门户数据资源管理器](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)
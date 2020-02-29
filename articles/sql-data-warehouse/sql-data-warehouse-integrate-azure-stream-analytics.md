---
title: 使用 Azure 流分析
description: 有关将 Azure 流分析与 Azure Synapse 中的数据仓库配合使用的技巧，以便开发实时解决方案。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 3aa881d5fc7689b20824792ee43ce369546c87e2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197884"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>将 Azure 流分析与 Azure Synapse Analytics 配合使用

Azure 流分析是一种完全托管的服务，可以在云中通过流式数据进行低延迟、高度可用、可缩放且复杂的事件处理。 可以通过阅读[Azure 流分析简介](../stream-analytics/stream-analytics-introduction.md)了解基本知识。 然后，你可以使用[Azure 流分析入门](../stream-analytics/stream-analytics-real-time-fraud-detection.md)教程，了解如何使用流分析创建端到端解决方案。

本文介绍如何使用数据仓库作为 Azure 流分析作业的输出接收器。

## <a name="prerequisites"></a>必备条件

* Azure 流分析作业-若要创建 Azure 流分析作业，请按照[开始使用 Azure 流分析](../stream-analytics/stream-analytics-real-time-fraud-detection.md)教程中的步骤执行以下操作：  

    1. 创建事件中心输入
    2. 配置并启动事件生成器应用程序
    3. 预配流分析作业
    4. 指定作业输入和查询
* Azure Synapse SQL 池数据仓库-若要创建新的数据仓库，请按照快速入门中的步骤[创建新的数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)。

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>指定流式处理输出以指向数据仓库

### <a name="step-1"></a>步骤 1

在 Azure 门户中，请切换到你的流分析作业，并单击 "**作业拓扑**" 菜单下的 "**输出**"。

### <a name="step-2"></a>步骤 2

单击 "**添加**" 按钮，然后从下拉菜单中选择 " **SQL 数据库**"。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>步骤 3

输入以下值：

* *输出别名*：输入此作业输出的友好名称。
* *订阅*：
  * 如果数据仓库与流分析作业属于同一订阅，请单击 "***选择订阅中的 SQL 数据库***"。
  * 如果数据库在不同的订阅中，请单击 "手动提供 SQL 数据库设置"。
* *数据库*：从下拉列表中选择目标数据库。
* *用户名*：指定具有数据库写入访问权限的帐户的用户名。
* *密码*：提供指定的用户帐户的密码。
* *表*：指定数据库中目标表的名称。
* 单击 "**保存**" 按钮

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>步骤 4

在运行测试之前，需要在数据仓库中创建表。  使用 SQL Server Management Studio （SSMS）或所选的查询工具运行以下表创建脚本。

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>步骤 5

在流分析作业的 Azure 门户上，单击作业名称。  单击 "***输出详细信息***" 窗格中的 "***测试***" 按钮。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) 成功连接到数据库后，会在门户中看到一条通知。

### <a name="step-6"></a>步骤 6

单击 "***作业拓扑***" 下的 "***查询***" 菜单并更改查询，将数据插入到所创建的流输出。  单击 "***测试选定的查询***" 按钮以测试查询。  如果查询测试成功，请单击 "***保存查询***" 按钮。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>步骤 7

启动 Azure 流分析作业。  单击 "***概述***" 菜单上的 "***开始***" 按钮。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

单击 "启动作业" 窗格上的 "***开始***" 按钮。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>后续步骤

有关集成的概述，请参阅[集成其他服务](sql-data-warehouse-overview-integrate.md)。
有关更多开发技巧，请参阅[数据仓库的设计决策和编码技术](sql-data-warehouse-overview-develop.md)。

---
title: 使用 Azure 流分析
description: 有关在开发解决方案时将 Azure 流分析与 Azure SQL 数据仓库配合使用的技巧。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721194"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>将 Azure 流分析与 Azure Synapse Analytics 配合使用
Azure 流分析是一种完全托管的服务，可以在云中通过流式数据进行低延迟、高度可用、可缩放且复杂的事件处理。 可以通过阅读[Azure 流分析简介](../stream-analytics/stream-analytics-introduction.md)了解基本知识。 然后，你可以使用[Azure 流分析入门](../stream-analytics/stream-analytics-real-time-fraud-detection.md)教程，了解如何使用流分析创建端到端解决方案。

本文介绍如何使用数据仓库数据库作为流分析作业的输出接收器。

## <a name="prerequisites"></a>必备条件
首先，请在[开始使用 Azure 流分析](../stream-analytics/stream-analytics-real-time-fraud-detection.md)教程中完成以下步骤。  

1. 创建事件中心输入
2. 配置并启动事件生成器应用程序
3. 预配流分析作业
4. 指定作业输入和查询

然后，创建 Azure SQL 数据仓库数据库

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>指定作业输出：Azure SQL 数据仓库数据库
### <a name="step-1"></a>步骤 1
在流分析作业中，单击页面顶部的 "**输出**"，然后单击 "**添加**"。

### <a name="step-2"></a>步骤 2
选择 "SQL 数据库"。

### <a name="step-3"></a>步骤 3
在下一页输入以下值：

* *输出别名*：输入此作业输出的友好名称。
* *订阅*：
  * 如果 SQL 数据仓库数据库与此流分析作业位于同一订阅中，请选择“使用当前订阅中的 SQL 数据库”。
  * 如果你的数据库在不同的订阅中，请选择“使用其他订阅中的 SQL 数据库”。
* *数据库*：指定目标数据库的名称。
* *服务器名称*：为刚刚指定的数据库指定服务器名称。 可以使用 Azure 门户查找此信息。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *用户名*：指定具有数据库写入访问权限的帐户的用户名。
* *密码*：提供指定的用户帐户的密码。
* *表*：指定数据库中目标表的名称。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>步骤 4
单击相应勾选按钮以添加此作业输出，并确保流分析可以成功连接到数据库。

成功连接到数据库后，会在门户中看到一条通知。 您可以单击 "测试" 以测试与数据库的连接。

## <a name="next-steps"></a>后续步骤
有关集成的概述，请参阅[集成其他服务](sql-data-warehouse-overview-integrate.md)。
有关更多开发技巧，请参阅[数据仓库的设计决策和编码技术](sql-data-warehouse-overview-develop.md)。


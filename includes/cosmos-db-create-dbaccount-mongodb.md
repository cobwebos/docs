---
title: 创建 Azure Cosmos DB MongoDB API 帐户
description: 介绍如何在 Azure 门户中创建 Azure Cosmos DB MongoDB API 帐户。
services: cosmos-db
author: mimig1
ms.service: cosmos-db
ms.topic: include
ms.date: 03/20/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 02ea0e011642313b885bc48ec48104fa2789da81
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
1. 在新窗口中，登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左菜单中，依次单击“创建资源”、“数据库”，然后在“Azure Cosmos DB”下单击“创建”。
   
   ![Azure 门户的屏幕截图，突出显示“更多服务”和“Azure Cosmos DB”](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)

3. 在“新建帐户”边栏选项卡中，指定“MongoDB”作为 API，并填充 Azure Cosmos DB 帐户所需的配置。
 
    ![“新 Azure Cosmos DB”边栏选项卡的屏幕截图](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-2.png)

    * “ID”必须是用于标识 Azure Cosmos DB 帐户的唯一名称。 ID 只能包含小写字母、数字和“-”字符，且长度必须为 3 到 50 个字符。
    * “订阅”是你的 Azure 订阅。 系统会自动填充此值。
    * “资源组”是 Azure Cosmos DB 帐户的资源组名称。
    * “位置”是 Azure Cosmos DB 实例所在的地理位置。 请选择最靠近用户的位置。

4. 单击“创建”  以创建帐户。
5. 在工具栏上，单击“通知”可监视部署过程。

    ![“部署已启动”通知](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-nosql-notification.png)

6.  部署完成后，请从“所有资源”磁贴打开新帐户。 

    ![“所有资源”磁贴中的 Azure Cosmos DB 帐户](./media/cosmos-db-create-dbaccount-mongodb/azure-documentdb-all-resources.png)

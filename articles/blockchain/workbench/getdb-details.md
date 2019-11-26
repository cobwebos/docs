---
title: 获取 Azure Blockchain Workbench 数据库详细信息
description: 了解如何获取 Azure 区块链工作台预览版数据库和数据库服务器信息。
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 2b3190a9d042be8ead1ff3d5ef48d4a2a19e8963
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324688"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>获取有关 Azure Blockchain Workbench 数据库的信息

本文介绍如何获取有关 Azure 区块链工作台预览数据库的详细信息。

## <a name="overview"></a>概述

在 Blockchain Workbench SQL DB 中，有关应用程序、工作流和智能合同执行的信息是使用数据库视图提供的。 当使用诸如 Microsoft Excel、Power BI、Visual Studio 和 SQL Server Management Studio 之类的工具时，开发人员可以使用此信息。

开发人员需要执行以下操作，然后才能连接到数据库：

* 在数据库防火墙中允许外部客户端访问。 本文中有关配置数据库防火墙的部分介绍了如何允许访问。
* 获取数据库服务器名称和数据库名称。

## <a name="connect-to-the-blockchain-workbench-database"></a>连接到 Blockchain Workbench 数据库

若要连接到数据库，请执行以下操作：

1. 使用对 Azure Blockchain Workbench 资源具有所有者权限的帐户登录到 Azure 门户。
2. 在左侧导航窗格中，选择“资源组”。
3. 为 Blockchain Workbench 部署选择资源组名称。
4. 选择“类型”来对资源列表进行排序，然后选择你的 **SQL Server**。 下面的屏幕截图中的已排序列表显示了两个 SQL 数据库，一个是“master”，另一个是使用“lhgn”作为**资源前缀**的数据库。

   ![已排序的 Blockchain Workbench 资源列表](./media/getdb-details/sorted-workbench-resource-list.png)

5. 若要查看有关 Blockchain Workbench 数据库的详细信息，请选择具有你在部署 Blockchain Workbench 时提供的**资源前缀**的数据库对应的链接。

   ![数据库详细信息](./media/getdb-details/workbench-db-details.png)

可以使用开发或报告工具通过使用数据库服务器名称和数据库名称连接到 Blockchain Workbench 数据库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 中的数据库视图](database-views.md)
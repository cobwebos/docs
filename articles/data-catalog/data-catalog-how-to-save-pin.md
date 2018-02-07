---
title: "在 Azure 数据目录中保存搜索并固定数据资产 | Microsoft Docs"
description: "操作指南文章重点说明 Azure 数据目录中保存数据源和数据资产供以后使用的功能。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: cced7b8253a45d990371d8f95fe36b580c2debbf
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>在 Azure 数据目录中保存搜索并固定数据资产
## <a name="introduction"></a>介绍
Azure 数据目录提供数据源发现功能。 可快速搜索并筛选目录，找到数据源并了解其用途，更轻松地找到适合手头作业的数据。

但如果需要经常使用相同数据呢？ 如果你和其他用户经常将知识应用到目录中的相同数据源呢？ 在这些情况下，反复发出相同的搜索可能会降低效率。 此时便是已保存搜索和固定数据资产大显身手的时候。

## <a name="saved-searches"></a>保存的搜索
数据目录中保存的搜索是可重用、针对每个用户的搜索定义。 可定义搜索（包括搜索词、标记和其他筛选器），然后保存搜索。 之后可再次运行已保存的搜索定义，返回与搜索条件匹配的任何数据资产。

### <a name="create-a-saved-search"></a>创建保存的搜索
若要创建保存的搜索，请执行以下操作：
1. 在 Azure 数据目录门户的“当前搜索”窗口中，单击“保存”。 

    ![“当前搜索”设置“保存”链接](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. 输入想要重复使用的搜索条件，然后单击“保存”。

    ![“当前搜索”设置保存的搜索名称](./media/data-catalog-how-to-save-pin/02-name.png)

3. 出现提示时，为保存的搜索输入名称。 选择有意义并对搜索将返回的数据资产具有描述性的名称。

### <a name="manage-saved-searches"></a>管理保存的搜索
保存一个或多个搜索后，“保存的搜索”选项会显示在“当前搜索”框下。 展开列表时，将显示所有已保存的搜索。

 ![已保存的搜索列表](./media/data-catalog-how-to-save-pin/03-list.png)

执行以下任一操作：

* 若要执行搜索，请在列表中选择已保存的搜索。

* 若要查看已保存搜索的管理选项列表，请单击搜索名称旁的向下箭头。

    ![用于管理已保存搜索的选项](./media/data-catalog-how-to-save-pin/04-managing.png)

* 若要为已保存的搜索输入新名称，请选择“重命名”。 搜索定义不会更改。

* 若要从列表中删除已保存的搜索，请选择“删除”，然后确认删除。

* 若要将已保存的搜索标记为默认搜索，请选择“保存为默认搜索”。 如果从 Azure 数据目录主页执行“空”搜索，将执行默认搜索。 此外，标记为默认搜索的搜索会在“保存的搜索”列表顶部显示。

### <a name="organizational-saved-searches"></a>保存的组织搜索
组织中的所有用户都可以保存搜索供自己使用。 数据目录管理员还可以保存搜索供组织内所有用户使用。 当管理员保存搜索时，会看到“在公司内共享”选项。 选择此选项会与组织中的所有用户共享该保存的搜索。

 ![保存的组织搜索](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>固定的数据资产
使用已保存的搜索，可保存并重用搜索定义。 搜索返回的数据资产可能会随目录内容的更改而发生变化。 固定数据资产时，可显式标识特定数据资产，使其更易于访问而无需使用搜索。

固定数据资产非常简单。 若要将数据资产添加到已固定列表中，只需单击“固定”图标即可。 此图标显示在磁贴视图中资产磁贴的角落，位于 Azure 数据目录门户中列表视图的最左列。

![数据资产固定图标](./media/data-catalog-how-to-save-pin/05-pinning.png)

取消固定数据资产同样很简单。 只需单击“取消固定”图标，为所选资产切换设置即可。

![数据资产取消固定图标](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>“我的资产”部分
数据目录门户主页包括“我的资产”部分，显示与当前用户相关的资产。 本部分包括固定的资产和保存的搜索。

![主页上“我的资产”部分](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>摘要
Azure 数据目录提供更易于发现所需数据源的功能，让你和其他组织成员可花更少的时间查找数据，有更多的时间来处理数据。 保存的搜索和固定的数据资产建立在这些核心功能的基础上，让用户可以轻松标识将重复使用的数据源。

---
title: "如何保存搜索并固定数据资产 | Microsoft Docs"
description: "操作指南文章重点说明 Azure 数据目录中保存数据源和数据资产供以后重复使用的功能。"
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
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f017776480466979d7f2f9edec2b3ac5caca2321


---
# <a name="how-to-save-searches-and-pin-data-assets"></a>如何保存搜索并固定数据资产
## <a name="introduction"></a>介绍
Microsoft Azure 数据目录提供数据源发现功能。 用户可以快速搜索并筛选目录，找到数据源并了解其用途，更轻松地找到适合手头作业的数据。

但如果用户需要经常使用相同数据呢？ 如果用户经常将他们的知识应用到目录中的相同数据源呢？ 在这些情况下，反复发出相同的搜索可能效率会很低 – 这就是保存搜索和固定数据资产发挥作用的地方。

## <a name="saved-searches"></a>保存的搜索
Azure 数据目录中保存的搜索是可重用、针对每个用户的搜索定义。 用户定义搜索（包括搜索词、标记和其他筛选器）后，可以保存搜索以待后用。 保存的搜索定义可供以后重复使用，用于返回与搜索条件匹配的任何数据资产。

### <a name="creating-a-saved-search"></a>创建保存的搜索
若要创建保存的搜索，先输入要重复使用的搜索条件。 然后单击 Azure 数据目录门户中“当前搜索”框中的“保存”链接。

 ![选择“保存”，保存当前的搜索设置](./media/data-catalog-how-to-save-pin/01-save-option.png)

出现提示时，为保存的搜索输入名称。 选择有意义并对搜索将返回的数据资产具有描述性的名称。

 ![为保存的搜索提供名称](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>管理保存的搜索
用户保存一个或多个搜索后，Azure 数据目录门户中的“当前搜索”框下将显示“保存的搜索”选项。 展开后，将显示已保存搜索的完整列表。

 ![已保存的搜索列表](./media/data-catalog-how-to-save-pin/03-list.png)

从列表中选择保存的搜索即可执行相应搜索。

选择下拉列表菜单将提供一组管理选项：

 ![用于管理已保存搜索的选项](./media/data-catalog-how-to-save-pin/04-managing.png)

选择“重命名”将提示用户为保存的搜索输入新名称。 搜索定义不会更改。

选择“删除”将提示用户进行确认，然后从用户列表中删除保存的搜索。

选择“另存为默认”会将所选的已保存搜索标记为用户的默认搜索。 如果用户从 Azure 数据目录主页执行“空”的搜索，将执行用户的默认搜索。 此外，标记为默认值的搜索将出现在已保存的搜索列表顶部。

### <a name="organizational-saved-searches"></a>保存的组织搜索
每个用户可以保存搜索供自己使用。 数据目录管理员还可以保存搜索供组织内所有用户使用。 保存搜索时，会为管理员提供一个选项，以在公司内共享保存的搜索。 如果选中此选项，保存的搜索将包含在可用搜索列表中，供所有用户使用。

 ![保存的组织搜索](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>固定的数据资产
保存的搜索允许用户保存并重用搜索定义；搜索返回的数据资产可能会随目录内容的更改而发生变化。 固定数据资产让用户可以显式标识特定的数据资产，使其更易于访问而无需使用搜索。

固定数据资产非常简单 - 用户只需单击数据资产的“大头针”图标，将其添加到已固定列表中。 此图标显示在磁贴视图中资产磁贴的角落，位于 Azure 数据目录门户中列表视图的最左列中。

![固定数据资产](./media/data-catalog-how-to-save-pin/05-pinning.png)

取消固定资产同样很简单 - 用户只需再次单击“大头针”图标，为所选资产切换设置。

![取消固定数据资产](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="my-assets"></a>“我的资产”
Azure 数据目录门户主页包括“我的资产”部分，显示当前用户感兴趣的资产。 本部分包括固定的资产和保存的搜索。

![主页上的“我的资产”](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>摘要
Azure 数据目录提供让用户能够更轻松地发现所需数据源的功能，让用户花更少的时间查找数据，有更多的时间来处理数据。 保存的搜索和固定的数据资产建立在这些核心功能的基础上，让用户可以轻松标识将重复使用的数据源。



<!--HONumber=Nov16_HO3-->



---
title: "数据配置文件数据源操作指南"
description: "操作指南文章重点说明在“Azure 数据目录”中注册数据源时如何包括表和列级数据配置文件，以及如何使用数据配置文件来了解数据源。"
services: data-catalog
documentationcenter: 
author: spelluru
manager: NA
editor: 
tags: 
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/01/2017
ms.author: spelluru
ms.openlocfilehash: 848d244a2a6c3420d1a11005e1b02f5671d7912a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2017
---
# <a name="data-profile-data-sources"></a>数据配置文件数据源
## <a name="introduction"></a>介绍
**Microsoft Azure 数据目录**是一个完全托管的云服务，充当企业数据源的注册系统和发现系统。 换而言之，**Azure 数据目录**旨在帮助人们发现、了解和使用数据源，并帮助组织从其现有数据中获取更多价值。 数据源向 **Azure 数据目录**注册时，该服务将复制其元数据并为其建立索引，但并不仅仅只是这样。

**Azure 数据目录**的“数据分析”功能检查目录中受支持数据源的数据，并收集有关该数据的统计信息和信息。 想要包括数据资产的配置文件很简单。 注册数据资产时，在数据源注册工具中选择“包括数据配置文件”。

## <a name="what-is-data-profiling"></a>什么是数据分析
数据分析检查正在注册的数据源中的数据，并收集有关该数据的统计信息和信息。 在发现数据源的过程中，这些统计信息有助于确定数据是否适用于解决其业务问题。

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

以下数据源支持数据分析：

* SQL Server（包括 Azure SQL DB 和 Azure SQL 数据仓库）表和视图
* Oracle 表和视图
* Teradata 表和视图
* Hive 表

注册数据资产时包括数据配置文件可帮助用户回答有关数据源的问题，包括：

* 它是否可以解决我的业务问题？
* 数据是否符合特定的标准或模式？
* 数据源有些什么异常？
* 将此数据集成到我的应用程序可能会遇到什么难题？

> [!NOTE]
> 此外还可以向资产添加文档，描述如何将数据集成到应用程序。 请参阅[如何记录数据源](data-catalog-how-to-documentation.md)。
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>如何在注册数据源时包括数据配置文件
想要包括数据源的配置文件很简单。 注册数据资产时，在数据源注册工具的“要注册的对象”面板中选择“包括数据配置文件”。

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

若要了解有关如何注册数据源的详细信息，请参阅[如何注册数据源](data-catalog-how-to-register.md)和 [Azure 数据目录入门](data-catalog-get-started.md)。

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>对包含数据配置文件的数据资产进行筛选
若要发现包括数据配置文件的数据资产，可以包括 `has:tableDataProfiles` 或 `has:columnsDataProfiles` 作为搜索条件之一。

> [!NOTE]
> 在数据源注册工具中选择“包括数据配置文件”将包括表和列级配置文件信息。 但是，数据目录 API 允许数据资产在只包含一组配置文件信息的情况下进行注册。
>
>

## <a name="viewing-data-profile-information"></a>查看数据配置文件信息
找到含有配置文件的合适数据源后，就可以查看数据配置文件详细信息。 要查看数据配置文件，请在数据目录门户窗口选择数据资产，并选择“数据配置文件”。

![](media/data-catalog-data-profile/data-catalog-view.png)

“Azure 数据目录”中的数据配置文件显示表和列配置文件信息，包括：

### <a name="object-data-profile"></a>对象数据配置文件
* 行数
* 表大小
* 上次更新对象的时间

### <a name="column-data-profile"></a>列数据配置文件
* 列数据类型
* 非重复值数目
* 值为 NULL 的行数
* 列值的最小值、最大值、平均值和标准偏差

## <a name="summary"></a>摘要
数据分析提供有关已注册数据资产的统计信息和信息，帮助确定数据是否适用于解决业务问题。 连同批注和记录数据源一起，数据配置文件可以让用户深入了解数据。

## <a name="see-also"></a>另请参阅
* [How to register data sources](data-catalog-how-to-register.md)
* [Azure 数据目录入门](data-catalog-get-started.md)

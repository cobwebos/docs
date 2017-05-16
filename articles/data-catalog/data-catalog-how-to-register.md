---
title: "如何注册数据源 | Microsoft Docs"
description: "操作指南文章重点说明如何使用 Azure 数据目录注册数据源，包括在注册过程中提取的元数据字段。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 25c2b279487f099a0e688621e63faaa4ed265e6e
ms.contentlocale: zh-cn
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-register-data-sources"></a>如何注册数据源
## <a name="introduction"></a>介绍
**Microsoft Azure 数据目录**是一个完全托管的云服务，充当企业数据源的注册系统和发现系统。 换而言之，**Azure 数据目录**旨在帮助人们发现、了解和使用数据源，并帮助组织从其现有数据中获取更多价值。 通过 **Azure 数据目录**使数据源可被发现的第一步是注册该数据源。

## <a name="registering-data-sources"></a>注册数据源
注册是从数据源提取元数据并将该数据复制到 **Azure 数据目录**服务的过程。 数据将保留在其当前位置，仍受当前系统的管理员和策略的控制。

若要注册数据源，只需从“Azure 数据目录”门户启动“Azure 数据目录”数据源注册工具即可。 使用工作或学校帐户（与登录到门户使用的 Azure Active Directory 凭据一样）登录，然后选择想要注册的数据源。
请参阅 [Azure 数据目录入门](data-catalog-get-started.md)教程，了解更多分步详细说明。

注册数据源后，目录会跟踪其位置并为其元数据编制索引，使用户可以搜索、浏览并发现数据源，然后借助其位置通过应用程序或所选工具连接该数据源。

## <a name="sources-supported"></a>支持的源
请参阅[数据目录 DSR](data-catalog-dsr.md)，获取当前支持的数据源列表。
<br/>

## <a name="structural-metadata"></a>结构化元数据
注册数据源时，注册工具将提取所选对象的结构相关信息 - 称为结构化元数据。

对于所有对象，此结构化元数据将包含对象的位置，以便发现数据的用户可以利用该信息在所选客户端工具中连接到该对象。 其他结构化元数据包括对象名称和类型以及属性/列名称和数据类型。

## <a name="descriptive-metadata"></a>描述性元数据
除了从数据源提取的核心结构化元数据外，数据源注册工具还会提取描述性元数据。 对于 SQL Server Analysis Services 和 SQL Server Reporting Services，这种数据来自这些服务公开的描述属性。 对于 SQL Server，将提取使用 ms_description 扩展属性提供的值。 对于 Oracle 数据库，数据源注册工具将从 ALL_TAB_COMMENTS 视图提取 COMMENTS 列。

除了从数据源提取的描述性元数据外，用户还可以使用数据源注册工具输入描述性元数据。 用户可以添加标记，还可以确定要注册的对象的专家。 所有这些描述性元数据会与结构化元数据一起复制到 **Azure 数据目录**服务。

## <a name="including-previews"></a>包括预览
默认情况下，只从数据源提取元数据并将它们复制到 **Azure 数据目录**服务，但通过查看数据源包含的数据示例，可以更容易了解数据源。

“Azure 数据目录”数据源注册工具允许用户在每个已注册的表和视图中包含数据的快照预览。 如果用户选择在注册过程中包括预览，注册工具最多将包括每个表和视图的 20 条记录。 然后，此快照随结构化元数据和描述性元数据一起被复制到目录中。

> [!NOTE]
> 具有大量列的宽表在其预览中可能包含少于 20 条的记录。
>
>

## <a name="including-data-profiles"></a>包括数据配置文件
正如包括预览能为用户在“Azure 数据目录”中搜索数据源提供有价值的上下文一样，包括数据配置文件也可以让理解所发现的数据源变得更简单。

“Azure 数据目录”数据源注册工具允许用户为每个已注册的表和视图包含数据配置文件。 如果用户选择在注册期间包括数据配置文件，注册工具将包括每个表和视图中数据相关的聚合统计信息，包括：

* 对象中的行数和数据大小
* 数据和对象架构的最近更新日期
* Null 记录数和列的非重复值数
* 列的最小值、最大值、平均值和标准偏差

然后，这些统计信息随结构化元数据和描述性元数据一起被复制到目录中。

> [!NOTE]
> 文本和日期列不会在其数据配置文件中包含平均值和标准偏差统计信息。
>
>

## <a name="updating-registrations"></a>更新注册
注册数据源，使它可以通过在注册期间提取的元数据和可选预览在“Azure 数据目录”发现。 如果数据源需要在目录中更新（例如，对象的架构已更改，或者需要包括先前未包括的表，再或者用户想要更新预览中包括的数据），可以重新运行数据源注册工具。

重新注册已注册的数据源将执行合并“upsert”操作：更新现有对象，同时创建新对象。 将保留用户通过“Azure 数据目录”门户提供的任何元数据。

## <a name="summary"></a>摘要
使用“Azure 数据目录”注册数据源，将结构性元数据和描述性元数据从数据源复制到目录服务，使发现和理解数据源更简单。 注册数据源后，可以使用“Azure 数据目录”门户对其进行批注、管理和检测。

## <a name="see-also"></a>另请参阅
* [Azure 数据目录入门](data-catalog-get-started.md)教程提供有关如何注册数据源的分步详细说明。


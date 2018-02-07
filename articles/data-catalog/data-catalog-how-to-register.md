---
title: "在 Azure 数据目录中注册数据源 | Microsoft Docs"
description: "本文重点说明如何在 Azure 数据目录中注册数据源，包括在注册过程中提取的元数据字段。"
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
ms.date: 01/18/2018
ms.author: maroche
ms.openlocfilehash: 48b13eef0960afb4aab68923fb97b5b9c14a3d9f
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="register-data-sources-in-azure-data-catalog"></a>在 Azure 数据目录中注册数据源
## <a name="introduction"></a>介绍
Azure 数据目录是一个完全托管的云服务，充当企业数据源的注册和发现系统。 换而言之，数据目录可帮助人们发现、了解和使用数据源，并帮助组织从其现有数据中获取更多价值。 通过数据目录使数据源可被发现的第一步是注册该数据源。

## <a name="register-data-sources"></a>注册数据源
注册是从数据源提取元数据并将该数据复制到数据目录服务的过程。 数据将保留在其当前位置，仍受当前系统的管理员和策略的控制。

若要注册数据源，请执行以下操作：
1. 在 Azure 数据目录门户中，启动数据目录数据源注册工具。 
2. 使用工作或学校帐户（与登录到门户的 Azure Active Directory 凭据相同）登录。
3. 选择要注册的数据源。

有关分步详细说明，请参阅 [Azure 数据目录入门](data-catalog-get-started.md)教程。

注册数据源后，目录将跟踪其位置并为其元数据编制索引。 用户可以搜索、浏览和发现数据源，然后借助其位置通过应用程序或所选工具连接该数据源。

## <a name="supported-data-sources"></a>支持的数据源
有关当前支持的数据源列表，请参阅[数据目录 DSR](data-catalog-dsr.md)。

## <a name="structural-metadata"></a>结构化元数据
注册数据源时，注册工具将提取选定对象的结构相关信息。 此信息被称为结构化元数据。

对于所有对象，此结构化元数据包括对象的位置，以便发现数据的用户可以利用该信息在所选客户端工具中连接到该对象。 其他结构化元数据包括对象名称和类型以及属性/列名称和数据类型。

## <a name="descriptive-metadata"></a>描述性元数据
除了从数据源提取的核心结构化元数据外，数据源注册工具还会提取描述性元数据。 对于 SQL Server Analysis Services 和 SQL Server Reporting Services，这种元数据来自这些服务公开的描述属性。 对于 SQL Server，将提取使用 ms\_description 扩展属性提供的值。 对于 Oracle 数据库，数据源注册工具将从 ALL\_TAB\_COMMENTS 视图提取 COMMENTS 列。

除了从数据源提取的描述性元数据外，用户还可以使用数据源注册工具输入描述性元数据。 用户可以添加标记，还可以确定要注册的对象的专家。 所有这些描述性元数据会与结构化元数据一起复制到数据目录服务。

## <a name="include-previews"></a>包括预览
默认情况下，只从数据源提取元数据并将它们复制到数据目录服务，但若能查看数据源包含的数据示例，即可更容易了解数据源。

通过使用数据目录数据源注册工具，可为每个已注册的表和视图包括数据快照预览。 如果选择在注册期间包括预览，注册工具最多包括每个表和视图的 20 条记录。 然后，此快照随结构化元数据和描述性元数据一起被复制到目录。

> [!NOTE]
> 具有大量列的宽表在其预览中包括的记录可能少于 20 条。
>
>

## <a name="include-data-profiles"></a>包括数据配置文件
正如包括预览能为在数据目录中搜索数据源的用户提供有价值的上下文一样，包括数据配置文件可以让理解所发现的数据源变得更简单。

通过使用数据目录数据源注册工具，可为每个已注册的表和视图包括数据配置文件。 如果选择在注册期间包括数据配置文件，注册工具将包括每个表和视图中数据相关的聚合统计信息，包括：

* 对象中的行数和数据大小。
* 数据和对象架构的最近更新日期。
* Null 记录数和列的非重复值数。
* 列的最小值、最大值、平均值和标准偏差值。

然后，这些统计信息随结构化元数据和描述性元数据一起被复制到目录。

> [!NOTE]
> 文本和日期列不会在其数据配置文件中包括平均值和标准偏差统计信息。
>
>

## <a name="update-registrations"></a>更新注册
注册数据源，使它可以通过在注册期间提取的元数据和可选预览在数据目录中被发现。 如果数据源需要在目录中更新（例如，对象的架构已更改，或者需要包括先前未包括的表，再或者想要更新预览中包括的数据），可以重新运行数据源注册工具。

重新注册已注册的数据源将执行合并“upsert”操作：更新现有对象，并且创建新对象。 保留用户通过数据目录门户提供的所有元数据。

## <a name="summary"></a>摘要
在数据目录中注册数据源会将结构化元数据和描述性元数据从数据源复制到目录服务，因此发现和理解数据源更简单。 注册数据源后，可通过使用数据目录门户批注、管理和发现该数据源。

## <a name="next-steps"></a>后续步骤
有关注册数据源的详细信息，请参阅 [Azure 数据目录入门](data-catalog-get-started.md)教程。

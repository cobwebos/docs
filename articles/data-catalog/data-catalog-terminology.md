---
title: "Azure 数据目录术语 | Microsoft Docs"
description: "本文介绍 Azure 数据目录文档中使用的概念和术语。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6fec74d9-4a3c-4b4b-88ba-cad5ad143331
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 557b529f45c7fbc286b7e1893d4b4688e088ed91
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-terminology"></a>Azure 数据目录术语
## <a name="catalog"></a>目录
Azure 数据目录是基于云的元数据存储库，可以在其中注册数据源和数据资产。 目录充当提取自数据源中的结构化元数据和用户添加的描述性元数据的中央存储位置。

## <a name="data-source"></a>数据源
数据源是管理数据资产的系统或容器。 例如 SQL Server 数据库、Oracle 数据库、SQL Server Analysis Services 数据库（表格或多维）以及 SQL Server Reporting Services 服务器。

## <a name="data-asset"></a>数据资产
数据资产是包含在数据源中、可使用目录注册的对象。 例如 SQL Server 表和视图、Oracle 表和视图、SQL Server Analysis Services 度量值、维度和 KPI 以及 SQL Server Reporting Services 报表。

## <a name="data-asset-location"></a>数据资产位置
目录存储数据源或数据资产的位置，可借助该位置通过客户端应用程序连接到源。 位置的格式和详细信息取决于数据源类型。 例如，SQL Server 表可以由四部分名称（服务器名称、数据库名称、架构名称、对象名称）标识，而 Server Reporting Services 报表可通过其 URL 标识。

## <a name="structural-metadata"></a>结构化元数据
结构化元数据是从数据源中提取的元数据，用于描述数据资产的结构。 包括资产位置、资产对象名称和类型，以及其他特定于类型的特性。 例如，表和视图的结构化元数据包含对象列的名称和数据类型。

## <a name="descriptive-metadata"></a>描述性元数据
描述性元数据是描述数据资产目的或意图的元数据。 通常描述性元数据由目录用户使用 Azure 数据目录门户进行添加，但也可在注册过程中从数据源提取。 例如，Azure 数据目录注册工具将从 SQL Server Analysis Services 和 SQL Server Reporting Services 的“说明”属性以及 SQL Server 数据库中的 [ms_description 扩展属性](https://technet.microsoft.com/library/ms190243.aspx) 中提取描述（如果这些属性已填充了值的话）。

## <a name="request-access"></a>请求访问权限
数据资产的描述性元数据可以包括如何请求访问数据资产或数据源的相关信息。 此信息随数据资产位置一起显示，并可以包括以下一个或多个选项：

* 负责授予数据源访问权限的用户或团队的电子邮件地址。
* 用户必须访问以获取数据源访问权限的文档化过程 URL。
* 可用于获取数据源访问权限的标识和访问管理工具（如 Microsoft 标识管理器）URL。
* 描述用户如何获得数据源访问权限的自由文本条目。

## <a name="preview"></a>预览
Azure 数据目录中的预览是一个最多含 20 条记录的快照，这些记录可以在注册期间从数据源提取，并与数据资产元数据存储在目录中。 预览可帮助发现数据资产的用户更好地了解其功能和用途。 换而言之，看到示例数据要比只是看到列名和数据类型更有价值。
只有表和视图支持预览，并且必须由用户在注册过程中显式选择。

## <a name="data-profile"></a>数据配置文件
Azure 数据目录中的数据配置文件是已注册数据资产相关的表级和列级元数据快照，这些数据可在注册期间从数据源提取，并与数据资产元数据存储在目录中。 数据配置文件可帮助发现数据资产的用户更好地了解其功能和用途。 与预览类似，数据配置文件必须由用户在注册过程中显式选择。

> [!NOTE]
> 对于大型表和视图，提取数据配置文件可能是一项高成本的操作，并且可能会大大增加注册数据源所需的时间。
>
>

## <a name="user-perspective"></a>用户视角
在 Azure 数据目录中，任何用户都可以为已注册的数据资产提供描述性元数据。 每个用户对数据及其使用具有不同的看法。 例如，负责服务器的管理员可以提供该服务器的服务级别协议 (SLA) 或备份窗口的详细信息；数据专员可以提供数据支持的业务过程的文档链接；而分析员可以对其他分析师相关度最高的条款提供说明，这对需要发现和了解数据的用户非常有价值。

每种观点都有继承价值，并且通过 Azure 数据目录，每个用户都可以对这些观点提供有意义的信息，同时所有用户都可以都使用这些信息来了解数据及其用途。

## <a name="expert"></a>专家
专家是已被认定为对某数据资产具有广博的“专家”见解的用户。 任何用户可以将自身或另一用户添加为某资产的专家。 在 Azure 数据目录中，被列为专家并不代表具有任何其他特权；它允许用户在查看资产的描述性元数据时，可轻松找到最可能有用的那些观点。

## <a name="owner"></a>所有者
所有者是在 Azure 数据目录中对数据资产管理具有额外权限的用户。 用户可取得已注册的数据资产的所有权，而所有者可以将其他用户添加为共同所有者。 有关详细信息，请参阅[如何管理数据资产](data-catalog-how-to-manage.md)  

> [!NOTE]
> 所有权和管理仅在标准版的 Azure 数据目录中可用。
>
>

## <a name="registration"></a>注册
注册是从数据资产提取数据资产元数据并将其复制到 Azure 数据目录服务的操作。 然后就可以批注和发现已注册的数据资产。

## <a name="see-also"></a>另请参阅
* [什么是 Azure 数据目录？](data-catalog-what-is-data-catalog.md) - 本文概述了 Azure 数据目录服务、它所提供的价值以及所支持的方案。
* [ Azure 数据目录入门](data-catalog-get-started.md) - 本文提供了介绍如何使用 Azure 数据目录进行数据源发现的端到端教程。  

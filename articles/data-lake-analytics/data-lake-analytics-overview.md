---
title: "Microsoft Azure Data Lake Analytics 概述 | Microsoft Docs"
description: "Data Lake Analytics 是一种 Azure 大数据服务，允许用户使用各种数据，借助基于云中数据收集的见解推动企业的发展，而不用考虑数据的大小和位置。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 1e1d443a-48a2-47fb-bc00-bf88274222de
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/23/2017
ms.author: saveenr
ms.openlocfilehash: 8817b511d779029421491194b50120d51ec9dbad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Microsoft Azure Data Lake Analytics 概述
## <a name="what-is-azure-data-lake-analytics"></a>什么是 Azure Data Lake Analytics？
Azure Data Lake Analytics 是一项按需分析作业服务，用于简化大数据分析。 可以专注于编写、运行和管理作业，而不是运行分布式基础结构。 无需部署、配置和调整硬件，只需编写查询即可转换数据并提取有价值的见解。 通过将表盘设置为所需值，该分析服务就可以立即处理任何规模的作业。 只需为运行作业付费，让服务变得更为经济高效。 该分析服务支持 Azure Active Directory，让用户可管理访问和角色，并与用户的本地识别系统集成。 它还包括了 U-SQL 语言，有效结合了 SQL 的优点和用户代码的表达力。 U-SQL 的可缩放分布式运行时可让用户高效地分析存储中的数据，以及跨 Azure 中的 SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库的数据。

## <a name="key-capabilities"></a>关键功能
* **动态缩放**
  
    Data Lake Analytics 是针对云缩放和性能需求进行构建的。  它能动态地预配资源并让你以千吉字节甚至百亿亿字节为单位进行分析。 当作业完成时，它自动释放资源，仅需为所用的处理功能付费。 增加或减少存储数据的大小或使用的计算资源量时，无需重写代码。 用户可仅关注自己的业务逻辑，而非如何处理和存储大数据集。
* **使用熟悉的工具更快开发、更智能调试和优化**
  
    数据湖分析具有与 Visual Studio 中，深度集成，因此你可以使用熟悉的工具来运行、 调试和优化你的代码。 U-SQL 作业可视化允许看见代码如何大规模运行，因此可以轻松找到性能瓶颈并优化成本。
* **U-SQL：简单熟悉、功能强大且易于扩展**
  
    Data Lake Analytics 包含 U-SQL，这是一种查询语言，扩展了 SQL 的简单熟悉的声明性本质和 C# 的表现力。 U-SQL 语言基于在 Microsoft 内部支持大数据系统的同一分布式运行时。 现在，数以百万计的 SQL 和 .NET 开发人员可以凭借自身已有的技能处理和分析自己的数据。
* 
            **与 IT 投资无缝集成**
  
    Data Lake Analytics 可以使用现有的 IT 投资进行识别、管理、安全和数据仓库工作来应对这个挑战。 此方法简化了数据管理，使当前的数据应用程序更容易扩展。 Data Lake Analytics 与适用于用户管理和权限的 Active Directory 集成且随附内置监视与审核功能。
* **价格合理且经济高效**
  
    Data Lake Analytics 是用于运行大数据工作负荷的经济高效的解决方案。 处理数据时按每个作业付费。 无需硬件、许可证或服务特定的支持协议。 作业开始和完成时，系统自动缩放大小，所以你永远无需为你所需之外的东西付费。
* **可用于所有 Azure 数据**
  
    Data Lake Analytics 已针对搭配使用 Azure Data Lake 而优化，可为大数据工作负荷提供最高级别的性能、吞吐量和并行化。  Data Lake Analytics 还可与 Azure Blob 存储和 Azure SQL 数据库搭配使用。

## <a name="next-steps"></a>后续步骤
 
  * 通过 [Azure 门户](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI](data-lake-analytics-get-started-cli2.md) 使用 Data Lake Analytics 入门
  * 使用 [Azure 门户](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) | [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) | [Node.js](data-lake-analytics-manage-use-nodejs.md) 管理 Azure Data Lake Analytics
  * [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md) 

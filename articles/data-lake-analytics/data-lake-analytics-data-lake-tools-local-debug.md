---
title: 在本地调试 Azure Data Lake Analytics 代码 | Microsoft Docs
description: 了解如何在本地工作站上使用针对 Visual Studio 的 Azure Data Lake 工具来调试 U-SQL 作业。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 181512c12c1e72e6aa8205aabd5ea22816d4c5df
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890656"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>在本地调试 Azure Data Lake Analytics 代码

与在 Azure Data Lake 服务中一样，可以使用针对 Visual Studio 的 Azure Data Lake 工具在工作站上运行和调试 Azure Data Lake Analytics 代码。

了解[如何在本地计算机上运行 U-SQL 脚本](data-lake-analytics-data-lake-tools-local-run.md)。

## <a name="debug-scripts-and-c-assemblies-locally"></a>在本地调试脚本和 C# 程序集

无需将 C# 程序集提交并注册到 Azure Data Lake Analytics 服务即可对其进行调试。 可以在两个代码隐藏文件和引用的 C# 项目中设置断点。

### <a name="to-debug-local-code-in-code-behind-file"></a>在代码隐藏文件中调试本地代码

1. 在代码隐藏文件中设置断点。
2. 按 F5 在本地调试脚本。

> [!NOTE]
   > 以下过程仅适用于 Visual Studio 2015。 在旧版 Visual Studio 中，可能需要手动添加 pdb 文件。  
   >
   >

### <a name="to-debug-local-code-in-a-referenced-c-project"></a>在引用的 C# 项目中调试本地代码

1. 创建 C# 程序集项目，生成该项目以生成输出 dll。
2. 使用 U-SQL 语句注册该 dll：

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. 在 C# 代码中设置断点。
4. 按 F5，在本地调试引用 C# dll 的脚本。


## <a name="next-steps"></a>后续步骤

- 若要查看更复杂的查询，请参阅 [Analyze website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)（使用 Azure Data Lake Analytics 分析网站日志）。
- 若要查看作业详细信息，请参阅 [Use Job Browser and Job View for Azure Data Lake Analytics jobs](data-lake-analytics-data-lake-tools-view-jobs.md)（对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图）。
- 若要使用顶点执行视图，请参阅 [Use the Vertex Execution View in Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)（使用用于 Visual Studio 的 Data Lake 工具中的顶点执行视图）。

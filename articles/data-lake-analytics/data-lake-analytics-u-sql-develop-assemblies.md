---
title: "为 Azure Data Lake Analytics 作业开发 U-SQL 程序集 | Microsoft Docs"
description: "了解如何开发可在 Data Lake Analytics 作业中使用和重复使用的程序集。 "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>为 Azure Data Lake Analytics 作业开发 U-SQL 程序集
了解如何将代码隐藏转换为程序集，从而在 Data Lake Analytics 作业中使用和重复使用。 

U-SQL 可使用户轻松使用 .Net 语言添加自己的自定义代码，例如 C#、VB.Net 或 F#。 甚至可以部署自己的运行时以支持其他语言。

使用自定义代码的最简方法是使用用于 Visual Studio 代码隐藏功能的 Data Lake 工具。 有关详细信息，请参阅[教程：使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。 使用代码隐藏有以下几个缺点：

- 每次提交脚本时都会上传源代码。
- 代码隐藏无法与其他作业共享。

要解决这些问题，可将代码隐藏转换为程序集，并将程序集注册到 Data Lake Analytics 目录。

## <a name="prerequisites"></a>先决条件
* Visual Studio 2017、Visual Studio 2015、Visual Studio 2013 Update 4 或安装有 Visual C++ 的 Visual Studio 2012
* 用于 .NET 的 Microsoft Azure SDK 2.5 或更高版本。  可以使用 Web 平台安装程序或 Visual Studio 安装程序来安装它
* Data Lake Analytics 帐户。  请参阅[通过 Azure 门户实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。
* 请通读 [Get started with Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md)（Azure Data Lake Analytics U-SQL Studio 入门）教程。
* 连接到 Azure。
* 若要上传源数据，请参阅 [Get started with Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md)（Azure Data Lake Analytics U-SQL Studio 入门）。 

## <a name="develop-assemblies-for-u-sql"></a>开发适用于 U-SQL 的程序集

**创建和提交 U-SQL 作业**

1. 在“文件”菜单中，单击“新建”，并单击“项目”。
2. 展开“已安装”、“模板”、“Azure Data Lake”、“U-SQL(ADLA)”，选择“类库(适用于 U-SQL 应用程序)”模板，并单击“确定”。
3. 在 Class1.cs 中编写代码。  下面就是一个代码示例。

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. 单击“生成”菜单，并单击“生成解决方案”，创建 dll。

## <a name="register-assemblies"></a>注册程序集

请参阅[使用 Data Lake Analytics(U-SQL) 目录](data-lake-analytics-use-u-sql-catalog.md)。


## <a name="use-the-assemblies"></a>使用程序集

请参阅[使用用于 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)。

## <a name="see-also"></a>另请参阅
* [通过 PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
* [使用适用于 Visual Studio 的工具开发 U-SQL 应用程序](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Data Lake Analytics(U-SQL) 目录](data-lake-analytics-use-u-sql-catalog.md)
* [使用用于 Visual Studio Code 的 Azure Data Lake 工具](data-lake-analytics-data-lake-tools-for-vscode.md)
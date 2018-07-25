---
title: 在本地计算机上运行 Azure Data Lake U-SQL 脚本 | Microsoft Docs
description: 了解如何使用针对 Visual Studio 的 Azure Data Lake 工具在本地计算机上运行 U-SQL 作业。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888960"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>在本地计算机上运行 U-SQL 脚本

在开发 U-SQL 脚本时，通常会在本地运行 U-SQL 脚本，因为这可以节省成本和时间。 针对 Visual Studio 的 Azure Data Lake 工具支持在本地计算机上运行 U-SQL 脚本。 

## <a name="basic-concepts-for-local-run"></a>本地运行的基本概念

下面的图表显示用于本地运行的组件以及这些组件是如何映射到云运行的。

|组件|本地运行|云运行|
|---------|---------|---------|
|存储|本地数据根文件夹|默认 Azure Data Lake Store 帐户|
|计算|U-SQL 本地运行引擎|Azure Data Lake Analytics 服务|
|执行环境|本地计算机上的工作目录|Azure Data Lake Analytics 群集|

本地运行组件的详细说明：

### <a name="local-data-root-folder"></a>本地数据根文件夹

本地数据根文件夹是本地计算帐户的“本地存储”。 本地计算机上的本地文件系统中的任何文件夹都可以是本地数据根文件夹。 它等同于 Data Lake Analytics 帐户的默认 Azure Data Lake Store 帐户。 切换到不同数据根文件夹与切换到不同默认存储帐户的操作相同。 

数据根文件夹的用途：
- 存储元数据，例如数据库、表、表值函数和程序集。
- 查找在 U-SQL 脚本中定义为相对路径的输入和输出路径。 使用相对路径可更轻松地将 U-SQL 脚本部署到 Azure。

### <a name="u-sql-local-run-engine"></a>U-SQL 本地运行引擎

U-SQL 本地运行引擎是 U-SQL 作业的“本地计算帐户”。 用户可以通过针对 Visual Studio 的 Azure Data Lake 工具在本地运行 U-SQL 作业。 也支持通过 Azure Data Lake U-SQL SDK 命令行和编程接口在本地执行。 [详细了解 Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)。

### <a name="working-directory"></a>工作目录

在运行 U-SQL 脚本时，需要使用工作目录文件夹来缓存编译结果、执行日志等。 在针对 Visual Studio 的 Azure Data Lake 工具中，工作目录是 U-SQL 项目的工作目录（通常位于 `<U-SQL project root path>/bin/debug>` 下）。 工作目录将在每次触发新运行时清理。

## <a name="local-run-in-visual-studio"></a>在 Visual Studio 中本地运行

针对 Visual Studio 的 Azure Data Lake 工具具有内置本地运行引擎，并将其展现为本地计算帐户。 若要在本地运行 U-SQL 脚本，请在脚本的编辑器边距下拉列表中选择（本地计算机）或（本地项目）帐户，然后单击“提交”。

![针对 Visual Studio 的 Data Lake 工具向本地帐户提交脚本](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>使用（本地计算机）帐户在本地运行

（本地计算机）帐户是一个共享的本地计算帐户，它含有单个本地数据根文件夹，作为本地存储帐户。 数据根文件夹在默认情况下位于“C:\Users\<username>\AppData\Local\USQLDataRoot”，也可以通过“工具”>“Data Lake”>“选项和设置”对其进行配置。

![针对 Visual Studio 的 Data Lake 工具配置本地数据根](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
U-SQL 项目需要在本地运行。 U-SQL 项目的工作目录用于 U-SQL 本地执行工作目录。 在本地运行期间生成编译结果、执行日志和其他与作业执行相关的文件，并存储在工作目录文件夹下。 请注意，在每次重新运行脚本时，工作目录中的所有这些文件都将被清理和重新生成。

## <a name="local-run-with-local-project-account"></a>使用（本地项目）帐户在本地运行

（本地项目）帐户是针对每个项目独立于项目的本地计算帐户，具有独立的本地数据根文件夹。 在解决方案资源管理器中打开的每个活动 U-SQL 项目都具有相应的 `(Local-project: <project name>)` 帐户，该帐户在服务器资源管理器和 U-SQL 脚本编辑器边距中列出。 

（本地项目）帐户为开发人员提供整洁和独立的开发环境。 与具有为所有本地作业存储元数据和输入/输出数据的共享本地数据根文件夹的（本地计算机）帐户不同，（本地项目）帐户将在 U-SQL 脚本每次运行时在 U-SQL 项目工作目录下创建一个临时的本地数据根文件夹。 此临时数据根文件夹将在重新生成或重新运行时被清理。 

U-SQL 项目提供了通过项目引用和属性来管理这一独立的本地运行环境的良好体验。 可以同时配置项目中的 U-SQL 脚本的输入数据源和引用的数据库环境。

### <a name="manage-input-data-source-for-local-project-account"></a>管理（本地项目）帐户的输入数据源

U-SQL 项目负责（本地项目）帐户的本地数据根文件夹创建和数据设置。 临时数据根文件夹将在每次重新生成和在本地运行时在 U-SQL 项目工作目录下被清理和重新创建。 由 U-SQL 项目配置的所有数据源都将在本地作业执行前被复制到此临时本地数据根文件夹。 

可以通过右键单击“U-SQL 项目”>“属性”>“测试数据源”来配置数据源的根文件夹。 在（本地项目）帐户上运行 U-SQL 脚本时，“测试数据源”中的所有文件和子文件夹（包括子文件夹下的文件）都被复制到临时本地数据根文件夹。 在本地作业执行完成后，还可以在项目工作目录中的临时本地数据根文件夹下找到输出结果。 请注意，所有这些输出都将在项目重新生成和清理时被删除和清理。 

![针对 Visual Studio 的 Data Lake 工具配置项目测试数据源](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>管理（本地项目）帐户的引用的数据库环境 

如果 U-SQL 使用 U-SQL 数据库对象或使用它来进行查询，则在本地运行此 U-SQL 脚本前，必须在本地准备好数据库环境。 对于（本地项目）帐户，U-SQL 数据库依赖关系可通过 U-SQL 项目引用进行管理。 可将 U-SQL 数据库项目引用添加到 U-SQL 项目。 在（本地项目）帐户上运行 U-SQL 脚本前，所有引用的数据库都将部署到临时本地数据根文件夹。 在每次运行时，临时数据根文件夹都将清理为全新的隔离环境。

相关文章：
* [了解如何通过 U-SQL 数据库项目来管理 U-SQL 数据库定义](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [了解如何在 U-SQL 项目中管理 U-SQL 数据库引用](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>（本地计算机）和（本地项目）帐户之间的差异

（本地计算机）帐户的目标是在用户的本地计算机上模拟 Azure Data Lake Analytics 帐户。 它与 Azure Data Lake Analytics 帐户共享同一体验。 （本地项目）的目标是提供用户友好的本地开发环境，以帮助用户在本地运行脚本前部署数据库引用和输入数据。 （本地计算机）帐户提供共享的永久环境，可通过所有项目来访问。 （本地项目）帐户为每个项目提供隔离的开发环境，并在每次运行时刷新。 基于以上分析，（本地项目）帐户通过快速应用新更改来提供更快的开发体验。

可以在以下所示的图表中找到（本地计算机）和（本地项目）帐户之间的更多区别：

|差异角度|（本地计算机）|（本地项目）|
|----------------|---------------|---------------|
|本地访问|所有项目都可访问|只有相应的项目才可访问此帐户|
|本地数据根文件夹|永久本地文件夹。 通过“工具”>“Data Lake”>“选项和设置”进行配置|为在 U-SQL 项目工作目录下的每个本地运行创建的临时文件夹。 文件夹将在重新生成或重新运行时被清理|
|U-SQL 脚本的输入数据|永久本地数据根文件夹下的相对路径|通过“U-SQL 项目属性”>“测试数据源”进行设置。 所有文件、子文件夹在本地执行前都会复制到临时数据根文件夹|
|U-SQL 脚本的输出数据|永久本地数据根文件夹下的相对路径|输出到临时数据根文件夹。 结果将在重新生成或重新运行时被清理。|
|引用的数据库部署|在针对（本地计算机）帐户运行时，将不会自动部署引用的数据库。 在提交到 Azure Data Lake Analytics 帐户时也是如此。|引用的数据库在本地执行前自动部署到（本地项目）帐户。 所有数据库环境都在重新生成或重新运行时清理和重新部署。|

## <a name="local-run-with-u-sql-sdk"></a>使用 U-SQL SDK 在本地运行

除在 Visual Studio 中本地运行 U-SQL 脚本外，还可使用 Azure Data Lake U-SQL SDK 通过命令行及编程接口本地运行 U-SQL 脚本。 通过这些接口，可以自动执行 U-SQL 本地运行和测试。

[详细了解 Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)。

## <a name="next-steps"></a>后续步骤

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [如何为 Azure Data Lake Analytics 设置 CI/CD 管道](data-lake-analytics-cicd-overview.md)
- [使用 U-SQL 数据库项目开发 U-SQL 数据库](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [如何测试 Azure Data Lake Analytics 代码](data-lake-analytics-cicd-test.md)

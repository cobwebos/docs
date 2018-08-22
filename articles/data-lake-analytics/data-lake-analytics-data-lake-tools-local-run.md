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
ms.openlocfilehash: 892147f3668ee811e3c43044478c650d2f37587a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630309"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>在本地计算机上运行 U-SQL 脚本

开发 U-SQL 脚本时，我们通常会在本地运行 U-SQL 脚本，因为这可以节省时间和开支。 针对 Visual Studio 的 Azure Data Lake 工具支持在本地计算机上运行 U-SQL 脚本。 

## <a name="basic-concepts-for-local-runs"></a>本地运行的基本概念

以下图表显示用于本地运行的组件以及这些组件如何映射到云运行。

|组件|本地运行|云运行|
|---------|---------|---------|
|存储|本地数据根文件夹|默认 Azure Data Lake Store 帐户|
|计算|U-SQL 本地运行引擎|Azure Data Lake Analytics 服务|
|运行环境|本地计算机上的工作目录|Azure Data Lake Analytics 群集|

以下部分提供有关本地运行组件的详细信息。

### <a name="local-data-root-folders"></a>本地数据根文件夹

本地数据根文件夹是本地计算帐户的**本地存储**。 本地计算机上的本地文件系统中的任何文件夹都可以是本地数据根文件夹。 它等同于 Data Lake Analytics 帐户的默认 Azure Data Lake Store 帐户。 切换到不同数据根文件夹与切换到不同默认存储帐户的操作相同。 

数据根文件夹的用途如下：
- 存储元数据。 示例包括数据库、表、表值函数和程序集。
- 查找在 U-SQL 脚本中定义为相对路径的输入和输出路径。 使用相对路径可更轻松地将 U-SQL 脚本部署到 Azure。

### <a name="u-sql-local-run-engines"></a>U-SQL 本地运行引擎

U-SQL 本地运行引擎是 U-SQL 作业的**本地计算帐户**。 用户可以通过针对 Visual Studio 的 Azure Data Lake 工具在本地运行 U-SQL 作业。 也支持通过 Azure Data Lake U-SQL SDK 命令行和编程接口在本地运行。 详细了解 [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)。

### <a name="working-directories"></a>工作目录

运行 U-SQL 脚本时，需要使用工作目录文件夹来缓存编译结果、运行日志和执行其他函数。 在针对 Visual Studio 的 Azure Data Lake 工具中，工作目录是 U-SQL 项目的工作目录。 该目录位于 `<U-SQL project root path>/bin/debug>` 下。 工作目录将在每次触发新运行时清理。

## <a name="local-runs-in-microsoft-visual-studio"></a>Microsoft Visual Studio 中的本地运行

针对 Visual Studio 的 Azure Data Lake 工具具有内置的本地运行引擎。 这些工具将引擎展现为本地计算帐户。 若要在本地运行 U-SQL 脚本，请在脚本的编辑器边缘下拉菜单中选择“本地计算机”或“本地项目”帐户。 然后选择“提交”。

![将 U-SQL 脚本提交到本地帐户](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>使用本地计算机帐户的本地运行

**本地计算机**帐户是一个共享的本地计算帐户，它含有单个本地数据根文件夹，作为本地存储帐户。 数据根文件夹默认位于 **C:\Users\<username>\AppData\Local\USQLDataRoot**。 也可以通过“工具” > “Data Lake” > “选项和设置”对其进行配置。

![配置本地数据根文件夹](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
本地运行需要一个 U-SQL 项目。 U-SQL 项目的工作目录用于 U-SQL 本地运行工作目录。 在本地运行期间生成编译结果、运行日志和其他与作业运行相关的文件，并存储在工作目录文件夹下。 每次重新运行脚本时，工作目录中的所有这些文件都将被清理和重新生成。

## <a name="local-runs-with-a-local-project-account"></a>使用本地项目帐户的本地运行

**本地项目**帐户是针对每个项目独立于项目的本地计算帐户，具有独立的本地数据根文件夹。 在解决方案资源管理器中打开的每个活动 U-SQL 项目都具有相应的 `(Local-project: <project name>)` 帐户。 该帐户在服务器资源管理器和 U-SQL 脚本编辑器的边缘列出。  

**本地项目**帐户提供整洁和独立的开发环境。 **本地计算机**帐户具有一个共享的本地数据根文件夹，该文件夹为所有本地作业存储元数据和输入/输出数据。 但是，**本地项目**帐户在 U-SQL 脚本每次运行时，在 U-SQL 项目工作目录下创建一个临时的本地数据根文件夹。 此临时数据根文件夹将在重新生成或重新运行时被清理。 

U-SQL 项目通过项目引用和属性来管理独立的本地运行环境。 可以在项目和引用的数据库环境中配置 U-SQL 脚本的输入数据源。

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>管理本地项目帐户的输入数据源 

U-SQL 项目为**本地项目**帐户创建本地数据根文件夹和设置数据。 每次重新生成和在本地运行时，将在 U-SQL 项目工作目录下清理并重新创建临时数据根文件夹。 由 U-SQL 项目配置的所有数据源将在本地作业运行之前复制到此临时本地数据根文件夹。 

可以配置数据源的根文件夹。 右键单击“U-SQL 项目” > “属性” > “测试数据源”。 在**本地项目**帐户中运行 U-SQL 脚本时，“测试数据源”文件夹中的所有文件和子文件夹将复制到临时本地数据根文件夹。 包括子文件夹下的文件。 运行本地作业后，还可以在项目工作目录中的临时本地数据根文件夹下找到输出结果。 重新生成和清理项目时，将删除并清理所有这些输出。 

![配置项目的测试数据源](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>管理**本地项目**帐户的引用数据库环境 

如果 U-SQL 使用 U-SQL 数据库对象或使用它来进行查询，则在本地运行此 U-SQL 脚本前，必须在本地准备好数据库环境。 对于**本地项目**帐户，U-SQL 数据库依赖关系可通过 U-SQL 项目引用进行管理。 可将 U-SQL 数据库项目引用添加到 U-SQL 项目。 在**本地项目**帐户中运行 U-SQL 脚本之前，所有引用的数据库将部署到临时本地数据根文件夹。 每次运行时，临时数据根文件夹将清理为全新的隔离环境。

请参阅以下相关文章：
* 了解如何管理 [U-SQL 数据库项目](data-lake-analytics-data-lake-tools-develop-usql-database.md)中的 U-SQL 数据库定义和引用。

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>**本地计算机**和**本地项目**帐户之间的差异

**本地计算机**帐户在用户的本地计算机上模拟 Azure Data Lake Analytics 帐户。 它与 Azure Data Lake Analytics 帐户具有相同的体验。 **本地项目**帐户提供用户友好的本地开发环境。 此环境可帮助用户在本地运行脚本之前部署数据库引用和输入数据。 **本地计算机**帐户提供共享的永久环境，可通过所有项目来访问。 **本地项目**帐户为每个项目提供独立开发环境。 每次运行时都会刷新该环境。 **本地项目**帐户通过快速应用新更改来提供更快的开发体验。

下表描述了**本地计算机**和**本地项目**帐户之间的其他差别：

|差异角度|本地计算机|本地项目|
|----------------|---------------|---------------|
|本地访问|可由所有项目访问。|只有相应的项目能够访问此帐户。|
|本地数据根文件夹|永久本地文件夹。 通过“工具” > “Data Lake” > “选项和设置”进行配置。|为在 U-SQL 项目工作目录下的每个本地运行创建的临时文件夹。 重新生成或重新运行时会清理该文件夹。|
|U-SQL 脚本的输入数据|永久本地数据根文件夹下的相对路径。|通过“U-SQL 项目属性” > “测试数据源”进行设置。 在本地运行之前，会将所有文件和子文件夹复制到临时数据根文件夹。|
|U-SQL 脚本的输出数据|永久本地数据根文件夹下的相对路径。|输出到临时数据根文件夹。 在重新生成或重新运行时清理结果。|
|引用的数据库部署|针对**本地计算机**帐户运行时，不会自动部署引用的数据库。 在提交到 Azure Data Lake Analytics 帐户时也是如此。|引用的数据库在本地运行之前自动部署到**本地项目**帐户。 重新生成或重新运行时，会清理和重新部署所有数据库环境。|

## <a name="a-local-run-with-the-u-sql-sdk"></a>使用 U-SQL SDK 的本地运行

可以在 Visual Studio 中本地运行 U-SQL 脚本，此外，还可以使用 Azure Data Lake U-SQL SDK 通过命令行及编程接口在本地运行 U-SQL 脚本。 通过这些接口，可以自动执行 U-SQL 本地运行和测试。

详细了解 [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)。

## <a name="next-steps"></a>后续步骤

- [如何为 Azure Data Lake Analytics 设置 CI/CD 管道](data-lake-analytics-cicd-overview.md)。
- [如何测试 Azure Data Lake Analytics 代码](data-lake-analytics-cicd-test.md)。

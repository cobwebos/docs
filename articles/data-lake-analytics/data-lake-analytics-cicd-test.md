---
title: 如何测试你的 Azure Data Lake Analytics 代码
description: 了解如何为 Azure Data Lake Analytics 的 U-SQL 和扩展的 C# 代码添加测试用例。
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913964"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>测试 Azure Data Lake Analytics 代码

Azure 数据湖提供[U-SQL](data-lake-analytics-u-sql-get-started.md)语言。 U-SQL 将声明性 SQL 与命令性 C# 相结合，可在任何规模上处理数据。 在本文中，您将了解如何为 U-SQL 和扩展的 C# 用户定义运算符 （UDO） 代码创建测试用例。

## <a name="test-u-sql-scripts"></a>测试 U-SQL 脚本

U-SQL 脚本已针对在 Azure 或本地计算机上运行的可执行代码进行编译和优化。 编译和优化过程将整个 U-SQL 脚本视为一个整体。 不能对每个语句执行传统的单元测试。 但是，通过使用 U-SQL 测试 SDK 和本地运行 SDK，可以执行脚本级测试。

### <a name="create-test-cases-for-u-sql-script"></a>为 U-SQL 脚本创建测试用例

针对 Visual Studio 的 Azure Data Lake 工具可用于创建 U-SQL 脚本测试用例。

1. 在解决方案资源管理器中，右键单击 U-SQL 脚本，然后选择“创建单元测试”****。

1. 创建一个新的测试项目或向现有测试项目插入测试用例。

   ![针对 Visual Studio 的 Data Lake 工具 - 创建 U-SQL 测试项目配置](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>管理测试数据源

在测试 U-SQL 脚本时，需要使用测试输入文件。 要管理测试数据，请在**解决方案资源管理器**中，右键单击 U-SQL 项目，然后选择**属性**。 您可以在**测试数据源**中输入源。

![针对 Visual Studio 的 Data Lake 工具 - 配置项目测试数据源](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

在 U-SQL 测试 SDK 中调用`Initialize()`接口时，将创建一个临时本地数据根文件夹，该文件夹位于测试项目的工作目录下。 在运行 U-SQL 脚本测试用例之前，测试数据源文件夹中的所有文件和文件夹都将复制到临时本地数据根文件夹。 通过使用分号拆分测试数据文件夹路径，可以添加更多测试数据源文件夹。

### <a name="manage-the-database-environment-for-testing"></a>管理用于测试的数据库环境

如果 U-SQL 脚本使用 U-SQL 数据库对象或查询 U-SQL 数据库对象，则需要在运行 U-SQL 测试用例之前初始化数据库环境。 调用存储过程时，此方法可能是必需的。 U-SQL 测试 SDK 中的 `Initialize()` 接口可帮助将 U-SQL 项目引用的所有数据库部署到测试项目工作目录中的临时本地数据根文件夹。

有关如何管理 U-SQL 项目的 U-SQL 数据库项目引用的详细信息，请参阅引用[U-SQL 数据库项目](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)。

### <a name="verify-test-results"></a>验证测试结果

`Run()` 接口返回作业执行结果。 *0*表示成功 *，1*表示失败。 也可以使用 C# 断言函数来验证输出。

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中运行测试用例

U-SQL 脚本测试项目是基于 C# 单元测试框架生成的。 生成项目后，选择 **"测试** > **Windows** > **测试资源管理器**"。 可以从**测试资源管理器**运行测试用例。 或者，右键单击单元测试中的 .cs 文件，然后选择 **"运行测试**"。

## <a name="test-c-udos"></a>测试 C# UDO

### <a name="create-test-cases-for-c-udos"></a>为 C# UDO 创建测试用例

您可以使用 C# 单元测试框架来测试 C# 用户定义的运算符 （UDO）。 在测试 UDO 时，需要准备相应的 IRowset 对象作为输入****。

创建**IRowset**对象的方法有两种：

- 从文件加载数据以创建**IRowset**：

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- 使用数据收集中的数据创建**IRowset**：

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>验证测试结果

调用 UDO 函数后，可以使用 C# 断言函数，通过架构和 Rowset 值验证来验证结果。 您可以将**U-SQL C++ UDO 单元测试项目**添加到解决方案中。 为此，请在可视化工作室**中选择"文件>">项目**。

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中运行测试用例

生成项目后，选择 **"测试** > **Windows** > **测试资源管理器**"。 可以从**测试资源管理器**运行测试用例。 或者，右键单击单元测试中的 .cs 文件，然后选择 **"运行测试**"。

## <a name="run-test-cases-in-azure-pipelines"></a>在 Azure 管道中运行测试用例<a name="run-test-cases-in-azure-devops"></a>

U-SQL 脚本测试项目和 C# UDO 测试项目都继承 C# 单元测试项目********。 Azure 管道中的[可视化工作室测试任务](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts)可以运行这些测试用例。

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>在 Azure 管道中运行 U-SQL 测试用例

对于 U-SQL 测试，请确保`CPPSDK`加载到生成计算机上，然后将`CPPSDK`路径传递给`USqlScriptTestRunner(cppSdkFolderFullPath: @"")`。

#### <a name="what-is-cppsdk"></a>什么是 CPPSDK？

CPPSDK 是包含 Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 的包。 此包包括 U-SQL 运行时所需的环境。 可以在针对 Visual Studio 的 Azure Data Lake 工具安装文件夹下获取此包：

- 对于 Visual Studio 2015，它位于 `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK` 下
- 对于 Visual Studio 2017，它位于 `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK` 下
- 对于视觉工作室 2019， 它下`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>在 Azure 管道生成代理中准备 CPPSDK

在 Azure 管道中准备 CPPSDK 依赖项的最常见方法是：

1. 压缩包含 CPPSDK 库的文件夹。

1. 将 .zip 文件签入到源代码管理系统。 .zip 文件可确保签入 CPPSDK 文件夹下的所有库，以便文件不会因为`.gitignore`文件而被忽略。

1. 解压缩生成管道中的 .zip 文件。

1. 指向`USqlScriptTestRunner`生成计算机上的此解压缩文件夹。

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>在 Azure 管道中运行 C# UDO 测试用例

对于 C# UDO 测试，请确保引用 UDO 所需的以下程序集。

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

如果通过 [Nuget 包 Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) 引用这些程序集，请确保在生成管道中添加 NuGet 还原任务。

## <a name="next-steps"></a>后续步骤

- [如何为 Azure Data Lake Analytics 设置 CI/CD 管道](data-lake-analytics-cicd-overview.md)
- [在本地计算机上运行 U-SQL 脚本](data-lake-analytics-data-lake-tools-local-run.md)
- [使用 U-SQL 数据库项目开发 U-SQL 数据库](data-lake-analytics-data-lake-tools-develop-usql-database.md)

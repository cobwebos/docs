---
title: 如何测试你的 Azure Data Lake Analytics 代码 | Microsoft Docs
description: 了解如何为 Azure Data Lake Analytics 的 U-SQL 和扩展的 C# 代码添加测试用例。
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
ms.openlocfilehash: fa5c113541452a93c25adc7c14bdaa6994434c71
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365854"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>测试 Azure Data Lake Analytics 代码

Azure Data Lake 提供 U-SQL 语言，此语言将声明性 SQL 与命令性 C# 相结合，以便处理任何规模的数据。 此文档介绍如何为 U-SQL 和扩展的 C# UDO（用户定义的运算符）代码创建测试用例。

## <a name="test-u-sql-scripts"></a>测试 U-SQL 脚本

U-SQL 脚本针对可执行代码进行编译和优化，以便在云或本地计算机上跨计算机运行。 编译和优化过程将整个 U-SQL 脚本视为一个整体。 不能为每个语句执行传统的“单元测试”。 但是，通过使用 U-SQL 测试 SDK 和本地运行 SDK，可以执行脚本级测试。

### <a name="create-test-cases-for-u-sql-script"></a>为 U-SQL 脚本创建测试用例

针对 Visual Studio 的 Azure Data Lake 工具可用于创建 U-SQL 脚本测试用例。

1.  在解决方案资源管理器中，右键单击 U-SQL 脚本，然后选择“创建单元测试”。
2.  创建一个新的测试项目或向现有测试项目插入测试用例。

    ![针对 Visual Studio 的 Data Lake 工具 - 创建 U-SQL 测试项目](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![针对 Visual Studio 的 Data Lake 工具 - 创建 U-SQL 测试项目配置](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>管理测试数据源

在测试 U-SQL 脚本时，需要使用测试输入文件。 可以通过配置 U-SQL 项目属性中的“测试数据源”来管理这些测试数据。 

在调用 U-SQL 测试 SDK 中的 `Initialize()` 接口时，会在测试项目的工作目录下创建一个临时的本地数据根文件夹，在运行 U-SQL 脚本测试用例前，测试数据源文件夹中的所有文件和子文件夹（以及子文件夹下的文件）都将被复制到这个临时本地数据根文件夹。 通过使用分号拆分测试数据文件夹路径，可以添加更多测试数据源文件夹。

![针对 Visual Studio 的 Data Lake 工具 - 配置项目测试数据源](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>管理用于测试的数据库环境

如果 U-SQL 脚本使用 U-SQL 数据库对象或使用它来进行查询（例如，调用存储过程），则需在运行 U-SQL 测试用例前对数据库环境进行初始化。 U-SQL 测试 SDK 中的 `Initialize()` 接口可帮助将 U-SQL 项目引用的所有数据库部署到测试项目工作目录中的临时本地数据根文件夹。 

深入了解[如何管理 U-SQL 项目的 U-SQL 数据库项目引用](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)。

### <a name="verify-test-results"></a>验证测试结果

`Run()` 接口返回作业执行结果。 0 表示成功，1 表示失败。 也可以使用 C# 断言函数来验证输出。 

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中运行测试用例

U-SQL 脚本测试项目是基于 C# 单元测试框架生成的。 在生成项目后，可以通过“测试资源管理器”>“播放列表”来运行所有测试用例。 也可以右键单击 .cs 文件并选择“运行测试”。

## <a name="test-c-udos"></a>测试 C# UDO

### <a name="create-test-cases-for-c-udos"></a>为 C# UDO 创建测试用例

可以使用 C# 单元测试框架来测试 C# UDO（用户定义的运算符）。 在测试 UDO 时，需要准备相应的 IRowset 对象作为输入。

可通过两种方法创建 IRowset 对象：

- 加载文件中的数据创建 IRowset：

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

- 使用数据集合中的数据创建 IRowset：

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

调用 UDO 函数后，可以使用 C# 断言函数，通过架构和 Rowset 值验证来验证结果。 可在 U-SQL C# UDO 单元测试示例项目中使用示例代码（在 Visual Studio 中的“文件”>“新建”>“项目”中）。

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中运行测试用例

在生成测试项目后，可以通过“测试资源管理器”>“播放列表”来运行所有测试用例，或右键单击 .cs 文件，然后选择“运行测试”。

## <a name="run-test-cases-in-visual-studio-team-service"></a>在 Visual Studio Team Service 中运行测试用例

U-SQL 脚本测试项目和 C# UDO 测试项目都继承 C# 单元测试项目。 Visual Studio Team Service 中的 [Visual Studio 测试任务](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts)可运行这些测试用例。 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>在 Visual Studio Team Service 中运行 U-SQL 测试用例

对于 U-SQL 测试，请确保在生成计算机上加载 `CPPSDK`，然后将 `CPPSDK` 路径传递到 USqlScriptTestRunner(cppSdkFolderFullPath: @"")。

**什么是 CPPSDK？**

CPPSDK 是包含 Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 的包。 这是 U-SQL 运行时所需的环境。 可以在针对 Visual Studio 的 Azure Data Lake 工具安装文件夹下获取此包：

- 对于 Visual Studio 2015，它位于 `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK` 下
- 对于 Visual Studio 2017，它位于 `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK` 下

**在 Visual Studio Team Service 生成代理中准备 CPPSDK**

在 Visual Studio Team Service 中准备 CPPSDK 依赖项的最常见方法如下：

1.  压缩包含 CPPSDK 库的文件夹。
2.  将 .zip 文件签入到源代码管理系统。 （该 .zip 文件可确保签入 CPPSDK 文件夹下的所有库，从而避免“.gitignore”忽略某些文件。）   
3.  解压缩生成管道中的 .zip 文件。
4.  将 `USqlScriptTestRunner` 指向生成计算机上此已解压缩的文件夹。

### <a name="run-c-udo-test-cases-in-visual-studio-team-services"></a>在 Visual Studio Team Services 中运行 C# UDO 测试用例

对于 C# UDO 测试，请确保引用 UDO 所需的以下程序集。 如果通过 [Nuget 包 Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) 引用这些程序集，请确保在生成管道中添加 NuGet 还原任务。

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>后续步骤

- [如何为 Azure Data Lake Analytics 设置 CI/CD 管道](data-lake-analytics-cicd-overview.md)
- [在本地计算机上运行 U-SQL 脚本](data-lake-analytics-data-lake-tools-local-run.md)
- [使用 U-SQL 数据库项目开发 U-SQL 数据库](data-lake-analytics-data-lake-tools-develop-usql-database.md)


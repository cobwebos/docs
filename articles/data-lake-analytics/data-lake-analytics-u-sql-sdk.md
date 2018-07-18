---
title: 使用 Azure Data Lake U-SQL SDK 在本地运行和测试 U-SQL 作业
description: 了解如何使用命令行和本地工作站上的编程接口在本地运行和测试 U-SQL 作业。
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 11a2bfdcda09a071667cc034ef1ff42794b73a33
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737065"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>使用 Azure Data Lake U-SQL SDK 运行和测试 U-SQL

开发 U-SQL 脚本时，在将其提交到云之前通常需要在本地运行并测试 U-SQL 脚本。 针对此方案，Azure Data Lake 提供了名为“Azure Data Lake U-SQL SDK”的 Nuget 包，通过它可轻松地缩放 U-SQL 运行和测试。 还能将此 U-SQL 测试与 CI（持续集成）系统集成，以自动执行编译和测试。

如果想了解如何通过 GUI 工具手动本地运行和调试 U-SQL 脚本，则可使用针对 Visual Studio 的 Azure Data Lake 工具以实现此操作。 可从[此处](data-lake-analytics-data-lake-tools-local-run.md)了解详细信息。

## <a name="install-azure-data-lake-u-sql-sdk"></a>安装 Azure Data Lake U-SQL SDK

可从 Nuget.org 上的[此处](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)获取 Azure Data Lake U-SQL SDK。在使用它之前，需要确保拥有以下依赖项。

### <a name="dependencies"></a>依赖项

Data Lake U-SQL SDK 需要以下依赖项：

- [Microsoft .NET Framework 4.6 或更高版本](https://www.microsoft.com/download/details.aspx?id=17851)。
- Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 或更高版本（在本文中称为 CppSDK）。 可通过两种方式来获取 CppSDK：

    - 安装 [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)。 Program Files 文件夹下面有一个 \Windows Kits\10 文件夹，例如 C:\Program Files (x86)\Windows Kits\10\。 也可在 \Windows Kits\10\Lib 下找到 Windows 10 SDK 版本。 如果看不到这些文件夹，请重新安装 Visual Studio，并确保在安装期间选择 Windows 10 SDK。 如果已与 Visual Studio 一起安装了它，U-SQL 本地编译器则会自动发现它。

    ![用于 Visual Studio 的 Data Lake 工具本地运行 Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - 安装[用于 Visual Studio 的 Data Lake 工具](http://aka.ms/adltoolsvs)。 可在 C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK 中找到预打包的 Visual C++ 和 Windows SDK 文件。 在本例中，U-SQL 本地编译器无法自动查找依赖项。 需要为它指定 CppSDK 路径。 可将文件复制到其他位置，或按原样使用。

## <a name="understand-basic-concepts"></a>了解基本概念

### <a name="data-root"></a>数据根

数据根文件夹是本地计算帐户的“本地存储”。 它相当于 Data Lake Analytics 帐户的 Azure Data Lake Store 帐户。 切换到不同数据根文件夹与切换到不同存储帐户的操作相同。 如果想要使用不同的数据根文件夹访问一般的共享数据，则必须在脚本中使用绝对路径。 或者，在数据根文件夹下创建指向共享数据的文件系统符号链接（例如，NTFS 上的 **mklink**）。

数据根文件夹的用途：

- 存储本地元数据，包括数据库、表、表值函数 (TVF) 和程序集。
- 查找在 U-SQL 中定义为相对路径的输入和输出路径。 使用相对路径可更轻松地将 U-SQL 项目部署到 Azure。

### <a name="file-path-in-u-sql"></a>U-SQL 中的文件路径

可在 U-SQL 脚本中使用相对路径和本地绝对路径。 相对路径相对于指定的数据根文件夹路径。 建议使用“/”作为路径分隔符，使脚本与服务器端兼容。 下面是一些相对路径及其等效绝对路径的示例。 在这些示例中，C:\LocalRunDataRoot 是数据根文件夹。

|相对路径|绝对路径|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>工作目录

本地运行 U-SQL 脚本时，在编译期间，会在当前运行目录下创建一个工作目录。 除编译输出外，本地执行所需的运行时文件也以卷影形式复制到此工作目录。 工作目录根文件夹名为“ScopeWorkDir”，该工作目录下的文件如下所示：

|目录/文件|目录/文件|目录/文件|定义|说明|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |运行时版本的哈希字符串|本地执行所需的运行时文件卷影副本|
| |Script_66AE4909AA0ED06C| |脚本名称 + 脚本路径的哈希字符串|编译输出和执行步骤日志记录|
| | |\_script\_.abr|编译器输出|代数文件|
| | |\_ScopeCodeGen\_.*|编译器输出|生成的托管代码|
| | |\_ScopeCodeGenEngine\_.*|编译器输出|生成的本机代码|
| | |引用的程序集|程序集引用|引用的程序集文件|
| | |deployed_resources|资源部署|资源部署文件|
| | |xxxxxxxx.xxx[1..n]\_\*.*|执行日志|执行步骤的日志|


## <a name="use-the-sdk-from-the-command-line"></a>从命令行使用 SDK

### <a name="command-line-interface-of-the-helper-application"></a>帮助器应用程序的命令行接口

在 SDK directory\build\runtime 下，LocalRunHelper.exe 是命令行帮助应用程序，它为大多数常用的本地运行函数提供接口。 请注意，命令和参数开关区分大小写。 调用方法：

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

不带参数运行或带 **help** 开关运行 LocalRunHelper.exe 可显示帮助信息：

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

在帮助信息中：

-  **Command** 指定命令的名称。  
-  **Required Argument** 列出必须提供的参数。  
-  **Optional Argument** 列出可选参数及其默认值。  可选的布尔实参不带形参，指定这些实参即意味着使用非默认值。

### <a name="return-value-and-logging"></a>返回值和日志记录

帮助器应用程序返回 **0**（成功）和 **-1**（失败）。 默认情况下，帮助器会将所有消息发送到当前控制台。 但是，大多数命令支持 **-MessageOut path_to_log_file** 可选参数，此参数会将输出重定向到日志文件。

### <a name="environment-variable-configuring"></a>环境变量配置

U-SQL 本地运行需要指定的数据根作为本地存储帐户，还需要针对依赖项的指定 CppSDK 路径。 可以在命令行中设置参数或为它们设置环境变量。

- 设置 **SCOPE_CPP_SDK** 环境变量。

    如果通过安装用于 Visual Studio 的 Data Lake 工具获取 Microsoft Visual C++ 和 Windows SDK，请检查是否存在以下文件夹：

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    定义名为 **SCOPE_CPP_SDK** 的新环境变量并将其指向此目录。 或者将文件夹复制到其他位置，并将 **SCOPE_CPP_SDK** 指定为该位置。

    除设置环境变量外，还可在使用命令行时指定 **-CppSDK** 参数。 此参数将覆盖默认的 CppSDK 环境变量。

- 设置 **LOCALRUN_DATAROOT** 环境变量。

    定义名为 **LOCALRUN_DATAROOT** 的新环境变量并将其指向数据根。

    除设置环境变量外，还可在使用命令行时结合数据根路径指定 **-DataRoot** 参数。 此参数将覆盖默认的数据根环境变量。 另外，需将此参数添加到运行的每个命令行，以便能够覆盖所有操作的默认数据根环境变量。

### <a name="sdk-command-line-usage-samples"></a>SDK 命令行使用示例

#### <a name="compile-and-run"></a>编译和运行

**run** 命令用于编译脚本，并执行编译的结果。 其命令行参数是 **compile** 和 **execute** 命令的参数组合。

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

以下是 **run** 的可选参数：


|参数|默认值|说明|
|--------|-------------|-----------|
|-CodeBehind|False|该脚本具有 .cs 代码隐藏|
|-CppSDK| |CppSDK 目录|
|-DataRoot| DataRoot 环境变量|用于本地运行的 DataRoot，默认为“LOCALRUN_DATAROOT”环境变量|
|-MessageOut| |将控制台上的消息转储到文件中|
|-Parallel|1|使用指定的并行度运行计划|
|-References| |代码隐藏的额外引用数据集或数据文件的路径列表，列表由“;”分隔|
|-UdoRedirect|False|生成 Udo 程序集重定向配置|
|-UseDatabase|master|用于代码隐藏临时程序集注册的数据库|
|-Verbose|False|显示运行时的详细输出|
|-WorkDir|当前目录|编译器用法和输出的目录|
|-RunScopeCEP|0|要使用的 ScopeCEP 模式|
|-ScopeCEPTempPath|temp|用于流式处理数据的临时路径|
|-OptFlags| |用逗号分隔的优化器标志列表|


下面是一个示例：

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

除了将 **compile** 和 **execute** 组合在一起外，还可单独编译和执行已编译的可执行文件。

#### <a name="compile-a-u-sql-script"></a>编译 U-SQL 脚本

**compile** 命令用于将 U-SQL 脚本编译成可执行文件。

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

以下是用于 **compile** 的可选参数：


|参数|说明|
|--------|-----------|
| -CodeBehind [default value 'False']|该脚本具有 .cs 代码隐藏|
| -CppSDK [default value '']|CppSDK 目录|
| -DataRoot [default value 'DataRoot environment variable']|用于本地运行的 DataRoot，默认为“LOCALRUN_DATAROOT”环境变量|
| -MessageOut [default value '']|将控制台上的消息转储到文件中|
| -References [default value '']|代码隐藏的额外引用数据集或数据文件的路径列表，列表由“;”分隔|
| -Shallow [default value 'False']|浅层编译|
| -UdoRedirect [default value 'False']|生成 Udo 程序集重定向配置|
| -UseDatabase [default value 'master']|用于代码隐藏临时程序集注册的数据库|
| -WorkDir [default value 'Current Directory']|编译器用法和输出的目录|
| -RunScopeCEP [default value '0']|要使用的 ScopeCEP 模式|
| -ScopeCEPTempPath [default value 'temp']|用于流式处理数据的临时路径|
| -OptFlags [default value '']|用逗号分隔的优化器标志列表|


下面是一些用法示例。

编译 U-SQL 脚本：

    LocalRunHelper compile -Script d:\test\test1.usql

编译 U-SQL 脚本并设置数据根文件夹。 请注意，这会覆盖设置的环境变量。

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

编译 U-SQL 脚本并设置工作目录、引用程序集和数据库：

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>执行编译的结果

**execute** 命令用于执行编译的结果。   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

以下是用于 **execute** 的可选参数：

|参数|默认值|说明|
|--------|-------------|-----------|
|-DataRoot | '' |元数据执行的数据根。 默认为 **LOCALRUN_DATAROOT** 环境变量。|
|-MessageOut | '' |将控制台上的消息转储到文件。|
|-Parallel | '1' |指示使用指定的并行度运行生成的本地运行步骤。|
|-Verbose | 'False' |指示显示运行时的详细输出。|

下面是用法示例：

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>将 SDK 与编程接口配合使用

所有的编程接口都位于 LocalRunHelper.exe 中。 可以使用这些接口来集成 U-SQL SDK 和 C# 测试框架的功能，以缩放 U-SQL 脚本本地测试。 在本文中，将使用标准 C# 单元测试项目演示如何使用这些接口来测试 U-SQL 脚本。

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>步骤 1：创建 C# 单元测试项目和配置

- 通过“文件”>“新建”>“项目”>“Visual C#”>“测试”>“单元测试项目”来创建 C# 单元测试项目。
- 添加 LocalRunHelper.exe 作为项目的引用。 LocalRunHelper.exe 位于 Nuget 包中的 \build\runtime\LocalRunHelper.exe 处。

    ![Azure Data Lake U-SQL SDK 添加引用](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK **仅**支持 x64 环境，请确保将生成目标平台设置为 x64。 可通过“项目属性”>“生成”>“目标平台”进行设置。

    ![Azure Data Lake U-SQL SDK 配置 x64 项目](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- 请确保将测试环境设置为 x64。 在 Visual Studio 中，可通过“测试”>“测试设置”>“默认处理器体系结构”>“x64”进行设置。

    ![Azure Data Lake U-SQL SDK 配置 x64 测试环境](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- 请确保将 NugetPackage\build\runtime\ 下的所有依赖项文件复制到项目工作目录（通常位于 ProjectFolder\bin\x64\Debug 下）。

### <a name="step-2-create-u-sql-script-test-case"></a>步骤 2：创建 U-SQL 脚本测试用例

以下是 U-SQL 脚本测试的示例代码。 为了进行测试，需要准备脚本、输入文件和预期输出文件。

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>LocalRunHelper.exe 中的编程接口

LocalRunHelper.exe 为 U-SQL 本地编译和运行等提供编程接口。以下列出了这些接口。

**构造函数**

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|参数|Type|说明|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|对于输出消息，设置为 null 以使用控制台|

**属性**

|属性|Type|说明|
|--------|----|-----------|
|AlgebraPath|字符串|代数文件的路径（代数文件是某个编译结果）|
|CodeBehindReferences|字符串|如果脚本有额外的代码隐藏引用，请指定用“;”分隔的路径|
|CppSdkDir|字符串|CppSDK 目录|
|CurrentDir|字符串|当前目录|
|DataRoot|字符串|数据根路径|
|DebuggerMailPath|字符串|调试程序邮件槽的路径|
|GenerateUdoRedirect|bool|是否要生成程序集加载重定向替代配置|
|HasCodeBehind|bool|如果脚本具有.cs 代码隐藏|
|InputDir|字符串|输入数据的目录|
|MessagePath|字符串|消息转储文件路径|
|OutputDir|字符串|输出数据的目录|
|并行度|int|运行代数的并行度|
|ParentPid|int|父级（服务监视器要从中退出）的 PID，设置为 0 或负数以忽略|
|ResultPath|字符串|结果转储文件路径|
|RuntimeDir|字符串|运行时目录|
|ScriptPath|字符串|在何处可以找到脚本|
|Shallow|bool|浅层编译或不编译|
|TempDir|字符串|临时目录|
|UseDataBase|字符串|指定用于代码隐藏临时程序集注册的数据库，默认为 master|
|WorkDir|字符串|首选工作目录|


**方法**

|方法|说明|返回|参数|
|------|-----------|------|---------|
|public bool DoCompile()|编译 U-SQL 脚本|如果成功，则返回 true| |
|public bool DoExec()|执行编译结果|如果成功，则返回 true| |
|public bool DoRun()|运行 U-SQL 脚本（编译 + 执行）|如果成功，则返回 true| |
|public bool IsValidRuntimeDir(string path)|检查给定的路径是否为有效的运行时路径|如果有效，则返回 true|运行时目录的路径|


## <a name="faq-about-common-issue"></a>常见问题

### <a name="error-1"></a>错误 1：
E_CSC_SYSTEM_INTERNAL：内部错误！ 无法加载文件或程序集“ScopeEngineManaged.dll”或其某个依赖项。 找不到指定的模块。

请检查以下事项：

- 请确保具有 x64 环境。 生成目标平台和测试环境应为 x64，请参阅上面的**步骤 1：创建 C# 单元测试项目和配置**。
- 请确保已将 NugetPackage\build\runtime\ 下的所有依赖项文件都复制到了项目工作目录。


## <a name="next-steps"></a>后续步骤

* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 若要记录诊断信息，请参阅 [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)（访问 Azure Data Lake Analytics 的诊断日志）。
* 若要查看更复杂的查询，请参阅 [Analyze website logs using Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)（使用 Azure Data Lake Analytics 分析网站日志）。
* 若要查看作业详细信息，请参阅 [Use Job Browser and Job View for Azure Data Lake Analytics jobs](data-lake-analytics-data-lake-tools-view-jobs.md)（对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图）。
* 若要使用顶点执行视图，请参阅 [Use the Vertex Execution View in Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)（使用用于 Visual Studio 的 Data Lake 工具中的顶点执行视图）。

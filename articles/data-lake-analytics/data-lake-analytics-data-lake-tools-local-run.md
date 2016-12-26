---
title: "使用本地运行和 Azure Data Lake U-SQL SDK 测试和调试 U-SQL 作业 | Microsoft Docs"
description: "了解如何使用适用于 Visual Studio 的 Azure Data Lake 工具和 Azure Data Lake U-SQL SDK 测试和调试本地工作站上的 U-SQL 作业。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 9d088a3afd8830c80462099cc869f1493d09fb41
ms.openlocfilehash: e0385315434ac7a10e121f88469aaaec45160b86


---
# <a name="test-and-debug-u-sql-jobs-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>使用本地运行和 Azure Data Lake U-SQL SDK 测试和调试 U-SQL 作业

了解如何使用适用于 Visual Studio 的 Azure Data Lake 工具和 Azure Data Lake U-SQL SDK 测试和调试本地工作站上的 U-SQL 作业。  使用这两个本地运行功能，可在工作站运行 U-SQL 作业，正如可在 Azure Data Lake 服务中运行一样。 这些功能可节省测试和调试 U-SQL 作业的时间。

先决条件： 

- 一个 Azure Data Lake Analytics 帐户。 请参阅 [Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。 
- 用于 Visual Studio 的 Azure Data Lake 工具。  请参阅[使用适用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。 
- U-SQL 脚本开发体验。 请参阅 [Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。 


## <a name="understand-data-root-and-file-path"></a>了解数据根和文件路径

本地运行和 U-SQL SDK 都需要数据根文件夹。 数据根是用于本地计算帐户的“本地存储”。 在 Azure 中，它相当于 Data Lake Analytics 帐户的 Data Lake Store 帐户。 切换到不同数据根文件夹与切换到不同存储帐户的操作相同。 如需通过不同的数据根文件夹访问普遍共享数据，必须在脚本中使用绝对路径，或在此数据根文件夹下创建指向共享数据的文件系统符号链接（例如，NTFS 上的 mklink）。 

数据根文件夹的用途：

- 存储元数据，包括数据库、表、TVF、程序集等。
- 查找在 U-SQL 中定义为相对路径的输入和输出路径。 使用相对路径可更轻松地将 U-SQL 项目部署到 Azure。

可在 U-SQL 脚本中使用相对路径和本地绝对路径，该相对路径相对于指定的数据根文件夹路径。 建议使用“/”作为路径分隔符，使脚本与服务器端兼容。 下面是一些相对路径及其等效绝对路径的示例。 在这些示例中，“C:\LocalRunDataRoot”是数据根：

|相对路径|绝对路径|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>从 Visual Studio 使用本地运行

用于 Visual Studio 的 Data Lake 工具在 Visual Studio 中提供 U-SQL 本地运行体验。 使用此功能，可以：

- 本地运行 U-SQL 脚本以及 C# 程序集。
- 本地调试 C# 程序集。
- 从服务器资源管理器创建、查看和删除 U-SQL 目录（本地数据库、程序集、架构和表）。 还可从服务器资源管理器中找到本地目录。

    ![用于 Visual Studio 的 Data Lake 工具本地运行本地目录](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake 工具安装程序创建要用作默认数据根文件夹的“C:\LocalRunRoot”文件夹。 默认本地运行并行度为 1。 

### <a name="to-configure-local-run-in-visual-studio"></a>在 Visual Studio 中配置本地运行

1. 打开 Visual Studio。
2. 打开“服务器资源管理器”。
3. 展开“Azure”、“Data Lake Analytics”。
4. 单击“Data Lake”菜单，然后单击“选项和设置”。 
5. 在左侧树中，依次展开“Azure Data Lake”、“常规”。

    ![用于 Visual Studio 的 Data Lake 工具本地运行配置设置](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

执行本地运行需要 Visual Studio U-SQL 项目。 此部分不同于从 Azure 运行 U-SQL 脚本。

### <a name="to-run-a-u-sql-script-locally"></a>本地运行 U-SQL 脚本
1. 从 Visual Studio，打开 U-SQL 项目。   
2. 在解决方案资源管理器中，右键单击 U-SQL 脚本，然后单击“提交脚本”。 选择“(本地)”作为本地运行脚本的 Analytics 帐户。
3. 还可在脚本窗口顶部单击“(本地)”帐户，然后单击“提交”（或使用 **Ctrl+F5** 热键）。

    ![用于 Visual Studio 的 Data Lake 工具本地运行提交作业](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-data-lake-u-sql-sdk"></a>从 Data Lake U-SQL SDK 使用本地运行
 
除使用 Visual Studio 本地运行 U-SQL 脚本外，还可使用 Azure Data Lake U-SQL SDK 通过命令行及编程接口本地运行 U-SQL 脚本，通过此可缩放 U-SQL 本地测试。

### <a name="install-the-sdk"></a>安装 SDK

Data Lake U-SQL SDK 需要以下依赖项：

- [Microsoft .Net Framework 4.6 或更新版本](https://www.microsoft.com/en-us/download/details.aspx?id=17851)。
- Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 或更新版本。 可通过 2 种方法获取此组件：

    - 安装 Visual Studio ([Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou))。 Program Files 文件夹下应有一个“\Windows Kits\10”文件夹，例如C:\Program Files (x86)\Windows Kits\10\"； 还应在“\Windows Kits\10\Lib”下找到 Windows 10 SDK 版本。 如未看到这些文件夹，请重新安装 Visual Studio，并在安装时确保选中了 Windows 10 SDK。 U-SQL 本地编译器脚本通过这种方式自动查找这些依赖项。

    ![用于 Visual Studio 的 Data Lake 工具本地运行 Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)
 
    - 或者，安装[用于 Visual Studio 的 Data Lake 工具](http://aka.ms/adltoolsvs)。 可在 C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK 中找到预打包的 VC++ 和 Windows SDK 文件。 在此情况下，U-SQL 本地编译器无法自动找到依赖项，需为其指定 CppSDK 路径。 可将文件复制到其他位置，或按原样使用。 然后，可选择将环境变量“SCOPE_CPP_SDK”设置到目录中，或在本地运行帮助应用程序的命令行上通过此目录指定“-CppSDK”参数。 

安装 SDK 后，必须执行以下配置步骤：

- 设置 **SCOPE_CPP_SDK** 环境变量

    如果通过安装用于 Visual Studio 的 Data Lake 工具获取 Microsoft Visual C++ 和 Windows SDK，请验证你是否有以下文件夹：

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    定义名为“SCOPE_CPP_SDK”的新环境变量指向此目录。 或将文件夹复制到其他位置，并指定“SCOPE_CPP_SDK”作为该环境变量。

    除设置环境变量外，还可在使用命令行时指定“-CppSDK”参数。 此参数将覆盖默认的 CppSDK 环境变量。 

- 设置 **LOCALRUN_DATAROOT** 环境变量

    定义名为“LOCALRUN_DATAROOT”的新环境变量指向数据根。 

    除设置环境变量外，还可在使用命令行时通过数据根路径指定“-DataRoot”参数。 此参数将覆盖默认的 Data Root 环境变量。 另外，需向所执行的每个命令行添加此参数，以便对所有操作使用此相同的新覆盖 Data Root。 

### <a name="using-the-sdk-from-command-line"></a>从命令行使用 SDK

#### <a name="the-command-line-interface-of-the-helper-application"></a>帮助应用程序的命令行接口

SDK 的 LocalRunHelper.exe 是命令行帮助应用程序，该应用程序向大多数常用的本地运行函数提供接口。 请注意，命令和参数开关均区分大小写。 调用方法：

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

不带参数运行或带 **help** 开关运行“LocalRunHelper.exe”以显示帮助信息：

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

-  **Command** 提供命令名称。  
-  **Required Argument** 列出了必须提供的参数。  
-  **Optional Argument** 列出了具有默认值的可选参数。  可选 bool 实参不含形参，其外观不接受使用默认值。

#### <a name="return-value-and-logging"></a>返回值和日志记录

如果成功，帮助应用程序返回 **0**，如果失败，则返回 **-1**。 默认情况下，帮助程序会将所有消息输出到当前控制台。  但是，大多数命令支持 **-MessageOut path_to_log_file** 可选参数，此参数会将输出重定向到日志文件。


### <a name="the-sdk-usage-samples"></a>SDK 使用示例

#### <a name="compile-and-run"></a>编译和运行

“run”命令用于编译脚本，然后执行编译结果。 其命令行参数是来自“compile”和“run”命令的参数组合。

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

下面是一个示例：

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

除了将“compile”和“run”组合在一起外，还可单独编译和执行已编译的可执行文件。 

#### <a name="compile-u-sql-script"></a>编译 U-SQL 脚本

“compile”命令用于将 U-SQL 脚本编译成可执行文件。 

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

下方是用于编译的可选参数，可在附录中找到更多工作目录 (-WorkDir) 相关信息。

|参数|说明|
|--------|-----------|
|-CppSDK [default value '']|CppSDK 目录|
|-DataRoot [default value '']|用于数据和元数据的 DataRoot，默认为“LOCALRUN_DATAROOT”环境变量|
|-MessageOut [default value '']|将控制台上的消息转储到文件中|
|-Shallow [default value 'False']|浅层编译仅执行脚本的语法检查，并返回结果。|
|-WorkDir [default value 'D:\localrun\t\ScopeWorkDir']|编译器用法和输出目录，详见附录中的“工作目录”。|

程序集和代码隐藏的可选参数：

|参数|说明|
|--------|-----------|
|-CodeBehind [default value 'False']|脚本隐藏了可自动编译并注册为 UDO 对象的 .cs 代码|
|-References [default value '']|代码隐藏的额外引用数据集或数据文件的路径列表，列表由“;”分隔|
|-UseDatabase [default value 'master']|用于代码隐藏临时程序集注册的数据库|
|-UdoRedirect [default value 'False']|生成 Udo 程序集重定向配置，该配置告知 .Net 运行时在调用 UDO 时，首先从已编译输出目录中探测相关程序集|

下面是一些用法示例：

编译 U-SQL 脚本：

    LocalRunHelper compile -Script d:\test\test1.usql

编译 U-SQL 脚本并设置数据根文件夹，请注意，此操作将覆盖已设置的环境变量。

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

编译 U-SQL 脚本并设置工作目录、引用程序集和数据库。

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-result"></a>执行编译结果

“execute”命令用于执行编译结果。   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

可选参数：

|参数|说明|
|--------|-----------|
|-DataRoot [default value '']|用于元数据执行的 DataRoot，默认为“LOCALRUN_DATAROOT”环境变量|
|-MessageOut [default value '']|将控制台上的消息转储到文件中|
|-Parallel [default value '1']|采用指定并行度运行生成的本地运行步骤|
|-Verbose [default value 'False']|显示运行时的详细输出|

下面是用法示例：

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="using-the-sdk-with-programming-interface"></a>结合使用 SDK 和编程接口

编程接口均位于“Microsoft.Analytics.LocalRun”程序集中，通过编程接口，可将 U-SQL SDK 的功能与 C# 测试框架集成以缩放 U-SQL 脚本本地测试，即将推出与此相关的新文档。 有关接口的详细信息，请参阅附录。

## <a name="appendix"></a>附录

### <a name="working-directory"></a>工作目录

本地运行 U-SQL 脚本时，会在编译期间创建一个工作目录。  除编译输出外，本地执行所需的运行时文件也将以卷影形式复制到此工作目录。   如果命令行上未给出 **-WorkDir** 参数，会在当前目录下创建默认的工作目录“ScopeWorkDir”。 工作目录下的文件如下所示。

|目录/文件|定义|说明|
|--------------|----------|-----------|
|ScopeWorkDir|工作目录|根文件夹|
|C6A101DDCB470506|运行时版本的哈希字符串|本地执行所需的运行时文件卷影副本|
|\.\Script_66AE4909AA0ED06C|脚本名称 + 脚本路径的哈希字符串|编译输出和执行步骤日志记录|
|\.\.\\_\_script\_\_.abr|编译器输出|代数文件|
|\.\.\\_\_ScopeCodeGen\_\_.*|编译器输出|生成的托管代码|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|编译器输出|生成的本机代码|
|\.\.\referenced_assemblies|程序集引用|引用程序集文件|
|\.\.\deployed_resources|资源部署|资源部署文件|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|执行日志|执行步骤的日志|

### <a name="programming-interfaces-for-azure-data-lake-u-sql-sdk"></a>用于 Azure Data Lake U-SQL SDK 的编程接口

编程接口均位于“Microsoft.Analytics.LocalRun”程序集中。

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
编译配置参数类

**构造函数**

公共 Configuration(string rootPath)

|参数|类型|说明|
|---------|----|-----------|
|rootPath|System.String|工作上下文的当前目录路径。 如未设置 WorkingDirectory，则默认工作目录为 rootPath +“ScopeWorkDir”。|

**属性**

|名称|说明|
|----|-----------|
|CppSDK|查找 Cpp SDK 的位置，默认使用系统默认配置 |
|DataDirectory|表、程序集和输入/输出数据的保存位置，默认为 ScopeWorkDir\DataDir |
|GenerateUdoRedirect|是否要生成程序集加载重定向替代配置|
|WorkingDirectory|编译器的工作目录，如未设置，则默认为 ScopeWorkDir|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
U-SQL 本地编译器类

**构造函数**

公共 LocalCompiler(Configuration configuration)

|参数|类型|说明|
|---------|----|-----------|
|配置|Microsoft.Analytics.LocalRun.Configuration||

**方法**

公共 bool Compile( string script, string filePath, bool shallow, out CommonCompileResult result )

|参数|类型|说明|
|---------|----|-----------|
|脚本|System.String|输入脚本的字符串|
|filePath|System.String|脚本文件的路径，设置为 null，使用默认值|
|shallow|System.Boolean|浅层编译（仅限语法验证）或完整编译|
|结果|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|详细的编译结果|
|返回值|System.Boolean|true：编译时未出现严重错误。 <br>false：编译时出现严重错误|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
U-SQL 本地运行程序类

**构造函数**

公共 LocalRunner( string algebraFilePath, string dataRoot, Action<string> postMessage = null )

|参数|类型|说明|
|---------|----|-----------|
|algebraFilePath|System.String|代数文件的路径|
|dataRoot|System.String|DataRoot 的路径|
|postMessage (Optional)|System.Action<String>|进程日志记录处理程序|

公共 LocalRunner( string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null )

|参数|类型|说明|
|---------|----|-----------|
|algebraFilePath|System.String|代数文件的路径|
|dataRoot|System.String|DataRoot 的路径|
|cachePath|System.String|编译结果的目录路径，设置为 null 以默认使用代数文件所在的位置|
|runtimePath|System.String|卷影复制运行时的目录路径，设置为 null 以默认使用 cachePath 的父目录位置|
|tempPath|System.String|临时存储路径（仅供内部使用），设置为 null|
|logPath|System.String|写入执行日志的目标路径，设置为 null 以默认使用 cachePath|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|执行状态更改事件通知处理程序|
|eventContext|System. Object|事件处理程序上下文|
|postMessage (Optional)|System.Action<String>|进程日志记录处理程序|

**属性**

|名称|说明|
|----|-----------|
|AlgebraPath|代数文件的路径|
|CachePath|生成的二进制文件所在的编译器结果缓存路径|
|CompletedSteps|完成的步骤数|
|DataRoot|元数据的 DataRoot|
|LastErrorMessage|（继承自 ExecutionStatusBase。）|
|LogPath|日志记录文件存储位置。如不存在，设置程序将创建此目录。不会清除之前创建的日志路径|
|OutputHeader|文本输出中的转储架构标头|
|Parallelism|并行度，默认为逻辑处理器 - 1。启动后更改此设置将引发异常|
|Progress|0 到 100% 缩放的执行进度|
|RuntimePath|运行时文件所在位置，该文件是编译器执行的卷影副本时，就必须是 CachePath 上的一个目录|
|Status|执行状态 <br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // initialize <br>Running,     // it is running,  WaitOne only check the event in this state <br>Success,     // it completed successfully <br>Error,       // it failed <br>}|
|TotalSteps|要运行的步骤总数，有效值仅在生成顶点 DAG 后可用|
|Verbose|执行期间的详细信息|

**方法**

|方法|说明|
|------|-----------|
|Cancel()|取消正运行的代数 <br>返回值 <br>类型：布尔值 <br>false：由于错误，未能取消，检查 LastErrorMessage 了解详细信息|
|Start()|开始运行代数 <br>返回值 <br>类型：布尔值 <br>false：由于错误，未能开始，检查 LastErrorMessage 了解详细信息|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|等待完成。请参阅 WaitHandle.WaitOne|
|Dispose()||


## <a name="see-also"></a>另请参阅

* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。
* 若要着手开发 U-SQL 应用程序，请参阅 [使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。
* 若要记录诊断信息，请参阅 [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要查看作业详细信息，请参阅[对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图](data-lake-analytics-data-lake-tools-view-jobs.md)
* 若要查看如何使用顶点执行视图，请参阅[在适用于 Visual Studio 的 Data Lake 工具中使用顶点执行视图](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)


<!--HONumber=Nov16_HO4-->



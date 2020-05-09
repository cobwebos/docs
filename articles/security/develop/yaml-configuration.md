---
title: Microsoft Azure 安全代码分析任务自定义指南
description: 本文介绍了用于自定义 Microsoft 安全代码分析扩展中的所有任务的 YAML 配置选项
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 11/29/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: cb18f93be62feb5f9fff02fc020f04899ec40a38
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594213"
---
# <a name="yaml-configuration-options-to-customize-the-build-tasks"></a>用于自定义生成任务的 YAML 配置选项

本文列出了每个生成任务中可用的所有 YAML 配置选项。 本文首先介绍安全代码分析工具的任务。 它以后续处理任务结束。

## <a name="anti-malware-scanner-task"></a>反恶意软件扫描器任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| #A2 | 列表 | 通用 | True | 基本 | 基本、自定义 | 
| ScanType | 列表 | InputType = 基本 | True | CustomScan | CustomScan, FullSystemScan, QuickScan, YourConfiguredScan | 要用于反恶意软件扫描的扫描类型。
| FileDirPath | filePath | ScanType = CustomScan | True | $ （Build.stagingdirectory） |  | 指示要扫描的文件或目录。
| DisableRemediation | boolean | ScanType = CustomScan | False | 是 |  | 如果选中：1），则忽略文件排除项。 2）扫描存档文件。 3）检测后不应用操作。 4）检测后不写入事件日志项。 5）自定义扫描的检测不显示在用户界面中。 6）控制台输出将显示自定义扫描的检测列表。
| BootSectorScan | boolean | ScanType = CustomScan | False | false |  | 如果选中，将启用启动扇区扫描。
| 参数 | string | InputType = Custom | True | -ScanType DisableRemediation-File $ （Build.stagingdirectory） |  | 命令行参数，其中-File 的参数是一个绝对路径，或者是在生成代理上预定义的 $ （Build.stagingdirectory）的相对路径。 注意：如果未提供自变量作为最后一个参数，则默认为 $ （Build.stagingdirectory）。 你还可以提供 MpCmdRun 工具允许的自变量。<br/><br/>有关此工具的命令行参数的更多详细信息，请输入<strong>-h</strong>或<strong>-？</strong> 并执行生成任务。
| EnableServices | boolean | 通用 | True | false |  | 如果选中，则在禁用所需的服务时，它将尝试为其启用 Windows 更新。<br/>**注意**：请确保组策略不禁用服务，并且运行此生成的帐户具有管理员权限。
| SupportLogOnError | boolean | 通用 | True | false |  | 如果选中，则在发生错误时，它将收集支持文件以供诊断。 此过程可能需要几分钟时间。<br/>**注意**：请确保运行此生成的帐户具有管理员权限。
| TreatSignatureUpdateFailureAs | 列表 | 通用 | True | 警告 | 错误，标准，警告 | 在运行时无法更新签名时使用的日志级别。 如果设置为 "**错误**"，则无法更新签名将导致生成任务失败。 请注意，签名更新在托管生成代理上会失败，即使签名可能相对较新（不超过3小时）。
| SignatureFreshness | 列表 | 通用 | True | UpToDate | OneDay、ThreeDays、TwoDays、UpToDate | 反恶意软件签名允许的最大期限。 如果签名无法更新且早于此值，则生成任务将根据 "**验证签名 Age Age** " 字段中的选定值进行。 注意：如果选择 "**最**新"，则允许签名最多为3小时。
| TreatStaleSignatureAs | 列表 | 通用 | True | 错误 | 错误，标准，警告 | 签名年龄超过选定**反恶意软件签名期限**时使用的日志级别。 过期的签名可能被视为**警告**或**信息**，以继续进行反恶意软件扫描，但不建议这样做。

## <a name="binskim-task"></a>BinSkim 任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| #A2 | 列表 | 通用 | True | 基本 | 基本、命令行 | 
| 参数 | string | InputType = 命令行 | True |  |  | 要执行的标准 Binskim 命令行参数。 将删除并替换输出路径。<br>有关此工具的命令行参数的更多详细信息，请在 "参数" 字段中输入**帮助**，然后执行生成任务。
| 函数 | 列表 | InputType = 基本 | True | 旁 | 分析、转储、exportConfig、exportRules | 
| AnalyzeTarget | filePath | InputType = Basic && 函数 = 分析 | True | $ （ArtifactStagingDirectory）\*.dll;<br>$ （ArtifactStagingDirectory）\*.exe |  | 可解析为要分析的一个或多个二进制文件、目录或筛选器模式的一个或多个说明符。 （";" 分隔列表）
| AnalyzeSymPath | string | InputType = Basic && 函数 = 分析 | False |  |  | 目标的符号文件的路径。
| AnalyzeConfigPath | string | InputType = Basic && 函数 = 分析 | False | default |  | 策略文件的路径，该文件将用于配置分析。 传递值 "default" 以使用内置设置。
| AnalyzePluginPath | string | InputType = Basic && 函数 = 分析 | False |  |  | 将对分析集中的所有目标调用的插件的路径。
| AnalyzeRecurse | boolean | InputType = Basic && 函数 = 分析 | False | 是 |  | 在计算文件说明符参数时递归到子目录中。
| AnalyzeVerbose | boolean | InputType = Basic && 函数 = 分析 | False | false |  | 发出详细输出。 生成的综合性报表旨在提供符合性方案的相应证据。
| AnalyzeHashes | boolean | InputType = Basic && 函数 = 分析 | False | false |  | 发出 SARIF 报表时输出 SHA-256 分析目标的哈希。
| AnalyzeStatistics | boolean | InputType = Basic && 函数 = 分析 | False | false |  | 为分析会话生成计时和其他统计信息。
| AnalyzeEnvironment | boolean | InputType = Basic && 函数 = 分析 | False | false |  | 运行到输出文件的日志计算机环境详细信息。 警告：此选项将可能的敏感信息（如所有环境变量值）记录到任何已发出的日志中。
| ExportRulesOutputType | 列表 | InputType = Basic && 函数 = exportRules | False | SARIF | SARIF、SonarQube | 要输出的规则描述符文件的类型。 这将包含在发布安全分析日志生成任务发布的 BinSkim logs 文件夹中。
| DumpTarget | filePath | InputType = Basic && 函数 = 转储 | True | $ （ArtifactStagingDirectory） |  | 可解析为要分析的一个或多个二进制文件、目录或筛选器模式的一个或多个说明符。 （";" 分隔列表）
| DumpRecurse | boolean | InputType = Basic && 函数 = 转储 | False | 是 |  | 在计算文件说明符参数时递归到子目录中。
| DumpVerbose | boolean | InputType = Basic && 函数 = 转储 | False | 是 |  | 发出详细输出。 生成的综合性报表旨在提供符合性方案的相应证据。
| toolVersion | 列表 | 通用 | False | 最新版本 | 1.5.0 _，最新，LatestPreRelease | 要运行的工具的版本。

## <a name="credential-scanner-task"></a>凭据扫描器任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| outputFormat | 列表 | 通用 | False | pre | csv，pre，tsv | 凭据扫描程序结果文件的输出格式。
| toolVersion | 列表 | 通用 | False | 最新版本 | 1.27.7、最新、LatestPreRelease | 要运行的工具的版本。
| scanFolder | filePath | 通用 | False | $ （Build.sourcesdirectory） |  | 要扫描凭据的存储库中的文件夹。
| searchersFileType | 列表 | 通用 | False | 默认 | Custom、Default、DefaultAndCustom | 用于查找用于扫描的 searchers 文件的选项。
| searchersFile | filePath | searchersFileType = = Custom 或 searchersFileType = = DefaultAndCustom | False |  |  | 要执行的检查的 Credential 扫描器 searchers 配置文件。 可以通过提供一个逗号分隔的凭据扫描程序 searchers 文件路径列表来包含和使用多个值。
| suppressionsFile | filePath | 通用 | False |  |  | 用于抑制输出日志中的问题的凭据扫描程序禁止显示文件。
| suppressAsError | boolean | 通用 | False | false |  | 取消的匹配将输出到输出文件 [-O]-匹配项。[-f]，而不是默认的已取消输出文件 [-O]-已取消。[-f]。 （默认值为 "False"）
| verboseOutput | boolean | 通用 | False | false |  | 输出详细信息。
| batchSize | string | 通用 | False |  |  | 用于并行运行凭据扫描器的并发线程数。 （默认值为20）<br/>值必须在1-2147483647 范围内。
| regexMatchTimeoutInSeconds | string | 通用 | False |  |  | 在放弃检查之前，尝试搜索者匹配所用的时间（以秒为单位）。<br/>添加``-Co RegexMatchTimeoutInSeconds=<Value>``到命令行。
| fileScanReadBufferSize | string | 通用 | False |  |  | 读取内容时缓冲区大小（以字节为单位）。 （默认值为524288）<br/>添加``-Co FileScanReadBufferSize=<Value>``到命令行。
| maxFileScanReadBytes | string | 通用 | False |  |  | 内容分析期间要从给定文件中读取的最大字节数。 （默认值为104857600）<br/>添加``-Co MaxFileScanReadBytes=<Value>``到命令行。

## <a name="microsoft-security-risk-detection-task"></a>Microsoft 安全风险检测任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                           |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 为 serviceendpointname | connectedService：泛型 | 通用 | True |  |  | VSTS 项目上预配置服务终结点的名称（通用类型），该终结点存储 MSRD 实例 URL （你已载入到）和 REST API 访问令牌（该令牌是从你的帐户设置页面生成的，并允许对你的帐户拥有完全访问权限）。
| AccountId | string | 通用 | True |  |  | 标识帐户的 GUID。 可以从帐户 URL 检索它。
| BinariesURL | string | 通用 | True |  |  | 要由模糊化计算机用来下载二进制文件的以分号分隔的 Url 列表。 确保 Url 可公开使用。
| SeedsURL | string | 通用 | False |  |  | 要由模糊化计算机用来下载种子的以分号分隔的 Url 列表。 请确保 Url 是公开的。
| OSPlatformType | 列表 | 通用 | True | Windows | Linux、Windows | 要在其上运行模糊化作业的计算机的 OS 平台类型。
| WindowsEdition | string | OSPlatformType = Windows | True | Server 2008 R2 |  | 要在其上运行模糊化作业的计算机的 OS 版本。
| LinuxEdition | string | OSPlatformType = Linux | True | Redhat |  | 要在其上运行模糊化作业的计算机的 OS 版本。
| PreSubmissionCommand | string | 通用 | False |  |  | 需要在测试计算机上运行的脚本，以便在提交模糊化作业之前安装测试目标程序及其依赖项。
| SeedDirectory | string | 通用 | True |  |  | 包含种子的模糊化计算机上的目录的路径。 有关详细信息，请参阅[Seed File Directory](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/03-choose-seed-files#seed-file-directory) 。
| SeedExtension | string | 通用 | True |  |  | 种子的文件扩展名。
| TestDriverExecutable | string | 通用 | True |  |  | 模糊计算机上目标可执行文件的路径。 有关详细信息，请参阅[EPE 的完整路径](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/02-choose-exe#full-path-to-the-epe)。
| TestDriverExeType | 列表 | 通用 | True | x86 | amd64，x86 | 目标可执行文件体系结构。
| TestDriverParameters | string | 通用 | True | "% testfile.txt%" |  | 传递给测试目标可执行文件的命令行参数。 请注意，会自动将 **"% testfile.txt%"** 符号（包括双引号）替换为测试驱动程序预期要分析的目标文件的完整路径，并且是必需的。 有关详细信息，请参阅[命令行参数](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/02-choose-exe#command-line-arguments)。
| ClosesItself | boolean | 通用 | True | 是 |  | 检查测试驱动程序是否在完成后终止自身（例如，测试驱动程序将分析输入文件并立即退出）;如果需要强行关闭测试驱动程序（例如，测试驱动程序是一种 GUI 应用程序，在分析输入后，其主窗口仍保持打开状态）。 有关详细信息，请参阅[自我终止](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/05-scope-exe-lifetime#self-termination)。
| MaxDurationInSeconds | string | 通用 | True | 5 |  | 测试驱动程序的最长持续时间（秒）。 提供目标程序分析输入文件所需的最长预期时间的估计。 此估计越精确，模糊化运行的效率就越高。 有关详细信息，请参阅[预期的最大执行持续时间](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/05-scope-exe-lifetime#maximum-expected-execution-duration)。
| CanRunRepeat | boolean | 通用 | True | 是 |  | 检查是否可以重复运行测试驱动程序，而无需依赖于持久化/shared 全局状态。 有关详细信息，请参阅[从头开始运行](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#runs-from-scratch)。
| CanTestDriverBeRenamed | boolean | 通用 | True | false |  | 检查是否可以重命名测试驱动程序可执行文件，并且仍能正常工作。 有关详细信息，请参阅[可重命名和并行](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#can-be-renamed-and-parallelized)化。
| SingleOsProcess | boolean | 通用 | True | false |  | 检查测试驱动程序是否在单个操作系统进程下运行;如果测试驱动程序生成其他进程，则取消选中。 有关详细信息，请参阅[单一进程](https://docs.microsoft.com/security-risk-detection/how-to/submit-windows-fuzzing-job/04-describe-exe-characteristics#single-process)。

## <a name="roslyn-analyzers-task"></a>Roslyn 分析器任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                   |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| userProvideBuildInfo | 列表 | 通用 | True | auto | auto、msBuildInfo | 用户为 Roslyn 分析提供 MSBuild 版本、MSBuild 体系结构和生成命令行选项。 如果选择了 "**自动**"，则此任务将从同一管道中的以前的**MSBuild**、 **VSBuild**和/或 **.net Core** （用于生成）任务中检索生成信息。
| msBuildVersion | 列表 | userProvideBuildInfo = = msBuildInfo | True | 16.0 | 15.0、16。0 | MSBuild 版本。
| msBuildArchitecture | 列表 | userProvideBuildInfo = = msBuildInfo | True | x86 | DotNetCore、x64、x86 | MSBuild 体系结构。 注意：如果生成命令行调用**dotnet build**，请选择**Via .Net Core**选项。
| msBuildCommandline | string | userProvideBuildInfo = = msBuildInfo | True |  |  | 用于编译解决方案或项目的完整生成命令行。<br/><br/>注意：命令行应以**msbuild.exe**或**dotnet**的完整路径开头。<br/>将使用 $ （Build.sourcesdirectory）作为工作目录运行该命令。
| rulesetName | 列表 | 通用 | False | 建议 | 自定义，无，建议，必需 | 要使用的命名规则集。<br/><br/>如果`Ruleset Configured In Your Visual Studio Project File(s)`选择了，则将使用在 VS 项目文件中预先配置的规则集。 如果`Custom`选择了，则可以设置自定义规则集路径选项。
| rulesetVersion | 列表 | rulesetName = = Required 或 rulesetName = = 推荐 | False | 最新版本 | 8.0，8.1，8.2，最新，LatestPreRelease | 所选 SDL 规则集的版本。
| customRuleset | string | rulesetName = Custom | False |  |  | 要使用的规则集的可访问路径。 相对路径将规范化为源存储库（`$(Build.SourcesDirectory)`）的根。<br/><br/>如果规则集指定`Rules` ， `Actions`并将`Error`设置为，则生成任务将失败。 若要使用这样做的规则集，请`Continue on error`签入生成任务的`Control Options`。
| microsoftAnalyzersVersion | 列表 | 通用 | False | 最新版本 | 2.9.3、2.9.4、2.9.6、最新软件、LatestPreRelease | 要运行的[CodeAnalysis](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)包的版本。
| suppressionFileForCompilerWarnings | filePath | 通用 | False |  |  | 禁止显示 c # 和 VB 编译器警告的禁止显示的文件。<br/><br/>每个警告 ID 列有一个单独的行的纯文本文件。<br/>对于编译器警告，请仅指定警告标识符的数字部分。 例如，1018将取消 CS1018，CA1501 将禁止显示 CA1501。<br/><br/>相对文件路径将追加到源存储库的根目录（`$(Build.SourcesDirectory)`）。

## <a name="tslint-task"></a>TSLint 任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RuleLibrary | 列表 | 通用 | True | tslint | custom、microsoft、tslint | 所有结果都包括 TSLint 的选定版本随附的规则（**仅限基准**）。<br/><br/>**仅限基准-** 仅 TSLint 附带的规则。<br/><br/>**包括 Microsoft 规则-** 下载[tslint-contrib](https://github.com/Microsoft/tslint-microsoft-contrib) ，并包含其可在 tslint 中使用的规则。 如果选择此选项， `Type Checking`则将隐藏该复选框，因为 Microsoft 的规则需要该复选框，并将自动使用。 它还取消隐藏特定`Microsoft Contribution Version`字段，允许选择`tslint-microsoft-contrib`从[npm](https://www.npmjs.com/package/tslint-microsoft-contrib)的版本。<br/><br/>**包括自定义规则-** 取消隐藏特定`Rules Directory`字段，该字段接受可在 TSLint 中使用的 TSLint 规则目录的可访问路径。<br/><br/>**注意：** 默认值已更改为 tslint，因为许多用户在配置 Microsoft 规则集时遇到问题。 有关特定版本的配置，请参阅[GitHub 上的 tslint-contrib](https://github.com/microsoft/tslint-microsoft-contrib)。
| RulesDirectory | string | RuleLibrary = = custom | True |  |  | 一个可访问目录，其中包含可在 TSLint 中使用的其他 TSLint 规则。
| 集中 | 列表 | RuleLibrary！ = microsoft | True | tsrecommended | custom、tslatest、tsrecommended | 定义要对 TypeScript 文件运行的规则。<br/><br/>** [tslint：最新](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) - **扩展`tslint:recommended`并不断更新，以包括每个 TSLint 版本中最新规则的配置。 使用此配置可能会导致小版本的重大更改，因为启用了新规则，这会导致代码中出现不起毛的错误。 当 TSLint 达到主要版本凸块时， `tslint:recommended`将更新为与完全相同`tslint:latest`。<br/><br/>** [tslint：建议](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) - **一组稳定的、固执的规则，TSLint 建议使用一般的 TypeScript 编程。 此配置如下`semver`所示，因此它在次要版本或修补程序版本中*不*会有重大更改。
| RulesetMicrosoft | 列表 | RuleLibrary = = microsoft | True | mssdlrequired | custom、msrecommended、mssdlrecommended、mssdlrequired、tslatest、tsrecommended | 定义要对 TypeScript 文件运行的规则。<br/><br/>** [microsoft： sdl-必需](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) - **运行 tslint 提供的所有可用检查和满足*所需*[安全开发生命周期（SDL）](https://www.microsoft.com/sdl/)策略的 tslint-contrib 规则。<br/><br/>** [microsoft： sdl-建议](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) - **运行 tslint 提供的所有可用检查和满足*必需和推荐*的[安全开发生命周期（SDL）](https://www.microsoft.com/sdl/)策略的 tslint-contrib 规则。<br/><br/>**microsoft：推荐**Tslint contrib 规则的创建者建议的所有检查。 这包括安全检查和非安全检查。<br/><br/>** [tslint：最新](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) - **扩展`tslint:recommended`并不断更新，以包括每个 TSLint 版本中最新规则的配置。 使用此配置可能会导致小版本的重大更改，因为启用了新规则，这会导致代码中出现不起毛的错误。 当 TSLint 达到主要版本凸块时， `tslint:recommended`将更新为与完全相同`tslint:latest`。<br/><br/>** [tslint：建议](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) - **一组稳定的、固执的规则，TSLint 建议使用一般的 TypeScript 编程。 此配置如下`semver`所示，因此它在次要版本或修补程序版本中*不*会有重大更改。
| RulesetFile | string | 规则集 = = custom 或 RulesetMicrosoft = = custom | True |  |  | 指定要运行的规则的[配置文件](https://palantir.github.io/tslint/usage/cli/)。<br/><br/>配置的路径将添加为[自定义规则](https://palantir.github.io/tslint/develop/custom-rules/)的路径。
| FileSelectionType | 列表 | 通用 | True | fileGlob | fileGlob、projectFile | 
| 文件 | string | FileSelectionType = = fileGlob | True | **\*。 ts |  | 确定要处理的文件的[glob](https://www.npmjs.com/package/glob)文件。 路径相对于`Build.SourcesDirectory`值。<br/><br/>Microsoft 的贡献库需要使用项目文件。 如果你使用的是 Microsoft 的 "发布内容`File Glob Pattern`库" 选项，则会为你生成项目文件。
| ECMAScriptVersion | 列表 | FileSelectionType = = fileGlob && RuleLibrary = = microsoft | True | ES3 | ES2015、ES2016、ES2017、ES3、ES5、ES6、ESNext | 用 TypeScript 编译器配置的 ECMAScript 的目标版本。 使用项目文件时，这是 TypeScript tsconfig.json 文件的 compilerOptions 字段。
| 项目 | string | FileSelectionType = = projectFile | True |  |  | [Tsconfig.json](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html)文件的路径，该文件指定要在其上运行 TSLint 的 TypeScript 文件。 路径相对于`Build.SourcesDirectory`值。
| TypeCheck | boolean | RuleLibrary！ = microsoft && FileSelectionType = = projectFile | False | 是 |  | 在运行 linting 规则时启用类型检查器。
| ExcludeFiles | string | 通用 | False |  |  | 指示要从 linting 中排除的文件的[glob](https://www.npmjs.com/package/glob) 。 路径相对于`Build.SourcesDirectory`值。 可以指定多个值，用分号分隔。
| OutputFormat | 列表 | 通用 | True | json | checkstyle、codeFrame、filesList、json、msbuild、pmd、prose、时尚、verbose、vso | 用于生成输出的[格式化程序](https://palantir.github.io/tslint/formatters/)。 请注意，JSON 格式与后期分析兼容。
| NodeMemory | string | 通用 | False |  |  | 要分配给节点以运行 TSLint 的显式内存量（Mb）。 示例：8000<br/><br/>映射到节点`--max_old_space=<value>`的 CLI 选项，这是`v8 option`。
| ToolVersion | 列表 | RuleLibrary！ = microsoft | True | 最新 | 4.0.0，4.0.1，4.0.2，4.1.0，4.1.1，4.2.0，4.3.0，4.3.1，4.4.0，4.4.1，4.4.2，4.5.0，4.5.1，5.0.0，5.1.0，5.2.0，5.3.0，5.3.2，5.4.0，5.4.1，5.4.2，5.4.3，5.5.0，最新 | 要下载并运行的 TSLint 的[版本](https://github.com/palantir/tslint/releases)。
| TypeScriptVersion | 列表 | 通用 | True | 最新 | 0.8.0、0.8.1，0.8.2，0.8.3，0.9.0，0.9.1，0.9.5，0.9.7，1.0.0，1.0.1，1.3.0，1.4.1，1.5.3，1.6.2，1.7.3，1.7.5，1.8.0，1.8.10，1.8.2，1.8.5，1.8.6，1.8.7，1.8.9，1.9.0，2.0.0，v2.0.10，2.0.2，2.0.3，2.0.6，2.0.7，2.0.8，2.0.9，2.1.4，2.1.5，2.1.6，2.2.0，2.2.1，，，， | 要下载和使用的[typescript](https://www.npmjs.com/package/typescript)版本。<br/>**注意：** 这需要与用于编译代码的 TypeScript 版本相同。
| TypeScriptVersionCustom | string | TypeScriptVersion = = custom | True | 最新 |  | 要下载和使用的[typescript](https://www.npmjs.com/package/typescript)版本。<br/>**注意：** 这需要与用于编译代码的 TypeScript 版本相同。
| MicrosoftContribVersion | 列表 | RuleLibrary = = microsoft |  | 最新 | 4.0.0，4.0.1，5.0.0，5.0.1，最新 | 要下载和使用的[tslint-contrib](https://www.npmjs.com/package/tslint-microsoft-contrib) （SDL 规则）的版本。</br>**注意：** 将选择与[tslint](https://www.npmjs.com/package/tslint) -contrib 所选版本兼容的版本。 对 tslint 的更新-contrib 将在此生成任务中封闭，直到发生一段测试。

## <a name="publish-security-analysis-logs-task"></a>发布安全分析日志任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ArtifactName | string | 通用 | True | CodeAnalysisLogs |  | 要创建的项目的名称。
| ArtifactType | 列表 | 通用 | True | 容器 | 容器，FilePath | 要创建的项目的类型。
| TargetPath | string | ArtifactType = FilePath | False | \\my\share\$（DefinitionName）<br>\$（BuildNumber） |  | 要将文件复制到的文件共享
| AllTools | boolean | 通用 | True | 是 |  | 发布所有安全开发工具生成任务生成的结果。
| 反恶意软件 | boolean | AllTools = false | True | 是 |  | 发布反恶意软件生成任务生成的结果。
| BinSkim | boolean | AllTools = false | True | 是 |  | 发布 BinSkim 生成任务生成的结果。
| CredScan | boolean | AllTools = false | True | 是 |  | 发布由 Credential 扫描器生成任务生成的结果。
| MSRD | boolean | AllTools = false | True | 是 |  | 为 MSRD 生成任务启动的 MSRD 作业发布作业信息和作业 url。 MSRD 作业长时间运行，并提供单独的报表。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 发布 Roslyn 分析器生成任务生成的结果。
| TSLint | boolean | AllTools = false | True | 是 |  | 发布 TSLint 生成任务生成的结果。 请注意，报表仅支持 JSON 格式的 TSLint 日志。 如果选择了不同的格式，请相应地更新 TSLint 生成任务。
| ToolLogsNotFoundAction | 列表 | 通用 | True | Standard | 错误，无，标准，警告 | 当找不到所选工具（或所有工具均已选中）的日志时要执行的操作，表示此工具未运行。<br/><br/>**选项：**<br/>**无：** 将消息写入到详细输出流，只能通过将 VSTS 变量**system**设置为**true**。<br/>**Standard：** （默认值）写入标准输出消息，该消息未找到该工具的日志。<br/>**警告：** 写入一个黄色警告消息，该工具未找到该工具的任何日志，这会在 "生成摘要" 页上显示为警告。<br/>**错误：** 写入红色错误消息并引发异常，从而中断生成。 使用此选项可确保使用各个工具选项来确保运行的工具。

## <a name="security-report-task"></a>安全报告任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VstsConsole | boolean | 通用 | False | 是 |  | 将结果写入管道控制台。
| TsvFile | boolean | 通用 | False | 是 |  | 生成一个 tsv 文件（制表符分隔值），每个查找结果对应一行，并为结果分隔信息。
| HtmlFile | boolean | 通用 | False | 是 |  | 生成 html 报表文件。
| AllTools | boolean | 通用 | True | false |  | 报告所有安全开发工具生成任务生成的结果。
| BinSkim | boolean | AllTools = false | True | false |  | 报告 BinSkim 生成任务生成的结果。
| BinSkimBreakOn | 列表 | AllTools = true 或 BinSkim = true | True | 错误 | 错误，WarningAbove | 要报告的结果级别。
| CredScan | boolean | AllTools = false | True | false |  | 报告 Credential 扫描器生成任务生成的结果。
| MSRD | boolean | AllTools = false | True | false |  | 报告由 MSRD 生成任务启动的 MSRD 作业的作业信息和作业 url。 MSRD 作业长时间运行，并提供单独的报表。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 报告 Roslyn Analyzer 生成任务生成的结果。
| RoslynAnalyzersBreakOn | 列表 | AllTools = true 或 RoslynAnalyzers = true | True | 错误 | 错误，WarningAbove | 要报告的结果级别。
| TSLint | boolean | AllTools = false | True | false |  | 报告 TSLint 生成任务生成的结果。 请注意，报表仅支持 JSON 格式的 TSLint 日志。 如果选择了不同的格式，请相应地更新 TSLint 生成任务。
| TSLintBreakOn | 列表 | AllTools = true 或 TSLint = true | True | 错误 | 错误，WarningAbove | 要报告的结果级别。
| ToolLogsNotFoundAction | 列表 | 通用 | True | Standard | 错误，无，标准，警告 | 当找不到所选工具（或所有工具均已选中）的日志时要执行的操作，表示此工具未运行。<br/><br/>**选项：**<br/>**无：** 将消息写入到详细输出流，只能通过将 VSTS 变量**system**设置为**true**。<br/>**Standard：** （默认值）写入标准输出消息，该消息未找到该工具的日志。<br/>**警告：** 写入一个黄色警告消息，该工具未找到该工具的任何日志，这会在 "生成摘要" 页上显示为警告。<br/>**错误：** 写入红色错误消息并引发异常，从而中断生成。 使用此选项可确保使用各个工具选项来确保运行的工具。
| CustomLogsFolder | string | 通用 | False |  |  | 分析工具日志所在的基文件夹;单个日志文件将位于此路径下的每个工具之后名为的子文件夹中。

## <a name="post-analysis-task"></a>分析后任务

| **#A2**      | 类型      | **才**            | **必需** | **默认值**             | **选项（对于选择列表）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AllTools | boolean | 通用 | True | false |  | 如果任何 Microsoft 安全代码分析生成任务发现任何问题，请中断生成。
| BinSkim | boolean | AllTools = false | True | false |  | 如果找到任何 BinSkim 问题，则根据所选的 "中断" 选项，中断生成。
| BinSkimBreakOn | 列表 | AllTools = true 或 BinSkim = true | True | 错误 | 错误，WarningAbove | 用于中断生成的问题的级别。
| CredScan | boolean | AllTools = false | True | false |  | 如果发现了任何凭据扫描程序问题，请中断生成。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 如果找到任何 Roslyn 分析器问题，请中断生成。
| RoslynAnalyzersBreakOn | 列表 | AllTools = true 或 RoslynAnalyzers = true | True | 错误 | 错误，WarningAbove | 用于中断生成的问题的级别。
| TSLint | boolean | AllTools = false | True | false |  | 如果找到任何 TSLint 问题，请中断生成。 请注意，后期分析仅支持 JSON 格式的 TSLint 日志。 如果选择了不同的格式，请相应地更新 TSLint 生成任务。
| TSLintBreakOn | 列表 | AllTools = true 或 TSLint = true | True | 错误 | 错误，WarningAbove | 用于中断生成的问题的级别。
| VstsConsole | boolean | 通用 | False | 是 |  | 将结果写入管道控制台。
| ToolLogsNotFoundAction | 列表 | 通用 | True | Standard | 错误，无，标准，警告 | 当找不到所选工具（或所有工具均已选中）的日志时要执行的操作，表示此工具未运行。<br/><br/>**选项：**<br/>**无：** 将消息写入到详细输出流，只能通过将 VSTS 变量**system**设置为**true**。<br/>**Standard：** （默认值）写入标准输出消息，该消息未找到该工具的日志。<br/>**警告：** 写入一个黄色警告消息，该工具未找到该工具的任何日志，这会在 "生成摘要" 页上显示为警告。<br/>**错误：** 写入红色错误消息并引发异常，从而中断生成。 使用此选项可确保使用各个工具选项来确保运行的工具。

## <a name="next-steps"></a>后续步骤

如果你对安全代码分析扩展和提供的工具有其他疑问，请查看[我们的常见问题页面](security-code-analysis-faq.md)。
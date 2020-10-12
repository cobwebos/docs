---
title: Microsoft Azure 安全代码分析任务自定义指南
description: 本文介绍/列出了用于在 Microsoft 安全代码分析扩展中自定义所有任务的 YAML 配置选项
author: sukhans
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
ms.openlocfilehash: 6985107dd8f13e26875cf5ea7428b3280d00cea1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85367251"
---
# <a name="yaml-configuration-options-to-customize-the-build-tasks"></a>用于自定义生成任务的 YAML 配置选项

本文列出了每个生成任务中可用的所有 YAML 配置选项。 本文首先介绍了与安全代码分析工具相关的任务， 最后介绍了处理后任务。

## <a name="anti-malware-scanner-task"></a>反恶意软件扫描程序任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | pickList | 通用 | True | 基本 | Basic、Custom | 
| ScanType | pickList | InputType = Basic | True | CustomScan | CustomScan、FullSystemScan、QuickScan、YourConfiguredScan | 要用于反恶意软件扫描的扫描类型。
| FileDirPath | filePath | ScanType = CustomScan | True | $(Build.StagingDirectory) |  | 指示要扫描的文件或目录。
| DisableRemediation | boolean | ScanType = CustomScan | False | 是 |  | 当选中时：1) 将忽略文件排除。 2) 将扫描存档文件。 3) 检测后不应用操作。 4) 检测后不写入事件日志条目。 5) 来自自定义扫描的检测不会显示在用户界面中。 6) 控制台输出将显示来自自定义扫描的检测列表。
| BootSectorScan | boolean | ScanType = CustomScan | False | false |  | 如果选中，它将启用启动扇区扫描。
| 参数 | string | InputType = Custom | True | -Scan -ScanType 3 -DisableRemediation -File $(Build.StagingDirectory) |  | 命令行参数，其中，-File 的参数是一个绝对路径，或者是相对于生成代理上预定义的 $(Build.StagingDirectory) 的相对路径。 注意：如果没有提供 -File 的参数作为最后一个参数，则它将默认为 $(Build.StagingDirectory)。 你还可以提供 MpCmdRun.exe 工具允许的你自己的参数。<br/><br/>若要获得有关此工具的命令行参数的更多详细信息，请在 Arguments 字段中输入 <strong>-h</strong> 或 <strong>-?</strong> 并执行生成任务。
| EnableServices | boolean | 通用 | True | false |  | 如果选中，则当 Windows Update 所需的服务被禁用时，它会尝试启用这些服务。<br/>**注意**：请确保组策略没有禁用这些服务，并且运行此生成的帐户具有管理员权限。
| SupportLogOnError | boolean | 通用 | True | false |  | 如果选中此项，则当发生错误时，它会收集用于诊断的支持文件。 此过程可能需要几分钟时间。<br/>**注意**：请确保运行此生成的帐户具有管理员权限。
| TreatSignatureUpdateFailureAs | pickList | 通用 | True | 警告 | Error、Standard、Warning | 在运行时无法更新特征时使用的日志级别。 如果设置为 **Error**，则无法更新特征会导致生成任务失败。 请注意，在托管的生成代理上，特征更新失败很常见，即使特征可能还相当新（存在时间不到 3 小时）。
| SignatureFreshness | pickList | 通用 | True | UpToDate | OneDay、ThreeDays、TwoDays、UpToDate | 反恶意软件特征的最大允许使用期限。 如果特征无法更新且早于此值，则生成任务将根据“特征使用期限验证方式”字段中的选定值进行操作。 注意：如果选择 **Up-To-Date**，则最多允许使用特征 3 个小时。
| TreatStaleSignatureAs | pickList | 通用 | True | 错误 | Error、Standard、Warning | 当特征使用期限早于所选的“反恶意软件特征使用期限”时使用的日志级别。 过期的特征可能会被视为 **Warning** 或 **Informational**，可以继续用于反恶意软件扫描，但不建议这样做。

## <a name="binskim-task"></a>BinSkim 任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| InputType | pickList | 通用 | True | 基本 | Basic、CommandLine | 
| 参数 | string | InputType = CommandLine | True |  |  | 要执行的标准 BinSkim 命令行参数。 输出路径会被删除并替换。<br>有关此工具的命令行参数的更多详细信息，请在 Arguments 字段中输入 **help** 并执行生成任务。
| 函数 | pickList | InputType = Basic | True | analyze | analyze、dump、exportConfig、exportRules | 
| AnalyzeTarget | filePath | InputType = Basic && Function = analyze | True | $(Build.ArtifactStagingDirectory)\*.dll;<br>$(Build.ArtifactStagingDirectory)\*.exe |  | 可解析为要分析的一个或多个二进制文件的文件、目录或筛选模式的一个或多个说明符。 （“;”分隔的列表）
| AnalyzeSymPath | string | InputType = Basic && Function = analyze | False |  |  | 目标的符号文件的路径。
| AnalyzeConfigPath | string | InputType = Basic && Function = analyze | False | 默认值 |  | 将要用来配置分析的策略文件的路径。 传递“default”值以使用内置设置。
| AnalyzePluginPath | string | InputType = Basic && Function = analyze | False |  |  | 将针对分析集中的所有目标调用的插件的路径。
| AnalyzeRecurse | boolean | InputType = Basic && Function = analyze | False | 是 |  | 在评估文件说明符参数时递归到子目录中。
| AnalyzeVerbose | boolean | InputType = Basic && Function = analyze | False | false |  | 发出详细输出。 生成的综合性报告旨在提供合规性方案的相应证据。
| AnalyzeHashes | boolean | InputType = Basic && Function = analyze | False | false |  | 发出 SARIF 报告时输出分析目标的 SHA-256 哈希。
| AnalyzeStatistics | boolean | InputType = Basic && Function = analyze | False | false |  | 为分析会话生成计时和其他统计信息。
| AnalyzeEnvironment | boolean | InputType = Basic && Function = analyze | False | false |  | 将运行的计算机环境详细信息记录到输出文件中。 警告：此选项会将可能的敏感信息（例如所有环境变量值）记录到发出的任何日志中。
| ExportRulesOutputType | pickList | InputType = Basic && Function = exportRules | False | SARIF | SARIF、SonarQube | 要输出的规则描述符文件的类型。 这将包含在由“发布安全分析日志”生成任务发布的 BinSkim 日志文件夹中。
| DumpTarget | filePath | InputType = Basic && Function = dump | True | $(Build.ArtifactStagingDirectory) |  | 可解析为要分析的一个或多个二进制文件的文件、目录或筛选模式的一个或多个说明符。 （“;”分隔的列表）
| DumpRecurse | boolean | InputType = Basic && Function = dump | False | 是 |  | 在评估文件说明符参数时递归到子目录中。
| DumpVerbose | boolean | InputType = Basic && Function = dump | False | 是 |  | 发出详细输出。 生成的综合性报告旨在提供合规性方案的相应证据。
| toolVersion | pickList | 通用 | False | 最新版本 | 1.5.0、Latest、LatestPreRelease | 要运行的工具的版本。

## <a name="credential-scanner-task"></a>凭据扫描程序任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| outputFormat | pickList | 通用 | False | pre | csv、pre、tsv | 凭据扫描程序结果文件的输出格式。
| toolVersion | pickList | 通用 | False | 最新版本 | 1.27.7、Latest、LatestPreRelease | 要运行的工具的版本。
| scanFolder | filePath | 通用 | False | $(Build.SourcesDirectory) |  | 存储库中要在其中扫描凭据的文件夹。
| searchersFileType | pickList | 通用 | False | 默认 | Custom、Default、DefaultAndCustom | 用于查找用于扫描的搜索器文件的选项。
| searchersFile | filePath | searchersFileType == Custom OR searchersFileType == DefaultAndCustom | False |  |  | 要执行的检查的凭据扫描程序搜索器配置文件。 通过提供凭据扫描程序搜索器文件路径的逗号分隔列表，可以包含和使用多个值。
| suppressionsFile | filePath | 通用 | False |  |  | 凭据扫描程序抑制文件，用于在输出日志中抑制问题。
| suppressAsError | boolean | 通用 | False | false |  | 被抑制的匹配项将输出到输出文件 [-O]-matches.[-f] 而非输出到默认抑制的输出文件 [-O]-suppressed.[-f]。 （默认值为“False”）
| verboseOutput | boolean | 通用 | False | false |  | 输出详细信息。
| batchSize | string | 通用 | False |  |  | 用于并行运行凭据扫描程序的并发线程数。 （默认值为 20）<br/>值必须位于 1-2147483647 范围内。
| regexMatchTimeoutInSeconds | string | 通用 | False |  |  | 在放弃检查之前，尝试搜索器匹配所花费的时间（以秒为单位）。<br/>将 ``-Co RegexMatchTimeoutInSeconds=<Value>`` 添加到命令行。
| fileScanReadBufferSize | string | 通用 | False |  |  | 读取内容时的缓冲区大小（以字节为单位）。 （默认值为 524288）<br/>将 ``-Co FileScanReadBufferSize=<Value>`` 添加到命令行。
| maxFileScanReadBytes | string | 通用 | False |  |  | 执行内容分析期间要从给定文件读取的最大字节数。 （默认值为 104857600）<br/>将 ``-Co MaxFileScanReadBytes=<Value>`` 添加到命令行。

## <a name="roslyn-analyzers-task"></a>Roslyn 分析器任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                   |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| userProvideBuildInfo | pickList | 通用 | True | auto | auto、msBuildInfo | 用户用来为 Roslyn 分析提供 MSBuild 版本、MSBuild 体系结构和生成命令行的选项。 如果选择了 **Auto**，则此任务会从同一管道中以前的 **MSBuild**、**VSBuild** 和/或 **.NET Core**（用于生成）任务中检索生成信息。
| msBuildVersion | pickList | userProvideBuildInfo == msBuildInfo | True | 16.0 | 15.0、16.0 | MSBuild 版本。
| msBuildArchitecture | pickList | userProvideBuildInfo == msBuildInfo | True | x86 | DotNetCore、x64、x86 | MSBuild 体系结构。 注意：如果生成命令行调用 **dotnet.exe "生成**"，请选择 " **通过 .net Core** " 选项。
| msBuildCommandline | string | userProvideBuildInfo == msBuildInfo | True |  |  | 用于编译你的解决方案或项目的完整生成命令行。<br/><br/>说明：此命令行应当以 **MSBuild.exe** 或 **dotnet.exe** 的完整路径开头。<br/>此命令在运行时将以 $(Build.SourcesDirectory) 作为工作目录。
| rulesetName | pickList | 通用 | False | 建议 | Custom、None、Recommended、Required | 要使用的已命名规则集。<br/><br/>如果选择了“`Ruleset Configured In Your Visual Studio Project File(s)`”，则会使用在 VS 项目文件中预先配置的规则集。 如果选择了“`Custom`”，则可以设置自定义规则集路径选项。
| rulesetVersion | pickList | rulesetName == Required OR rulesetName == Recommended | False | 最新版本 | 8.0、8.1、8.2、Latest、LatestPreRelease | 所选 SDL 规则集的版本。
| customRuleset | string | rulesetName = Custom | False |  |  | 要使用的规则集的可访问路径。 相对路径将规范化为源存储库的根 (`$(Build.SourcesDirectory)`)。<br/><br/>如果规则集指定了 `Rules` 并将 `Actions` 设置为 `Error`，则生成任务会失败。 若要使用这样做的规则集，请在生成任务的 `Control Options` 中选中 `Continue on error`。
| microsoftAnalyzersVersion | pickList | 通用 | False | 最新版本 | 2.9.3、2.9.4、2.9.6、Latest、LatestPreRelease | 要运行的 [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 包的版本。
| suppressionFileForCompilerWarnings | filePath | 通用 | False |  |  | 用于抑制 C# 和 VB 编译器警告的抑制文件。<br/><br/>一个列出了每个警告 ID 的纯文本文件，其中每个 ID 占用一个单独的行。<br/>对于编译器警告，请仅指定警告标识符的数字部分。 例如，1018 将抑制 CS1018，CA1501 将抑制 CA1501。<br/><br/>一个相对文件路径将追加到源存储库的根 (`$(Build.SourcesDirectory)`)。

## <a name="tslint-task"></a>TSLint 任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RuleLibrary | pickList | 通用 | True | tslint | custom、microsoft、tslint | 所有结果都包括所选 TSLint 版本附带的规则（**仅限基础**）。<br/><br/>**仅限基础 -** 仅限 TSLint 附带的规则。<br/><br/>**包括 Microsoft 规则 -** 下载 [tslint-microsoft-contrib](https://github.com/Microsoft/tslint-microsoft-contrib) 并包括其规则以供在 TSLint 运行中使用。 选择此选项将隐藏 `Type Checking` 复选框，因为该复选框是 Microsoft 的规则所需的，将会自动使用。 它还将取消隐藏 `Microsoft Contribution Version` 字段，从而允许选择 [npm](https://www.npmjs.com/package/tslint-microsoft-contrib) 中的 `tslint-microsoft-contrib` 的版本。<br/><br/>**包括自定义规则 -** 取消隐藏 `Rules Directory` 字段，该字段接受供在 TSLint 运行中使用的 TSLint 规则所在目录的可访问路径。<br/><br/>**注意：** 默认值已更改为 tslint，因为许多用户在配置 Microsoft 规则集时遇到问题。 有关特定的版本配置，请参阅 [GitHub 上的 tslint-microsoft-contrib](https://github.com/microsoft/tslint-microsoft-contrib)。
| RulesDirectory | string | RuleLibrary == custom | True |  |  | 包含可供在 TSLint 运行中使用的其他 TSLint 规则的可访问目录。
| Ruleset | pickList | RuleLibrary != microsoft | True | tsrecommended | custom、tslatest、tsrecommended | 定义要对 TypeScript 文件运行的规则。<br/><br/>**[tslint:latest](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) -** 扩展了 `tslint:recommended` 并不断更新以包括每个 TSLint 版本中的最新规则的配置。 使用此配置可能会在次版本之间引入中断性变更，因为启用了会导致在代码中出现 lint 失败的新规则。 当 TSLint 到达主版本更新时，`tslint:recommended` 将更新为与 `tslint:latest` 相同。<br/><br/>**[tslint:recommended](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) -** TSLint 推荐用于一般 TypeScript 编程的一组稳定的、有点“固执己见”的规则。 此配置遵循 `semver`，因此它在次版本或修补程序版本之间不会有中断性变更。
| RulesetMicrosoft | pickList | RuleLibrary == microsoft | True | mssdlrequired | custom、msrecommended、mssdlrecommended、mssdlrequired、tslatest、tsrecommended | 定义要对 TypeScript 文件运行的规则。<br/><br/>**[microsoft:sdl-required](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) -** 运行由满足必需的[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl/) 策略的 tslint 和 tslint-microsoft-contrib 规则提供的所有可用检查。<br/><br/>**[microsoft:sdl-recommended](https://github.com/Microsoft/tslint-microsoft-contrib/wiki/TSLint-and-the-Microsoft-Security-Development-Lifecycle) -** 运行由满足必需的和建议的[安全开发生命周期 (SDL)](https://www.microsoft.com/sdl/) 策略的 tslint 和 tslint-microsoft-contrib 规则提供的所有可用检查。<br/><br/>**microsoft:recommended** 由 tslint-microsoft-contrib 规则的创建者建议的所有检查。 这包括安全检查和非安全检查。<br/><br/>**[tslint:latest](https://github.com/palantir/tslint/blob/master/src/configs/latest.ts) -** 扩展了 `tslint:recommended` 并不断更新以包括每个 TSLint 版本中的最新规则的配置。 使用此配置可能会在次版本之间引入中断性变更，因为启用了会导致在代码中出现 lint 失败的新规则。 当 TSLint 到达主版本更新时，`tslint:recommended` 将更新为与 `tslint:latest` 相同。<br/><br/>**[tslint:recommended](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts) -** TSLint 推荐用于一般 TypeScript 编程的一组稳定的、有点“固执己见”的规则。 此配置遵循 `semver`，因此它在次版本或修补程序版本之间不会有中断性变更。
| RulesetFile | string | Ruleset == custom OR RulesetMicrosoft == custom | True |  |  | [配置文件](https://palantir.github.io/tslint/usage/cli/)指定要运行的规则。<br/><br/>配置的路径将添加为[自定义规则](https://palantir.github.io/tslint/develop/custom-rules/)的路径。
| FileSelectionType | pickList | 通用 | True | fileGlob | fileGlob、projectFile | 
| 文件 | string | FileSelectionType == fileGlob | True | **\*.ts |  | 一个文件 [glob](https://www.npmjs.com/package/glob)，它确定了要处理的文件。 路径相对于 `Build.SourcesDirectory` 值。<br/><br/>Microsoft 的贡献库要求使用项目文件。 如果你将 Microsoft 的贡献库与 `File Glob Pattern` 选项一起使用，则会为你生成一个项目文件。
| ECMAScriptVersion | pickList | FileSelectionType == fileGlob && RuleLibrary == microsoft | True | ES3 | ES2015、ES2016、ES2017、ES3、ES5、ES6、ESNext | 随你的 TypeScript 编译器配置的 ECMAScript 的目标版本。 使用项目文件时，这是 TypeScript tsconfig.json 文件的 compilerOptions.target 字段。
| Project | string | FileSelectionType == projectFile | True |  |  | 一个 [tsconfig.json](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html) 文件的路径，该文件用于指定要对其运行 TSLint 的 TypeScript 文件。 路径相对于 `Build.SourcesDirectory` 值。
| TypeCheck | boolean | RuleLibrary != microsoft && FileSelectionType == projectFile | False | 是 |  | 在运行 lint 分析规则时启用类型检查器。
| ExcludeFiles | string | 通用 | False |  |  | 一个 [glob](https://www.npmjs.com/package/glob)，它指示要从 Lint 分析中排除的文件。 路径相对于 `Build.SourcesDirectory` 值。 可以指定多个值，用分号分隔。
| OutputFormat | pickList | 通用 | True | json | checkstyle、codeFrame、filesList、json、msbuild、pmd、prose、stylish、verbose、vso | 用于生成输出的[格式化程序](https://palantir.github.io/tslint/formatters/)。 请注意，JSON 格式与事后分析兼容。
| NodeMemory | string | 通用 | False |  |  | 要分配给节点以运行 TSLint 的显式内存量 (MB)。 示例：8000<br/><br/>映射到节点的 `--max_old_space=<value>` CLI 选项，这是一个 `v8 option`。
| ToolVersion | pickList | RuleLibrary != microsoft | True | 最新 | 4.0.0、4.0.1、4.0.2、4.1.0、4.1.1、4.2.0、4.3.0、4.3.1、4.4.0、4.4.1、4.4.2、4.5.0、4.5.1、5.0.0、5.1.0、5.2.0、5.3.0、5.3.2、5.4.0、5.4.1、5.4.2、5.4.3、5.5.0、latest | 要下载和运行的 TSLint 的[版本](https://github.com/palantir/tslint/releases)。
| TypeScriptVersion | pickList | 通用 | True | 最新 | 0.8.0、0.8.1、0.8.2、0.8.3、0.9.0、0.9.1、0.9.5、0.9.7、1.0.0、1.0.1、1.3.0、1.4.1、1.5.3、1.6.2、1.7.3、1.7.5、1.8.0、1.8.10、1.8.2、1.8.5、1.8.6、1.8.7、1.8.9、1.9.0、2.0.0、2.0.10、2.0.2、2.0.3、2.0.6、2.0.7、2.0.8、2.0.9、2.1.1、2.1.4、2.1.5、2.1.6、2.2.0、2.2.1、custom、latest | 要下载和使用的 [typescript](https://www.npmjs.com/package/typescript) 的版本。<br/>**注意：** 这需要与用来编译代码的 TypeScript 版本相同。
| TypeScriptVersionCustom | string | TypeScriptVersion == custom | True | 最新 |  | 要下载和使用的 [typescript](https://www.npmjs.com/package/typescript) 的版本。<br/>**注意：** 这需要与用来编译代码的 TypeScript 版本相同。
| MicrosoftContribVersion | pickList | RuleLibrary == microsoft |  | 最新 | 4.0.0、4.0.1、5.0.0、5.0.1、latest | 要下载和使用的 [tslint-microsoft-contrib](https://www.npmjs.com/package/tslint-microsoft-contrib)（SDL 规则）的版本。</br>**注意：** 将选择与为 tslint-microsoft-contrib 选择的版本兼容的 [tslint](https://www.npmjs.com/package/tslint) 版本。 此生成任务将封闭对 tslint-microsoft-contrib 的更新，直到可以进行一段时间的测试。

## <a name="publish-security-analysis-logs-task"></a>“发布安全分析日志”任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ArtifactName | string | 通用 | True | CodeAnalysisLogs |  | 要创建的项目的名称。
| ArtifactType | pickList | 通用 | True | 容器 | Container、FilePath | 要创建的项目的类型。
| TargetPath | string | ArtifactType = FilePath | False | \\my\share\$(Build.DefinitionName)<br>\$(Build.BuildNumber) |  | 要将文件复制到的文件共享
| AllTools | boolean | 通用 | True | 是 |  | 发布由所有安全开发工具生成任务生成的结果。
| AntiMalware | boolean | AllTools = false | True | 是 |  | 发布由反恶意软件生成任务生成的结果。
| BinSkim | boolean | AllTools = false | True | 是 |  | 发布由 BinSkim 生成任务生成的结果。
| CredScan | boolean | AllTools = false | True | 是 |  | 发布由凭据扫描程序生成任务生成的结果。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 发布由 Roslyn 分析器生成任务生成的结果。
| TSLint | boolean | AllTools = false | True | 是 |  | 发布由 TSLint 生成任务生成的结果。 请注意，报告仅支持 JSON 格式的 TSLint 日志。 如果选择了其他格式，请相应地更新你的 TSLint 生成任务。
| ToolLogsNotFoundAction | picklist | 通用 | True | 标准 | Error、None、Standard、Warning | 找不到所选工具的日志（意味着该工具未运行）时要采取的操作，或者在选中“所有工具”的情况下找不到任意工具的日志时要采取的操作。<br/><br/>**选项：**<br/>**None：** 只有将 VSTS 变量 **system.debug** 设置为 **true** 时才会将消息写入到可访问的详细输出流中。<br/>**标准：** （默认值）写入一条标准输出消息，指出没有找到该工具的日志。<br/>**警告：** 写入一条黄色警告消息，指出没有找到该工具的日志，这将在生成摘要页上显示为警告。<br/>**错误：** 写入一条红色错误消息并引发异常，中断生成。 使用此选项可确保通过选择各个工具来确保运行哪些工具。

## <a name="security-report-task"></a>安全报告任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| VstsConsole | boolean | 通用 | False | 是 |  | 将结果写入管道控制台。
| TsvFile | boolean | 通用 | False | 是 |  | 生成一个 tsv 文件（制表符分隔的值），找到的每个结果对应一行，并使用制表符分隔结果信息。
| HtmlFile | boolean | 通用 | False | 是 |  | 生成 html 报告文件。
| AllTools | boolean | 通用 | True | false |  | 报告由所有安全开发工具生成任务生成的结果。
| BinSkim | boolean | AllTools = false | True | false |  | 报告由 BinSkim 生成任务生成的结果。
| BinSkimBreakOn | pickList | AllTools = true OR BinSkim = true | True | 错误 | Error、WarningAbove | 要报告的结果的级别。
| CredScan | boolean | AllTools = false | True | false |  | 报告由凭据扫描程序生成任务生成的结果。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 报告由 Roslyn 分析器生成任务生成的结果。
| RoslynAnalyzersBreakOn | pickList | AllTools = true OR RoslynAnalyzers = true | True | 错误 | Error、WarningAbove | 要报告的结果的级别。
| TSLint | boolean | AllTools = false | True | false |  | 报告由 TSLint 生成任务生成的结果。 请注意，报告仅支持 JSON 格式的 TSLint 日志。 如果选择了其他格式，请相应地更新你的 TSLint 生成任务。
| TSLintBreakOn | pickList | AllTools = true OR TSLint = true | True | 错误 | Error、WarningAbove | 要报告的结果的级别。
| ToolLogsNotFoundAction | picklist | 通用 | True | 标准 | Error、None、Standard、Warning | 找不到所选工具的日志（意味着该工具未运行）时要采取的操作，或者在选中“所有工具”的情况下找不到任意工具的日志时要采取的操作。<br/><br/>**选项：**<br/>**None：** 只有将 VSTS 变量 **system.debug** 设置为 **true** 时才会将消息写入到可访问的详细输出流中。<br/>**标准：** （默认值）写入一条标准输出消息，指出没有找到该工具的日志。<br/>**警告：** 写入一条黄色警告消息，指出没有找到该工具的日志，这将在生成摘要页上显示为警告。<br/>**错误：** 写入一条红色错误消息并引发异常，中断生成。 使用此选项可确保通过选择各个工具来确保运行哪些工具。
| CustomLogsFolder | string | 通用 | False |  |  | 分析工具日志所在的基文件夹；各个日志文件将位于此路径下按每个工具命名的子文件夹中。

## <a name="post-analysis-task"></a>事后分析任务

| **InputType**      | **类型**     | **适用于**            | **必需** | **默认值**             | **选项（针对 pickList）**                                   | **说明**                                                                                                                                                                                                                                                                                                                            |
|------------|---------------|-----------------------|----------|---------------------------|----------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AllTools | boolean | 通用 | True | false |  | 如果有任何 Microsoft 安全代码分析生成任务发现任何问题，则中断生成。
| BinSkim | boolean | AllTools = false | True | false |  | 如果发现任何 BinSkim 问题，则根据你选择的“中断条件”选项中断生成。
| BinSkimBreakOn | pickList | AllTools = true OR BinSkim = true | True | 错误 | Error、WarningAbove | 将中断生成的问题的级别。
| CredScan | boolean | AllTools = false | True | false |  | 如果发现任何凭据扫描程序问题，则中断生成。
| RoslynAnalyzers | boolean | AllTools = false | True | false |  | 如果发现任何 Roslyn 分析器问题，则中断生成。
| RoslynAnalyzersBreakOn | pickList | AllTools = true OR RoslynAnalyzers = true | True | 错误 | Error、WarningAbove | 将中断生成的问题的级别。
| TSLint | boolean | AllTools = false | True | false |  | 如果发现任何 TSLint 问题，则中断生成。 请注意，事后分析仅支持 JSON 格式的 TSLint 日志。 如果选择了其他格式，请相应地更新你的 TSLint 生成任务。
| TSLintBreakOn | pickList | AllTools = true OR TSLint = true | True | 错误 | Error、WarningAbove | 将中断生成的问题的级别。
| VstsConsole | boolean | 通用 | False | 是 |  | 将结果写入管道控制台。
| ToolLogsNotFoundAction | picklist | 通用 | True | 标准 | Error、None、Standard、Warning | 找不到所选工具的日志（意味着该工具未运行）时要采取的操作，或者在选中“所有工具”的情况下找不到任意工具的日志时要采取的操作。<br/><br/>**选项：**<br/>**None：** 只有将 VSTS 变量 **system.debug** 设置为 **true** 时才会将消息写入到可访问的详细输出流中。<br/>**标准：** （默认值）写入一条标准输出消息，指出没有找到该工具的日志。<br/>**警告：** 写入一条黄色警告消息，指出没有找到该工具的日志，这将在生成摘要页上显示为警告。<br/>**错误：** 写入一条红色错误消息并引发异常，中断生成。 使用此选项可确保通过选择各个工具来确保运行哪些工具。

## <a name="next-steps"></a>后续步骤

对于安全代码分析扩展和所提供的工具，如果仍有疑问，请查看我们的[常见问题解答页](security-code-analysis-faq.md)。
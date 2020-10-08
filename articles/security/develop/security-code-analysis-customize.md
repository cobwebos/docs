---
title: 自定义 Microsoft 安全代码分析任务
titleSuffix: Azure
description: 本文介绍如何在 Microsoft 安全代码分析扩展中自定义任务
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: b05084a7d01f4c5d5d5a79b60ac0b8ba47843622
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91816792"
---
# <a name="configure-and-customize-the-build-tasks"></a>配置和自定义生成任务

本文详细介绍每个生成任务中可用的配置选项。 本文首先介绍了与安全代码分析工具相关的任务， 最后介绍了处理后任务。

## <a name="anti-malware-scanner-task"></a>反恶意软件扫描程序任务

>[!NOTE]
> 反恶意软件扫描程序生成任务需要启用了 Windows Defender 的生成代理。 托管的 Visual Studio 2017 和更高版本提供了这样的代理。 生成任务不会在 Visual Studio 2015 托管代理上运行。
>
> 尽管不能在这些代理上更新特征，但特征的使用期限应始终小于三个小时。

以下屏幕截图和文本显示任务配置的详细信息。

![配置反恶意软件扫描程序生成任务](./media/security-tools/5-add-anti-malware-task600.png)

 在屏幕截图的“类型”列表框中选择“基本”。 选择“自定义”以提供命令行参数来自定义扫描。

Windows Defender 使用 Windows 更新客户端来下载和安装签名。 如果生成代理上的签名更新失败，Windows 更新可能发送 HRESULT 错误代码。

有关 Windows 更新错误及其缓解措施的详细信息，请参阅[按组件列出的 Windows 更新错误代码](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)和 TechNet 文章 [Windows 更新代理 - 错误代码](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)。

有关此任务的 YAML 配置的信息，请查看[反恶意软件 YAML 选项](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim 任务

> [!NOTE]
> 生成必须满足以下条件之一，才能运行 BinSkim 任务：
>  - 生成从托管代码产生二进制项目。
>  - 已提交要使用 BinSkim 进行分析的二进制项目。

以下屏幕截图和列表显示任务配置的详细信息。

![配置 BinSkim 生成任务](./media/security-tools/7-binskim-task-details.png)

- 将生成配置设置为“调试”，以便生成 .pdb 调试文件。 BinSkim 使用这些文件将输出二进制文件中的问题映射回源代码。
- 若要避免研究和创建自己的命令行，请执行以下操作：
     - 在“类型”列表中，选择“基本”。 
     -  在“函数”列表中，选择“分析”。
- 在“目标”中为文件、目录或筛选器模式输入一个或多个说明符。 这些说明符解析为要分析的一个或多个二进制文件：
    - 多个指定的目标必须用分号 (;) 分隔。
    - 说明符可以是单个文件，也可以包含通配符。
    - 目录规范必须始终以 \\* 结尾。
    - 示例：

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

-  如果在“类型”列表中选择“命令行”，则需要运行 binskim.exe：
     - 确保 binskim.exe 的第一个参数是谓词 analyze 后跟一个或多个路径规范。 每个路径可以是完整路径，也可以是相对于源目录的路径。
     - 多个目标路径必须用空格分隔。
     -  可以省略 /o 或 /output 选项。 将为你添加输出值或替换它。
     - 标准命令行配置如下所示。

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> 如果为目标指定目录，则结尾的 \\* 非常重要。

有关 BinSkim 命令行参数、规则（按 ID）或退出代码的详细信息，请参阅 [BinSkim 用户指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)。

有关此任务的 YAML 配置的信息，请查看 [BinSkim YAML 选项](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>凭据扫描程序任务

以下屏幕截图和列表显示任务配置的详细信息。

![配置凭据扫描程序生成任务](./media/security-tools/3-taskdetails.png)

可用选项包括：
  - **显示名称**：Azure DevOps 任务的名称。 默认值为“运行凭据扫描程序”
  - **工具主版本**： 可用值包括 CredScan V2 和 CredScan V1。 建议客户使用 CredScan V2 版本。
  - **输出格式**：   可用值包括 TSV、CSV、SARIF 和 PREfast。
  - **工具版本**：建议选择“最新”。
  - **扫描文件夹**：要扫描的存储库文件夹。
  - **搜索器文件类型**：用来查找用于扫描的搜索器文件的选项。
  - **禁止显示文件**：[JSON](https://json.org/) 文件可以禁止显示输出日志中的问题。 有关禁止显示方案的详细信息，请参阅本文的常见问题解答部分。
  - **详细输出**：一目了然。
  - **批大小**：用于运行凭据扫描程序的并发线程数。 默认值为 20。 可能的值介于 1 到 2,147,483,647 之间。
  - **匹配超时**：在放弃检查之前，尝试搜索器匹配所花费的时间（以秒为单位）。
  - **文件扫描读取缓冲区大小**：读取内容时使用的缓冲区大小（以字节为单位）。 默认值为 524,288。  
  - **最大文件扫描读取字节数**：内容分析期间从文件读取的最大字节数。 默认值为 104,857,600。
  - **控制选项** > **运行此任务**：指定运行任务的时间。 选择“自定义条件”以指定更复杂的条件。
  - **版本**：Azure DevOps 中的生成任务版本。 此选项不常用。

有关此任务的 YAML 配置的信息，请查看[凭据扫描程序选项](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>Roslyn 分析器任务

> [!NOTE]
> 生成需要满足以下条件，才能运行 Roslyn 分析器任务：
>
> - 生成定义包含内置的 MSBuild 或 VSBuild 生成任务以编译 C# 或 Visual Basic 代码。 分析器任务依赖于内置任务的输入和输出来运行启用了 Roslyn 分析器的 MSBuild 编译。
> - 运行此生成任务的生成代理安装了 Visual Studio 2017 15.5 或更高版本，因此它使用编译器 2.6 或更高版本。

下面的列表和说明显示任务配置的详细信息。

可用选项包括：

- **规则集**： 值是 SDL 要求的、SDL 推荐的或你自己的自定义规则集。
- **分析器版本**：建议选择“最新”。
- **编译器警告禁止显示文件**：禁止显示带有警告 ID 列表的文本文件。
- **控制选项** > **运行此任务**：指定运行任务的时间。 选择“自定义条件”以指定更复杂的条件。

> [!NOTE]
>
> - Roslyn 分析器与编译器集成，只能作为 csc.exe 编译的一部分运行。 因此，此任务需要重新启用或再次运行生成中之前运行的编译器命令。 此重播或运行是通过查询 Azure DevOps (以前 Visual Studio Team Services MSBuild 生成任务日志的) 来完成的。
>
>   任务从生成定义可靠获取 MSBuild 编译命令行没有其他途径。 我们考虑了添加自由格式文本框，使用户能够输入其命令行。 但这样做很难使这些命令行保持最新并与主生成同步。
>
>   自定义的生成需要重新运行整个命令集，而不只是编译器命令。 这些情况下，启用 Roslyn 分析器并不简单或可靠。
>
> - Roslyn 分析器与编译器集成。 Roslyn 分析器需要编译才可以进行调用。
>
>   通过重新编译已构建的 C# 项目来实现此新生成任务。 新任务仅使用与原始任务相同的生成或生成定义中的 MSBuild 和 VSBuild 生成任务。 但在这种情况下，新任务在启用了 Roslyn 分析器的情况下使用它们。
>
>   如果新任务在与原始任务相同的代理上运行，新任务的输出将覆盖 s 源文件夹中的原始任务输出。 尽管生成输出是相同的，我们建议运行 MSBuild，将输出复制到项目分段目录，然后运行 Roslyn 分析器。

有关 Roslyn 分析器任务的其他资源，请参阅 Microsoft Docs 上的[基于 Roslyn 的分析器](https://docs.microsoft.com/dotnet/standard/analyzers/api-analyzer)。

可以在 NuGet 页 [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) 上找到此生成任务安装并使用的分析器包。

有关此任务的 YAML 配置的信息，请查看 [Roslyn 分析器 YAML 选项](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint 任务

有关 TSLint 的详细信息，请转到 [TSLint GitHub 存储库](https://github.com/palantir/tslint)。

>[!NOTE] 
>你可能知道，[TSLint GitHub 存储库](https://github.com/palantir/tslint)主页显示将在 2019 年的某个时间弃用 TSLint。 Microsoft 正在研究 [ESLint](https://github.com/eslint/eslint) 作为备用任务。

有关此任务的 YAML 配置的信息，请查看 [TSLint YAML 选项](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>“发布安全分析日志”任务

以下屏幕截图和列表显示任务配置的详细信息。

![配置“发布安全分析日志”生成任务](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **项目名称**：任何字符串标识符。
- **项目类型**：根据所做的选择，可以将日志发布到 Azure DevOps Server 或生成代理可以访问的共享文件。
- **工具**：可以选择保留特定工具的日志，也可以选择“所有工具”保留所有日志。

有关此任务的 YAML 配置的信息，请查看[发布安全日志 YAML 选项](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>安全报告任务

以下屏幕截图和列表显示安全性报表配置的详细信息。

![配置安全性报表生成任务](./media/security-tools/4-createsecurityanalysisreport600.png)

- **报表**：  选择“管道控制台”、“TSV 文件”和“Html 文件”格式中的任何一种。 为每个选定格式创建一个报表文件。
- **工具**：选择生成定义中需要其提供已检测问题摘要的工具。 对于所选的每个工具，可能提供选项用于选择是仅查看错误还是查看摘要报表中的错误和警告。
- **高级选项**：如果选择的某个工具没有日志，可以选择记录警告或错误。 如果记录错误，任务将失败。
- **基本日志文件夹**：可以自定义日志所在的基本日志文件夹。 但通常不会使用此选项。

有关此任务的 YAML 配置的信息，请查看[安全性报表 YAML 选项](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>事后分析任务

以下屏幕截图和列表显示任务配置的详细信息。

![配置后期分析生成任务](./media/security-tools/a-post-analysis600.png)

- **工具**：选择生成定义中要为其有条件地注入生成中断的工具。 对于所选的每个工具，可能提供选项用于选择是要仅在错误时中断还是同时出现错误和警告时中断。
- **报表**：可以根据需要写入导致生成中断的结果。 结果将写入 Azure DevOps 控制台窗口和日志文件。
- **高级选项**：如果选择的某个工具没有日志，可以选择记录警告或错误。 如果记录错误，任务将失败。

有关此任务的 YAML 配置的信息，请查看[后期分析 YAML 选项](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>后续步骤

有关基于 YAML 的配置的信息，请参阅 [YAML 配置指南](yaml-configuration.md)。

对于安全代码分析扩展和所提供的工具，如果仍有疑问，请查看我们的[常见问题解答页](security-code-analysis-faq.md)。

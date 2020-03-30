---
title: 自定义 Microsoft 安全代码分析任务
titleSuffix: Azure
description: 本文介绍在 Microsoft 安全代码分析扩展中自定义任务
author: vharindra
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
ms.openlocfilehash: 6cdf892651407defc21f359a8e3b326b4af63b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77499990"
---
# <a name="configure-and-customize-the-build-tasks"></a>配置和自定义生成任务

本文详细介绍了每个生成任务中可用的配置选项。 本文从安全代码分析工具的任务开始。 它以后处理任务结束。

## <a name="anti-malware-scanner-task"></a>反恶意软件扫描器任务

>[!NOTE]
> 反恶意软件扫描仪生成任务需要启用 Windows Defender 的生成代理。 托管视觉工作室 2017 和更高版本提供这样的代理。 生成任务不会在 Visual Studio 2015 托管代理上运行。
>
> 尽管无法在这些代理上更新签名，但签名应始终小于三个小时。

任务配置的详细信息显示在以下屏幕截图和文本中。

![配置反恶意软件扫描仪生成任务](./media/security-tools/5-add-anti-malware-task600.png)

在屏幕截图的 **"类型"** 列表框中，选择了 **"基本**"。 选择 **"自定义"** 以提供自定义扫描的命令行参数。

Windows Defender 使用 Windows 更新客户端下载并安装签名。 如果生成代理上的签名更新失败，则**HRESULT**错误代码可能来自 Windows 更新。

有关 Windows 更新错误及其缓解的详细信息，请参阅[按组件更新错误代码的 Windows 更新代码](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)和 TechNet 文章[Windows 更新代理 - 错误代码](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)。

有关此任务的 YAML 配置的信息，请查看我们的[反恶意软件 YAML 选项](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>宾斯基任务

> [!NOTE]
> 在运行 BinSkim 任务之前，生成必须满足以下条件之一：
>  - 生成从托管代码生成二进制项目。
>  - 您已承诺要使用 BinSkim 分析的二进制工件。

任务配置的详细信息显示在以下屏幕截图和列表中。

![配置 BinSkim 生成任务](./media/security-tools/7-binskim-task-details.png)

- 将生成配置设置为调试，以便生成 .pdb 调试文件。 BinSkim 使用这些文件将输出二进制文件中的问题映射回源代码。
- 为了避免研究和创建自己的命令行：
     - 在 **"类型"** 列表中，选择 **"基本**"。
     - 在 **"函数**"列表中，选择 **"分析**"。
- 在 **"目标"** 中，为文件、目录或筛选器模式输入一个或多个指定值。 这些指定者解析为一个或多个要分析的二进制文件：
    - 多个指定的目标必须用分号分隔（;)。
    - 指定器可以是单个文件或包含通配符。
    - 目录规范必须始终以 *\\结尾。
    - 示例：

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- 如果在 **"类型"** 列表中选择**命令行**，则需要运行 binskim.exe：
     - 确保 binskim.exe 的第一个参数是动词**分析**，后跟一个或多个路径规范。 每个路径可以是完整路径，也可以是相对于源目录的路径。
     - 多个目标路径必须由空格分隔。
     - 您可以省略 **/o**或 **/输出**选项。 为您添加或替换输出值。
     - 标准命令行配置如下所示。

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > 如果为\\目标指定目录，则尾随 * 非常重要。

有关 BinSkim 命令行参数、按 ID 规则或退出代码的详细信息，请参阅[BinSkim 用户指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)。

有关此任务的 YAML 配置的信息，请查看我们的[BinSkim YAML 选项](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>凭据扫描程序任务

任务配置的详细信息显示在以下屏幕截图和列表中。

![配置凭据扫描程序生成任务](./media/security-tools/3-taskdetails.png)

可用选项包括：

  - **输出格式**：可用值包括 TSV、CSV、SARIF 和**PREfast**。 **TSV** **CSV** **SARIF**
  - **工具版本**：我们建议您选择 **"最新**"。
  - **扫描文件夹**：要扫描的存储库文件夹。
  - **搜索器文件类型**：用于查找用于扫描的搜索者文件的选项。
  - **抑制文件** [：JSON](https://json.org/)文件可以抑制输出日志中的问题。 有关抑制方案的详细信息，请参阅本文的常见问题部分。
  - **详细输出**：不言自明。
  - **批处理大小**：用于运行凭据扫描程序的并发线程数。 默认值为 20。 可能的值范围为 1 到 2，147，483，647。
  - **匹配超时**：在放弃检查之前尝试搜索器匹配所花费的时间（以秒为单位）。
  - **文件扫描读取缓冲区大小**：读取内容时使用的缓冲区的大小（ 以字节为单位）。 默认值为 524，288。  
  - **最大文件扫描读取字节**：内容分析期间从文件读取的最大字节数。 默认值为 104，857，600。
  - **控件选项** > **运行此任务**：指定任务何时运行。 选择 **"自定义条件"** 以指定更复杂的条件。
  - **版本**：Azure DevOps 中的生成任务版本。 此选项不经常使用。

有关此任务的 YAML 配置的信息，请查看我们的[凭据扫描仪 YAML 选项](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>微软安全风险检测任务

> [!NOTE]
> 在使用 MSRD 任务之前，您必须使用 Microsoft 安全风险检测 （MSRD） 服务创建和配置帐户。 此服务需要单独的载入流程。 与此扩展中的大多数其他任务不同，此任务需要使用 MSRD 进行单独的订阅。
>
> 请参阅[Microsoft 安全风险检测](https://aka.ms/msrddocs)和[Microsoft 安全风险检测：如何](https://docs.microsoft.com/security-risk-detection/how-to/)获得说明。

配置此任务的详细信息将显示在以下列表中。 对于任何 UI 元素，都可以将鼠标悬停在该元素上以获取有关帮助。

   - **MSRD 的 Azure DevOps 服务终结点名称**：Azure DevOps 服务终结点的泛型类型存储板载的 MSRD 实例 URL 和 REST API 访问令牌。 如果已创建此类终结点，可以在此处指定它。 否则，选择 **"管理**"链接可为此 MSRD 任务创建和配置新的服务终结点。
   - **帐户 ID**：可以从 MSRD 帐户 URL 检索的 GUID。
   - **到二进制的 URL**：公开可用的 URL 的分号分隔列表。 模糊计算机使用这些 URL 下载二进制文件。
   - **种子文件的 URL：** 公开可用的 URL 的分号分隔列表。 模糊机使用这些 URL 下载种子。 如果将种子文件与二进制文件一起下载，则指定此值是可选的。
   - **OS 平台类型**：运行模糊作业的计算机的操作系统 （OS） 平台。 可用值是**Windows**和**Linux**。
   - **Windows 版本 / Linux 版本**： 运行模糊作业的计算机的操作系统版本。 如果计算机具有不同的操作系统版本，则可以覆盖默认值。
   - **包安装脚本**：要在测试计算机上运行的脚本。 此脚本在提交模糊作业之前安装测试目标程序及其依赖项。
   - **作业提交参数**：
       - **种子目录**：包含种子的模糊计算机上的目录的路径。
       - **种子扩展名**：种子的文件名扩展名。
       - **测试驱动程序可执行性**：模糊计算机上目标可执行文件的路径。
       - **测试驱动程序可执行体系结构**：目标可执行文件的体系结构。 可用值为**x86**和**amd64**。
       - **测试驱动程序参数**：传递给测试可执行文件的命令行参数。 参数"%testfile%"（包括引号）将自动替换为目标文件的完整路径。 此文件由测试驱动程序解析，并且是必需的。
       - **测试驱动程序在测试完成后退出**：如果测试驱动程序要在完成后终止，请选择此复选框。 如果测试驱动程序需要强制关闭，则清除它。
       - **最大持续时间（以秒为单位）：** 估计目标程序解析输入文件所需的最长合理预期时间。 估计越准确，模糊应用运行的效率就越高。
       - **测试驱动程序可以重复运行**：如果测试驱动程序可以重复运行而不依赖于持久性或共享的全局状态，请选择此复选框。
       - **测试驱动程序可以重命名**：如果测试驱动程序可执行文件可以重命名并且仍然正常工作，请选择此复选框。
       - **模糊应用程序作为单个操作系统进程运行**：如果测试驱动程序在单个操作系统进程下运行，请选择此复选框。 如果测试驱动程序生成其他进程，则清除它。

有关此任务的 YAML 配置的信息，请查看我们的[Microsoft 安全风险检测 YAML 选项](yaml-configuration.md#microsoft-security-risk-detection-task)

## <a name="roslyn-analyzers-task"></a>罗斯林分析器任务

> [!NOTE]
> 在运行 Roslyn 分析器任务之前，您的生成需要满足以下条件：
>
> - 生成定义包括内置的 MSBuild 或 VSBuild 生成任务，用于编译 C# 或可视化基本代码。 分析器任务依赖于内置任务的输入和输出来运行 MSBuild 编译，并启用了 Roslyn 分析器。
> - 运行此生成任务的生成代理安装了 Visual Studio 2017 版本 15.5 或更高版本，因此它使用编译器版本 2.6 或更高版本。

任务配置的详细信息显示在以下列表和注释中。

可用选项包括：

- **规则集**：值为**SDL 必需**值 **、SDL 建议**值或您自己的自定义规则集。
- **分析器版本**：我们建议您选择 **"最新**"。
- **编译器警告抑制文件**：包含被抑制的警告 ID 列表的文本文件。
- **控件选项** > **运行此任务**：指定任务何时运行。 选择 **"自定义条件"** 以指定更复杂的条件。

> [!NOTE]
>
> - Roslyn 分析器与编译器集成，只能作为 csc.exe 编译的一部分运行。 因此，此任务要求在生成中较早运行的编译器命令重新播放或重新运行。 此重播或运行是通过查询 MSBuild 生成任务日志的可视化工作室团队服务 （VSTS） 来完成的。
>
>   任务没有其他方法可以可靠地从生成定义中获取 MSBuild 编译命令行。 我们考虑添加一个自由格式文本框，以允许用户输入其命令行。 但是，很难使这些命令行保持最新且与主生成同步。
>
>   自定义生成需要重播整个命令集，而不仅仅是编译器命令。 在这些情况下，启用 Roslyn 分析仪并非微不足道或可靠。
>
> - 罗斯林分析器与编译器集成。 要调用 Roslyn 分析器需要编译。
>
>   此新的生成任务是通过重新编译已生成的 C# 项目实现的。 新任务仅使用与原始任务相同的生成或生成定义中的 MSBuild 和 VSBuild 生成任务。 但是，在这种情况下，新任务在启用了 Roslyn 分析器的情况下使用它们。
>
>   如果新任务在与原始任务相同的代理上运行，则新任务的输出将覆盖原始*任务的源文件夹中*的输出。 尽管生成输出相同，但我们建议您运行 MSBuild，将输出复制到工件暂存目录，然后运行 Roslyn 分析器。

有关 Roslyn 分析器任务的其他资源，请查看 Microsoft 文档上的[基于 Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/)的分析器。

您可以在 NuGet 页面 Microsoft 上找到此生成任务安装和使用的分析器包[。](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)

有关此任务的 YAML 配置的信息，请查看我们的[罗斯林分析仪 YAML 选项](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint 任务

有关 TSLint 的更多信息，请访问[TSLint GitHub 存储库](https://github.com/palantir/tslint)。

>[!NOTE] 
>您可能知道[，TSLint GitHub 存储库](https://github.com/palantir/tslint)主页表示，TSLint 将在 2019 年某个时候被弃用。 微软正在调查[ESLint](https://github.com/eslint/eslint)作为替代任务。

有关此任务的 YAML 配置的信息，请查看我们的[TSLint YAML 选项](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>发布安全分析日志任务

任务配置的详细信息显示在以下屏幕截图和列表中。

![配置发布安全分析日志生成任务](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **项目名称**：任何字符串标识符。
- **项目类型**：根据您的选择，您可以将日志发布到 Azure DevOps 服务器或生成代理可访问的共享文件。
- **工具**：您可以选择保留特定工具的日志，也可以选择 **"所有工具**"来保留所有日志。

有关此任务的 YAML 配置的信息，请查看我们的[发布安全日志 YAML 选项](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>安全报告任务

安全报告配置的详细信息显示在以下屏幕截图和列表中。

![配置安全报告生成任务](./media/security-tools/4-createsecurityanalysisreport600.png)

- **报表**： 选择任何**管道控制台****、TSV 文件和** **Html 文件**格式。 为每个选定的格式创建一个报表文件。
- **工具**：选择生成定义中需要对检测到的问题进行摘要的工具。 对于所选的每个工具，可能有一个选项可以选择是只看到错误还是在摘要报告中同时看到错误和警告。
- **高级选项**：如果所选工具之一没有日志，您可以选择记录警告或错误。 如果记录错误，任务将失败。
- **基本日志文件夹**：您可以自定义要找到日志的基本日志文件夹。 但通常不使用此选项。

有关此任务的 YAML 配置的信息，请查看我们的[安全报告 YAML 选项](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>分析后任务

任务配置的详细信息显示在以下屏幕截图和列表中。

![配置分析后生成任务](./media/security-tools/a-post-analysis600.png)

- **工具**：选择生成定义中要有条件地注入生成中断的工具。 对于所选的每个工具，可能有一个选项可以选择是仅在错误上中断，还是同时选择错误和警告。
- **报表**：您可以选择编写导致生成中断的结果。 结果将写入 Azure DevOps 控制台窗口和日志文件。
- **高级选项**：如果所选工具之一没有日志，您可以选择记录警告或错误。 如果记录错误，任务将失败。

有关此任务的 YAML 配置的信息，请查看我们的[后分析 YAML 选项](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>后续步骤

有关基于 YAML 的配置的信息，请参阅我们的[YAML 配置指南](yaml-configuration.md)。

如果您对安全代码分析扩展和所提供的工具有任何疑问，请查看[我们的常见问题页面](security-code-analysis-faq.md)。

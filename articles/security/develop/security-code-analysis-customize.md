---
title: 自定义 Microsoft 安全代码分析任务
titleSuffix: Azure
description: 本文介绍如何自定义 Microsoft 安全代码分析扩展中的任务
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
ms.openlocfilehash: 8d074c12f28abdc61f4d70356c2a7aa264deb44c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871900"
---
# <a name="configure-and-customize-the-build-tasks"></a>配置和自定义生成任务

本文详细介绍了每个生成任务中可用的配置选项。 本文首先介绍安全代码分析工具的任务。 它以后续处理任务结束。

## <a name="anti-malware-scanner-task"></a>反恶意软件扫描器任务

>[!NOTE]
> 反恶意软件扫描程序生成任务需要启用了 Windows Defender 的生成代理。 托管的 Visual Studio 2017 和更高版本提供此类代理。 生成任务不会在 Visual Studio 2015 托管代理上运行。
>
> 尽管不能在这些代理上更新签名，但签名应始终小于三个小时。

以下屏幕截图和文本中显示了任务配置的详细信息。

![配置反恶意软件扫描程序生成任务](./media/security-tools/5-add-anti-malware-task600.png)

在屏幕截图的 "**类型**" 列表框中，选择 "**基本**"。 选择 "**自定义**" 可提供自定义扫描的命令行参数。

Windows Defender 使用 Windows 更新客户端下载和安装签名。 如果生成代理上的签名更新失败，则**HRESULT**错误代码可能来自 Windows 更新。

有关 Windows 更新错误和缓解措施的详细信息，请参阅[按组件 Windows 更新错误代码](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)和 TechNet 文章[Windows 更新代理-错误代码](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)。

有关此任务的 YAML 配置的信息，请查看我们的[反恶意软件 YAML 选项](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim 任务

> [!NOTE]
> 你的生成必须满足以下条件之一，然后才能运行 BinSkim 任务：
>  - 您的生成从托管代码生成二进制项目。
>  - 已提交要使用 BinSkim 进行分析的二进制项目。

以下屏幕截图和列表中显示了任务配置的详细信息。

![配置 BinSkim 生成任务](./media/security-tools/7-binskim-task-details.png)

- 将生成配置设置为 "调试"，以便生成 .pdb 调试文件。 BinSkim 使用这些文件将输出二进制文件中的问题映射回源代码。
- 若要避免研究和创建自己的命令行：
     - 在 "**类型**" 列表中，选择 "**基本**"。
     - 在**函数**列表中，选择 "**分析**"。
- 在 "**目标**" 中，为文件、目录或筛选器模式输入一个或多个说明符。 这些说明符解析为要分析的一个或多个二进制文件：
    - 多个指定的目标必须用分号（;) 分隔。
    - 说明符可以是单个文件，也可以包含通配符。
    - 目录规范必须始终以\\* 结尾。
    - 示例:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- 如果在 "**类型**" 列表中选择 "**命令行**"，则需要运行 binskim：
     - 请确保 binskim 的第一个参数是谓词**分析**后跟一个或多个路径规范。 每个路径可以是完整路径，也可以是相对于源目录的路径。
     - 多个目标路径必须用空格分隔。
     - 可以省略 **/o**或 **/output**选项。 为您添加或替换的输出值。
     - 标准命令行配置如下所示。

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > 如果为\\目标指定目录，则尾随 * 非常重要。

有关 BinSkim 命令行参数、按 ID 或退出代码的规则的详细信息，请参阅[BinSkim 用户指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)。

有关此任务的 YAML 配置的信息，请查看[BINSKIM YAML 选项](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>凭据扫描器任务

以下屏幕截图和列表中显示了任务配置的详细信息。

![配置凭据扫描器生成任务](./media/security-tools/3-taskdetails.png)

可用选项包括：
  - **显示名称**： Azure DevOps 任务的名称。 默认值为 "运行凭据扫描器"
  - **工具主要版本**：可用值包括**CredScan V2**、 **CredScan V1**。 建议客户使用**CredScan V2**版本。
  - **输出格式**：可用值包括**TSV**、 **CSV**、 **SARIF**和**PREfast**。
  - **工具版本**：建议选择 "**最新**"。
  - **Scan 文件夹**：要扫描的存储库文件夹。
  - **Searchers 文件类型**：用于查找用于扫描的 Searchers 文件的选项。
  - **禁止显示文件**： [JSON](https://json.org/)文件可以在输出日志中取消显示问题。 有关抑制方案的详细信息，请参阅本文的 "常见问题解答" 部分。
  - **详细输出**：一目了然。
  - **批大小**：用于运行凭据扫描程序的并发线程数。 默认值为 20。 可能的值介于1到2147483647之间。
  - **匹配超时**：在放弃检查之前，尝试搜索者匹配所用的时间（以秒为单位）。
  - **文件扫描读取缓冲区大小**：读取内容时使用的缓冲区大小（以字节为单位）。 默认值为524288。  
  - **最大文件扫描读取字节**数：内容分析期间要从文件中读取的最大字节数。 默认值为104857600。
  - **控制选项** > **运行此任务**：指定运行任务的时间。 选择 "**自定义条件**" 指定更复杂的条件。
  - **版本**： Azure DevOps 中的生成任务版本。 此选项不经常使用。

有关此任务的 YAML 配置的信息，请查看[凭据扫描器 YAML 选项](yaml-configuration.md#credential-scanner-task)

## <a name="microsoft-security-risk-detection-task"></a>Microsoft 安全风险检测任务

> [!NOTE]
> 使用 MSRD 任务之前，必须创建和配置具有 Microsoft 安全风险检测（MSRD）服务的帐户。 此服务需要单独的载入过程。 与此扩展中的大多数其他任务不同，此任务需要单独的订阅和 MSRD。
>
> 请参阅[Microsoft 安全风险检测](https://aka.ms/msrddocs)和[Microsoft 安全风险检测：如何](https://docs.microsoft.com/security-risk-detection/how-to/)了解相关说明。

以下列表显示了配置此任务的详细信息。 对于任何 UI 元素，您可以将鼠标悬停在该元素上以获得帮助。

   - **适用于 MSRD 的 Azure DevOps 服务终结点名称**： azure DevOps 服务终结点的泛型类型存储载入 MSRD 实例 URL 和 REST API 访问令牌。 如果已创建此类终结点，则可以在此处指定。 否则，请选择 "**管理**" 链接，为此 MSRD 任务创建和配置新的服务终结点。
   - **帐户 ID**：可以从 MSRD 帐户 URL 检索的 GUID。
   - **二进制文件的 url**：以分号分隔的公开可用 url 列表。 模糊化计算机使用这些 Url 下载二进制文件。
   - **Seed 文件的 url**：以分号分隔的公开可用 url 列表。 模糊化计算机使用这些 Url 下载种子。 如果将 seed 文件与二进制文件一起下载，则指定此值是可选的。
   - **操作系统平台类型**：运行模糊化作业的计算机的操作系统（OS）平台。 可用值为**Windows**和**Linux**。
   - **Windows edition/Linux 版本**：运行模糊化作业的计算机的操作系统版本。 如果计算机具有不同的操作系统版本，则可以覆盖默认值。
   - **包安装脚本**：要在测试计算机上运行的脚本。 此脚本在提交模糊化作业之前安装测试目标程序及其依赖项。
   - **作业提交参数**：
       - **种子目录**：包含种子的模糊计算机上的目录的路径。
       - **Seed Extension**：种子的文件扩展名。
       - **测试驱动程序可执行**文件：模糊化计算机上目标可执行文件的路径。
       - **测试驱动程序可执行文件体系结构**：目标可执行文件的体系结构。 可用值为**x86**和**amd64**。
       - **测试驱动程序参数**：传递到测试可执行文件的命令行参数。 参数 "% testfile.txt%" （包括引号）自动替换为目标文件的完整路径。 此文件由测试驱动程序分析并且是必需的。
       - 测试**完成后，测试驱动程序进程退出**：如果测试驱动程序在完成后终止，则选择此复选框。 如果需要强行关闭测试驱动程序，请将其清除。
       - **最长持续时间（以秒为单位）**：预计目标程序分析输入文件所需的最长预期时间。 估算越精确，模糊化应用运行的效率就越高。
       - **可以重复运行测试驱动程序**：如果测试驱动程序可以重复运行（不依赖于持久性或共享的全局状态），请选中此复选框。
       - **可以重命名测试驱动程序**：如果可以重命名测试驱动程序可执行文件，但仍能正常工作，请选中此复选框。
       - **模糊化应用程序作为单个 Os 进程运行**：如果测试驱动程序在单个操作系统进程下运行，则选择此复选框。 如果测试驱动程序产生额外的进程，请清除此方法。

有关此任务的 YAML 配置的信息，请查看[Microsoft 安全风险检测 YAML 选项](yaml-configuration.md#microsoft-security-risk-detection-task)

## <a name="roslyn-analyzers-task"></a>Roslyn 分析器任务

> [!NOTE]
> 你的生成需要满足以下条件，然后才能运行 Roslyn 分析器任务：
>
> - 您的生成定义包含内置的 MSBuild 或 VSBuild 生成任务，用于编译 c # 或 Visual Basic 代码。 分析器任务依赖于内置任务的输入和输出来运行启用了 Roslyn 分析器的 MSBuild 编译。
> - 运行此生成任务的生成代理安装了 Visual Studio 2017 版本15.5 或更高版本，因此它使用编译器版本2.6 或更高版本。

以下列表和说明中显示了任务配置的详细信息。

可用选项包括：

- **规则集**：值为**sdl 必需**、 **sdl 建议**或你自己的自定义规则集。
- **分析器版本**：建议选择 "**最新**"。
- **编译器警告禁止显示文件**：一个文本文件，其中包含已取消显示的警告 id 的列表。
- **控制选项** > **运行此任务**：指定运行任务的时间。 选择 "**自定义条件**" 指定更复杂的条件。

> [!NOTE]
>
> - Roslyn 分析器与编译器集成，只能作为 csc 编译的一部分运行。 因此，此任务需要重播或再次运行在生成中运行的编译器命令。 此重播或运行通过查询 MSBuild 生成任务日志 Visual Studio Team Services （VSTS）来完成。
>
>   任务不能通过其他方式可靠地从生成定义获取 MSBuild 编译命令行。 建议添加一个自由格式文本框，使用户能够输入其命令行。 但这样做很难使这些命令行保持最新，并与主版本同步。
>
>   自定义生成需要重播整个命令集，而不只是编译器命令。 在这些情况下，启用 Roslyn 分析器并不简单或可靠。
>
> - Roslyn 分析器与编译器集成。 若要调用，Roslyn 分析器需要编译。
>
>   这一新的生成任务是通过重新编译已生成的 c # 项目来实现的。 新任务仅使用与原始任务相同的生成或生成定义中的 MSBuild 和 VSBuild 生成任务。 但是，在这种情况下，新任务会将其用于启用 Roslyn 分析器。
>
>   如果新任务在与原始任务相同的代理上运行，则新任务的*输出将在 "源"* 文件夹中覆盖原始任务的输出。 尽管生成输出是相同的，但我们建议你运行 MSBuild，将输出复制到项目暂存目录，然后运行 Roslyn 分析器。

有关 Roslyn 分析器任务的其他资源，请查看 Microsoft Docs 上[基于 Roslyn 的分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)。

你可以在 NuGet 页[CodeAnalysis](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)上找到此生成任务安装并使用的分析器包。

有关此任务的 YAML 配置的信息，请查看我们的[Roslyn 分析器 YAML 选项](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint 任务

有关 TSLint 的详细信息，请参阅[TSLint GitHub](https://github.com/palantir/tslint)存储库。

>[!NOTE] 
>如您所知， [TSLint GitHub](https://github.com/palantir/tslint)存储库主页指出 TSLint 将在2019的某个时间被弃用。 Microsoft 正在调查[ESLint](https://github.com/eslint/eslint)作为备用任务。

有关此任务的 YAML 配置的信息，请查看[TSLINT YAML 选项](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>发布安全分析日志任务

以下屏幕截图和列表中显示了任务配置的详细信息。

![配置发布安全分析日志生成任务](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **项目名称**：任何字符串标识符。
- **项目类型**：根据您的选择，您可以将日志发布到您的 Azure DevOps Server 或生成代理可以访问的共享文件。
- **工具**：你可以选择保留特定工具的日志，也可以选择**所有工具**来保留所有日志。

有关此任务的 YAML 配置的信息，请查看[发布安全日志 YAML 选项](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>安全报告任务

以下屏幕截图和列表显示了安全报表配置的详细信息。

![配置安全报告生成任务](./media/security-tools/4-createsecurityanalysisreport600.png)

- **报表**：选择**管道控制台**、 **TSV 文件**和**Html 文件**格式中的任何一种。 为每个选定的格式创建一个报表文件。
- **工具**：在生成定义中选择要为其提供检测到的问题摘要的工具。 对于所选的每个工具，可能会有选择是仅显示错误还是查看摘要报表中的错误和警告。
- **高级选项**：如果未选择任何一个工具的日志，则可以选择记录警告或错误。 如果记录错误，任务将失败。
- **基本日志文件夹**：可以自定义要在其中找到日志的基本日志文件夹。 但通常不会使用此选项。

有关此任务的 YAML 配置的信息，请查看我们的[安全报表 YAML 选项](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>分析后任务

以下屏幕截图和列表中显示了任务配置的详细信息。

![配置后期分析生成任务](./media/security-tools/a-post-analysis600.png)

- **工具**：选择您要为其有条件地注入生成中断的生成定义中的工具。 对于所选的每个工具，可能会有选择是只中断错误，还是仅中断错误和警告。
- **报表**：可以选择写入导致生成中断的结果。 结果将写入 Azure DevOps 控制台窗口和日志文件。
- **高级选项**：如果未选择任何一个工具的日志，则可以选择记录警告或错误。 如果记录错误，任务将失败。

有关此任务的 YAML 配置的信息，请查看我们的[Post 分析 YAML 选项](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>后续步骤

有关基于 YAML 的配置的信息，请参阅我们的[YAML 配置指南](yaml-configuration.md)。

如果你对安全代码分析扩展和提供的工具有其他疑问，请查看[我们的常见问题页面](security-code-analysis-faq.md)。
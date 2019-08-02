---
title: Microsoft Azure 安全代码分析任务自定义指南
description: 本文介绍如何自定义安全代码分析扩展中的任务
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
ms.openlocfilehash: ab219b71eb8cd6f6172b7d02a639301c67811b49
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718348"
---
# <a name="how-to-configure--customize-the-build-tasks"></a>如何：配置 & 自定义生成任务

本页详细说明每个生成任务中可用的配置选项, 从安全代码分析工具任务开始, 后跟处理后任务

## <a name="anti-malware-scanner-task"></a>反恶意软件扫描器任务

> [!NOTE]
> 反恶意软件生成任务需要启用 Windows Defender 的生成代理, 该代理在 "托管 VS2017" 和更高版本的生成代理上为 true。 (它将不会在旧版/VS2015 的 "托管" 代理上运行)无法在这些代理上更新签名, 但签名应始终相对较新, 不能超过3小时。

下面是有关配置的屏幕截图和详细信息。

![自定义反恶意软件扫描程序生成任务](./media/security-tools/5-add-anti-malware-task600.png) 

- 类型为 "**基本**" 的设置
- 对于 Type = **Custom**, 可以提供命令行参数以自定义扫描。

Windows Defender 使用 Windows 更新客户端下载和安装签名。 如果生成代理上的签名更新失败, 则 HRESULT 错误代码可能来自 Windows 更新。 有关 Windows 更新错误和缓解措施的详细信息, 请访问[此页面](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)和此[technet 页面](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)

## <a name="binskim-task"></a>BinSkim 任务

> [!NOTE]
> 作为运行 BinSkim 任务的先决条件, 你的构建应满足以下条件之一。
>    - 您的生成从托管代码生成二进制项目。
>    - 已提交二进制项目, 希望使用 BinSkim 进行分析。

下面是有关配置的屏幕截图和详细信息。 

![BinSkim 安装程序](./media/security-tools/7-binskim-task-details.png)  
1. 将生成配置设置为 "调试" 以生成 * **.pdb**调试文件。 BinSkim 使用它们将输出二进制文件中发现的问题映射回源代码。 
2. 选择 Type = **Basic** & Function = "**分析**" 可以避免研究和创建自己的命令行。 
3. **Target** -一种或多种说明符, 可解析为要分析的一个或多个二进制文件、目录或筛选模式。 
    - 应该用**分号 (;)** 分隔多个目标。 
    - 可以是单个文件, 也可以包含通配符。
    - 目录应始终以结尾\*
    - 示例:

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
4. 如果选择 Type = **Command Line**, 
     - 请确保**BinSkim**的第一个参数是使用完整路径**分析**的谓词, 或相对于源目录的路径。
     - 对于**命令行**输入, 多个目标应由空格分隔。
     - 可以省略 **/o**或 **/output**文件参数;将为你添加或替换它。 
     - **标准命令行配置** 

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
          > [!NOTE]
          > 在为\*目标指定目录时, 尾随是非常重要的。 

有关命令行参数、按 ID 或退出代码的规则的详细信息, 请访问[BinSkim 用户指南](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)BinSkim

## <a name="credential-scanner-task"></a>凭据扫描器任务
下面是有关配置的屏幕截图和详细信息。
 
![凭据扫描器自定义](./media/security-tools/3-taskdetails.png)

可用选项包括 
  - **输出格式**– TSV/CSV/SARIF/PREfast
  - **工具版本**您最近
  - **Scan folder** –存储库中要扫描的文件夹
  - **Searchers 文件类型**-用于查找用于扫描的 Searchers 文件的选项。
  - **禁止显示文件**– [JSON](https://json.org/)文件可用于取消输出日志中的问题 (资源部分中的更多详细信息)。 
  - **详细输出**-一目了然 
  - **批大小**-用于并行运行凭据扫描程序的并发线程数。 默认值为 20 (值必须在1到2147483647的范围内)。
  - **匹配超时**-在放弃检查之前尝试搜索者匹配所花费的时间。 
  - **文件扫描读取缓冲区大小**-读取内容时缓冲区大小 (以字节为单位)。 (默认值为 524288) 
  - **最大文件扫描读取字节**数-内容分析期间从给定文件中读取的最大字节数。 (默认值为 104857600) 
  - **运行此任务**(在 "**控制选项**" 下)-指定任务应运行的时间。 选择 "自定义条件" 指定更复杂的条件。 
  - **版本**-在 Azure DevOps 中生成任务版本。 不经常使用。 

## <a name="microsoft-security-risk-detection-task"></a>Microsoft 安全风险检测任务
> [!NOTE]
> 您必须创建一个帐户并将其配置为包含风险检测服务, 才能使用此任务。 此服务需要单独的加入过程;此扩展中的大多数其他任务不是 "即插即用"。 请参阅[microsoft 安全风险检测](https://aka.ms/msrddocs)和[microsoft 安全风险检测:](https://docs.microsoft.com/security-risk-detection/how-to/)如何获取说明。

有关配置的详细信息。

输入所需的数据;每个选项都具有悬停文本帮助。
   - **MSRD 的 Azure DevOps 服务终结点名称**:如果已创建了一种泛型类型的 Azure DevOps Service 终结点来存储 MSRD 实例 URL (你有载入) 和 REST API 访问令牌, 则可以选择该服务终结点。 如果没有, 请单击 "管理" 链接, 为此 MSRD 任务创建和配置新的服务终结点。 
   - **帐户 ID**:它是可以从 MSRD 帐户 URL 检索的 GUID。
   - **二进制文件的 url**:以分号分隔的公开可用 Url 列表 (模糊化机将使用该列表下载二进制文件)。
   - **Seed 文件的 url**:以分号分隔的公开可用 Url 列表 (模糊化机将使用该列表下载种子)。 如果 seed 文件与二进制文件一起下载, 则此字段是可选的。
   - **操作系统平台类型**:要在其上运行模糊化作业的计算机的 OS 平台类型 (Windows 或 Linux)。
   - **Windows edition/Linux 版**:要在其上运行模糊化作业的计算机的 OS 版本。 如果计算机具有不同的操作系统版本, 则可以覆盖默认值。
   - **包安装脚本**:提供要在测试计算机上运行的脚本, 以便在提交模糊化作业之前安装测试目标程序及其依赖项。
   - **作业提交参数**:
       - **种子目录**:包含种子的模糊化计算机上的目录的路径。
       - **Seed Extension**:种子的文件扩展名
       - **测试驱动程序可执行文件**:模糊计算机上目标可执行文件的路径。
       - **测试驱动程序可执行体系结构**:目标可执行文件体系结构 (x86 或 amd64)。
       - **测试驱动程序参数**:传递给测试目标可执行文件的命令行参数。 请注意, 会自动将 "% testfile.txt%" 符号 (包括双引号) 替换为测试驱动程序预期要分析的目标文件的完整路径, 并且是必需的。
       - 测试**完成后, 测试驱动程序进程退出**:选中以在完成后终止测试驱动程序;如果需要强行关闭测试驱动程序, 请取消选中。
       - **最长持续时间 (秒)** :为目标程序分析输入文件所需的最长预期时间提供估算。 此估计越精确, 模糊化运行的效率就越高。
       - **测试驱动程序可以重复运行**:检查是否可以重复运行测试驱动程序, 而无需依赖于持久化/shared 全局状态。
       - **可重命名测试驱动程序**:检查是否可以重命名测试驱动程序可执行文件, 并且仍能正常工作。
       - **模糊化应用程序作为单个 OS 进程运行**:检查测试驱动程序是否在单个操作系统进程下运行;取消检查测试驱动程序是否产生其他进程。


## <a name="roslyn-analyzers-task"></a>Roslyn 分析器任务
> [!NOTE]
> 作为运行 Roslyn 分析器任务的先决条件, 你的生成应满足以下条件。
>  - 您的生成定义包含内置的 MSBuild 或 VSBuild 生成任务以编译C# (或 VB) 代码。 此任务依赖于该特定生成任务的输入和输出, 以重新运行启用了 Roslyn 分析器的 MSBuild 编译。
>  - 运行此生成任务的生成代理安装了 Visual Studio 2017 v 15.5 或更高版本 (编译器版本 2.6. x)。
>

有关配置的详细信息。

可用选项包括 
- **规则集**-sdl 必需、sdl 建议, 或者你可以使用自己的自定义规则集。
- **分析器版本**您最近
- **编译器警告禁止显示文件**-包含应禁止显示的警告 id 列表的文本文件。 
- **运行此任务**(在 "**控制选项**" 下)-指定任务应运行的时间。 选择 "**自定义条件**" 指定更复杂的条件。 

> [!NOTE]
> - Roslyn 分析器集成了编译器, 只能作为 CSC 编译的一部分运行。 因此, 此任务需要重播/重新运行在生成之前运行的编译器命令。 这是通过为 MSBuild 生成任务日志查询 VSTS 来完成的 (该任务不能通过其他方式从生成定义中可靠地获取 MSBuild 编译命令行; 我们确实考虑添加一个自由格式的文本框以允许用户输入其命令行但很难使它们保持最新, 并与主版本同步。 自定义生成需要重播整个命令集, 而不只是编译器命令, 在这种情况下, 启用 Roslyn 分析器并不简单/可靠。 
> - Roslyn 分析器与编译器集成, 需要调用编译。 此生成任务是通过在同一C#生成/生成定义中重新编译已使用 MSBuild/VSBuild 生成任务生成的项目, 但在这种情况下, 将使用分析器启用。 如果此生成任务在与原始生成任务相同的代理上运行, 则原始 MSBuild/VSBuild 生成任务的输出将在 "源" 文件夹中被此生成任务的输出覆盖。 生成输出将是相同的, 但建议运行 MSBuild, 将输出复制到项目暂存目录, 然后运行 Roslyn。
>

有关 Roslyn 分析器任务的其他资源, 请检查[Microsoft Docs 上的 Roslyn 分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)

可在[此处](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)找到此生成任务安装和使用的分析器包 

## <a name="tslint-task"></a>TSLint 任务

有关 TSLint 的详细信息, 请访问[TSLint GitHub](https://github.com/palantir/tslint)存储库
>[!NOTE] 
>如您所知, TSLint 将在 2019 (源:[TSLint GitHub](https://github.com/palantir/tslint)存储库)此团队当前正在调查[ESLint](https://github.com/eslint/eslint) , 并在路线图中为 ESLint 创建新的任务。

## <a name="publish-security-analysis-logs-task"></a>发布安全分析日志任务
下面是有关配置的屏幕截图和详细信息。

![自定义发布安全分析](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **项目名称**-可以是任意字符串标识符
- **项目类型**-可以将日志发布到 Azure DevOps 服务器或可供生成代理访问的文件共享。 
- **工具**-您可以选择保留单个/特定工具的日志, 或选择**所有工具**来保留所有日志。 

## <a name="security-report-task"></a>安全报告任务
下面是有关配置的屏幕截图和详细信息。  
![安装后期分析](./media/security-tools/4-createsecurityanalysisreport600.png) 
- **报表**-选择要创建的报表文件;将在每个格式**控制台**、 **TSV**和/或**HTML**中创建一个 
- **工具**-在生成定义中选择你想要检测到的问题摘要的工具。 对于所选的每个工具, 可能会有选择是只查看报表中的错误还是同时查看错误和警告。 
- **高级选项**-可以选择记录警告或错误 (并使任务失败) 以防选中其中一个工具的日志。
你可以自定义要在其中找到日志的基本日志文件夹, 但这不是典型方案。 

## <a name="post-analysis-task"></a>分析后任务
下面是有关配置的屏幕截图和详细信息。

![自定义后期分析](./media/security-tools/a-post-analysis600.png) 
- **工具**-在生成定义中选择要根据其发现生成中断的工具。 对于所选的每个工具, 可能会有选择是只中断错误, 还是仅中断错误和警告。 
- **报告**-您可以选择将找到的结果写入到 Azure DevOps 控制台窗口和日志文件中, 并引发生成中断。 
- **高级选项**-可以选择记录警告或错误 (并使任务失败) 以防选中其中一个工具的日志。

## <a name="next-steps"></a>后续步骤

如果对扩展和所提供的工具有更多的问题, 请[查看我们的 "常见问题" 页。](security-code-analysis-faq.md)



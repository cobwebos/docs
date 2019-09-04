---
title: Microsoft 安全代码分析文档概述
description: 本文概述了 Microsoft 安全代码分析扩展
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
ms.openlocfilehash: a7ab908673f60a651db48ae0485592a362741b6d
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242318"
---
# <a name="about-microsoft-security-code-analysis"></a>关于 Microsoft 安全代码分析

利用 Microsoft 安全代码分析扩展，团队可以将安全代码分析添加到其 Azure DevOps 持续集成和交付（CI/CD）管道。 Microsoft[安全开发生命周期（SDL）](https://www.microsoft.com/securityengineering/sdl/practices)专家建议进行此分析。

一致性 UX 通过隐藏正在运行的工具的复杂性来简化安全性。 通过基于 NuGet 的工具交付，团队不再需要管理工具的安装或更新。 使用用于生成任务的命令行和基本接口，所有用户都可以根据需要对工具有很多控制。

团队还可以使用功能强大的后处理功能，如：

- 正在为保留发布日志。
- 生成可操作的、以开发人员为中心的报表。
- 配置回归测试的生成中断。

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>为什么要使用 Microsoft 安全代码分析？

### <a name="security-simplified"></a>简化安全性

将 Microsoft 安全代码分析工具添加到 Azure DevOps 管道与添加新任务一样简单。 自定义任务或使用其默认行为。 任务作为 Azure DevOps 管道的一部分运行，并生成详细说明多种结果的日志。

### <a name="clean-builds"></a>清理生成

解决工具所报告的初始问题后，可以将扩展配置为对新问题中断生成。 在每个拉取请求上设置持续集成版本非常简单。

### <a name="set-it-and-forget-it"></a>设置它并将其遗忘

默认情况下，生成任务和工具随时保持最新状态。 如果有工具的更新版本，则无需下载和安装它。 扩展会为你处理更新。

### <a name="under-the-hood"></a>揭秘

扩展的生成任务隐藏了的复杂性：
  - 运行安全静态分析工具。
  - 处理日志文件中的结果以创建摘要报表或中断生成。

## <a name="microsoft-security-code-analysis-tool-set"></a>Microsoft 安全代码分析工具集

Microsoft 安全代码分析扩展使你随时可以使用最新版本的重要分析工具。 此扩展包括 Microsoft 托管工具和开源工具。

使用相应的生成任务配置和运行管道后，这些工具会自动下载到云托管的代理。

本部分列出了扩展中当前可用的工具集。 观看更多工具。 此外，请向我们发送你希望我们添加的工具的建议。

### <a name="anti-malware-scanner"></a>反恶意软件扫描程序

反恶意软件扫描程序生成任务现已包含在 Microsoft 安全代码分析扩展中。 此任务必须在已安装 Windows Defender 的生成代理上运行。 有关详细信息，请参阅[Windows Defender 网站](https://aka.ms/defender)。

### <a name="binskim"></a>BinSkim

BinSkim 是一种可移植可执行（PE）的轻型扫描程序，用于验证编译器设置、链接器设置和二进制文件的其他与安全相关的特征。 此生成任务在 binskim 控制台应用程序的周围提供了一个命令行包装器。 BinSkim 是一个开源工具。 有关详细信息，请参阅[GitHub 上的 BinSkim](https://github.com/Microsoft/binskim)。

### <a name="credential-scanner"></a>凭据扫描器

在源代码中存储的密码和其他机密是一个重大问题。 Credential 扫描器是一个专用静态分析工具，可帮助解决此问题。 该工具在源代码和生成输出中检测凭据、机密、证书和其他敏感内容。

### <a name="microsoft-security-risk-detection"></a>Microsoft 安全风险检测

Microsoft 安全风险检测（MSRD）是一项基于云的服务，用于模糊测试。 它识别软件中可利用的安全错误。 此服务需要单独的载入过程。 有关详细信息，请参阅[MSRD 开发人员中心](https://docs.microsoft.com/security-risk-detection/)。

### <a name="roslyn-analyzers"></a>Roslyn 分析器

Roslyn 分析器是 Microsoft 的编译器集成工具，用于静态分析托管C#代码和 Visual Basic 代码。 有关详细信息，请参阅[基于 Roslyn 的分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)。

### <a name="tslint"></a>TSLint

TSLint 是一种可扩展的静态分析工具，用于检查 TypeScript 代码的可读性、可维护性和功能错误。 它受新式编辑器和生成系统广泛支持。 可以用自己不起毛的规则、配置和格式化程序对其进行自定义。 TSLint 是一个开源工具。 有关详细信息，请参阅[GitHub 上的 TSLint](https://github.com/palantir/tslint)。

## <a name="analysis-and-post-processing-of-results"></a>分析并处理结果

Microsoft 安全代码分析扩展还包含三个后处理任务。 这些任务有助于分析安全工具任务找到的结果。 添加到管道后，这些任务通常遵循所有其他工具任务。

### <a name="publish-security-analysis-logs"></a>发布安全分析日志

"发布安全分析日志生成" 任务将保留在生成过程中运行的安全工具的日志文件。 您可以阅读这些日志以便进行调查和跟进。

你可以将日志文件作为 .zip 文件发布到 Azure Artifacts。 你还可以从专用生成代理将它们复制到可访问的文件共享中。

### <a name="security-report"></a>安全报表

安全报表生成任务分析日志文件。 这些文件由生成过程中运行的安全工具创建。 然后，生成任务将创建单个汇总报表文件。 此文件显示分析工具发现的所有问题。

可以将此任务配置为报告特定工具或所有工具的结果。 你还可以选择要报告的问题级别，例如仅错误或错误和警告。

### <a name="post-analysis-build-break"></a>后期分析（生成中断）

使用后期分析生成任务，可以注入特意导致生成失败的生成中断。 如果一个或多个分析工具在代码中报告问题，则会注入生成中断。

你可以配置此任务以中断特定工具或所有工具发现的问题的生成。 你还可以根据发现的问题的严重性（如错误或警告）来配置它。

>[!NOTE]
>按照设计，当任务成功完成时，每个生成任务都将成功。 无论工具是否找到问题，都是如此，以便可以通过允许所有工具运行来完成生成。

## <a name="next-steps"></a>后续步骤

有关如何载入和安装 Microsoft 安全代码分析的说明，请参阅我们的[载入和安装指南](security-code-analysis-onboard.md)。

有关配置生成任务的详细信息，请参阅我们的[配置指南](security-code-analysis-customize.md)。

如果对扩展和所提供的工具还有其他疑问，请查看[常见问题页](security-code-analysis-faq.md)。

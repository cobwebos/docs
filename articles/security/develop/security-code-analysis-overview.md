---
title: Microsoft Azure 安全代码分析文档概述
description: 本文概述了安全代码分析扩展
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
ms.openlocfilehash: 283d63bafc583f2ac9da3294644aaebd17d7c950
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718283"
---
# <a name="about-microsoft-security-code-analysis"></a>关于 Microsoft 安全代码分析

**Microsoft 安全代码分析扩展**使团队能够按照 Microsoft[安全开发生命周期 (SDL)](https://www.microsoft.com/securityengineering/sdl/practices)专家建议的方式将安全代码分析无缝地集成到其 AZURE DevOps CI/CD 管道中。 通过一致的 UX, 可简化运行各种工具的复杂性, 从而简化了安全性。 通过基于 NuGet 的工具交付, 团队不再需要管理工具的安装或更新。 使用命令行和基本生成任务界面, 所有用户 (从精通工具专家到日常开发人员) 都可以根据需要对工具有很多控制。 团队还可以利用强大的后处理功能 (如发布日志以实现保留), 并生成可操作的开发人员重点报表 & 在回归上配置生成中断。

## <a name="why-microsoft-security-code-analysis"></a>为什么选择 Microsoft 安全代码分析

### <a name="security-simplified"></a>简化安全性

将安全代码分析工具添加到 Azure DevOps 管道与添加新任务一样简单。 自定义它们或采用默认值。 这些任务作为 DevOps 管道的一部分运行, 并生成记录所有种类的发现的日志。

### <a name="clean-builds"></a>清理生成

解决工具所报告的初始问题后, 可以将扩展配置为对新问题中断生成。 在每个拉取请求上设置持续集成生成从来都不是这么简单!

### <a name="set-it-and-forget-it"></a>设置它并将其遗忘

生成任务和工具可设置为保持最新状态 (默认为)。 如果有该工具的更新版本, 则无需下载并安装它;此扩展会为你处理此扩展。 

>>>
### <a name="under-the-hood"></a>在后台

Microsoft 安全代码分析扩展生成任务抽象了的复杂性:
  - 运行安全静态分析工具和
  - 处理日志文件中的结果以创建摘要报表或中断生成。
>>>

## <a name="security-code-analysis-toolset"></a>安全代码分析工具集

Microsoft 安全代码分析扩展可供你使用最新版本的重要分析工具。 此扩展包括 Microsoft 内部和开源工具。 配置 & 使用相应的生成任务运行管道后, 会自动在云托管代理上下载工具。 下面是目前扩展中可用的一组工具。 请继续关注详细信息, 并向我们发送有关可添加的工具的建议。

### <a name="anti-malware-scanner"></a>反恶意软件扫描程序

反恶意软件扫描程序生成任务现已包含在 Microsoft 安全代码分析扩展中。 它必须在已安装 Windows Defender 的生成代理上运行。 有关详细信息, 请访问[Defender 网站](https://aka.ms/defender) 

### <a name="binskim"></a>BinSkim

BinSkim 是可移植可执行 (PE) 的轻型扫描程序, 用于验证编译器/链接器设置和其他与安全相关的二进制特性。 生成任务在 BinSkim 应用程序的周围提供了一个命令行包装器。 BinSkim 是一种开源工具, 有关详细信息, 请访问[GitHub 上的 BinSkim](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>凭据扫描器

源代码中存储的密码和其他机密目前是一个重大问题。 Credential 扫描器是一种专用静态分析工具, 用于检测源代码和生成输出中的凭据、机密、证书和其他敏感内容。

### <a name="microsoft-security-risk-detection"></a>Microsoft 安全风险检测

安全风险检测是 Microsoft 提供的基于云的独特模糊测试服务, 用于识别软件中可利用的安全错误。 此服务需要单独的载入过程。 有关详细信息, 请访问[docs.microsoft.com 上的 MSRD](https://docs.microsoft.com/security-risk-detection/)

### <a name="roslyn-analyzers"></a>Roslyn 分析器

Microsoft 编译器集成的静态分析工具, 用于分析托管代码 (C#和 VB)。 有关详细信息, 请访问[docs.microsoft.com 上的 Roslyn 分析器](https://docs.microsoft.com/dotnet/standard/analyzers/)

### <a name="tslint"></a>TSLint

TSLint 是一种可扩展的静态分析工具, 用于检查 TypeScript 代码的可读性、可维护性和功能错误。 它跨新式编辑器和生成系统广泛受支持, 可通过自己不起毛的规则、配置和格式化程序进行自定义。 TSLint 是一种开源工具, 有关详细信息, 请访问[GitHub 上的 TSLint](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>分析并处理结果

Microsoft 安全代码分析扩展还具有三个有用的后处理任务, 可帮助您处理和分析安全工具任务找到的结果。 它们通常会在所有其他工具任务之后添加到管道中。

### <a name="publish-security-analysis-logs"></a>发布安全分析日志
"发布安全分析日志生成" 任务可在生成期间保留安全工具运行的日志文件, 以便进行调查和跟进。

可以将其发布到 Azure 服务器项目 (作为 zip 文件), 或从专用生成代理复制到可访问文件共享。

### <a name="security-report"></a>安全报表
安全报告生成任务分析在生成过程中由安全工具创建的日志文件, 并创建包含分析工具发现的所有问题的单个汇总报表文件。

可以将任务配置为报告特定工具或所有工具的发现, 还可以选择要报告的问题的级别 (错误或错误和警告)。

### <a name="post-analysis-build-break"></a>后期分析 (生成中断)
使用后期分析生成任务, 客户可以注入生成中断, 并使生成失败, 以防一个或多个分析工具报告代码中的发现或问题。

可以将任务配置为中断特定工具或所有工具发现的问题的生成, 以及基于发现的问题的严重级别 (错误或警告)。

>[!NOTE]
>每个生成任务将在设计时成功完成, 前提是该工具成功完成, 是否有发现或不允许运行所有工具。

## <a name="next-steps"></a>后续步骤

有关载入和安装安全代码分析的说明, 请参阅我们的[载入和安装指南](security-code-analysis-onboard.md)

有关配置生成任务的详细信息, 请参阅我们的[配置指南](security-code-analysis-customize.md)

如果对扩展和所提供的工具有更多的问题, 请[查看我们的 "常见问题" 页。](security-code-analysis-faq.md)
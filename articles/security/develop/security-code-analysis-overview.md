---
title: 微软安全代码分析文档概述
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
ms.openlocfilehash: 963bc909b69962cded0a50d717e3a653d3d69769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851462"
---
# <a name="about-microsoft-security-code-analysis"></a>关于微软安全代码分析

使用 Microsoft 安全代码分析扩展，团队可以将安全代码分析添加到其 Azure DevOps 持续集成和交付 （CI/CD） 管道中。 此分析是由 Microsoft[的安全开发生命周期 （SDL）](https://www.microsoft.com/securityengineering/sdl/practices)专家推荐的。

一致的 UX 通过隐藏运行工具的复杂性来简化安全性。 通过基于 NuGet 的工具交付，团队不再需要管理工具的安装或更新。 使用生成任务的命令行和基本接口，所有用户都可以根据需要对工具进行尽可能多的控制。

团队还可以使用强大的后处理功能，例如：

- 发布日志以保留。
- 生成可操作的、以开发人员为中心的报告。
- 在回归测试中配置生成中断。

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>为什么我应该使用 Microsoft 安全代码分析？

### <a name="security-simplified"></a>安全简化

将 Microsoft 安全代码分析工具添加到 Azure DevOps 管道就像添加新任务一样简单。 自定义任务或使用其默认行为。 任务作为 Azure DevOps 管道的一部分运行，并生成详细说明多种结果的日志。

### <a name="clean-builds"></a>清理生成

解决工具报告的初始问题后，可以将扩展配置为在新问题上中断构建。在每个拉取请求的基础上设置持续集成构建非常简单。

### <a name="set-it-and-forget-it"></a>设置它，忘记它

默认情况下，生成任务和工具保持最新。 如果有工具的更新版本，则无需下载并安装它。 扩展负责为您更新。

### <a name="under-the-hood"></a>揭秘

扩展的生成任务隐藏了以下的复杂性：
  - 运行安全静态分析工具。
  - 处理日志文件的结果以创建摘要报告或中断生成。

## <a name="microsoft-security-code-analysis-tool-set"></a>微软安全代码分析工具集

Microsoft 安全代码分析扩展使重要分析工具的最新版本随时可供您使用。 扩展包括 Microsoft 管理的工具和开源工具。

使用相应的生成任务配置和运行管道后，这些工具将自动下载到云托管代理。

本节列出了扩展中当前可用的工具集。 注意添加更多工具。 此外，请向我们发送您希望我们添加的工具的建议。

### <a name="anti-malware-scanner"></a>反恶意软件扫描仪

反恶意软件扫描程序生成任务现在包含在 Microsoft 安全代码分析扩展中。 此任务必须在已安装 Windows Defender 的生成代理上运行。 有关详细信息，请参阅 Windows[防御者网站](https://aka.ms/defender)。

### <a name="binskim"></a>宾斯基姆

BinSkim 是一种可移植可执行 （PE） 轻量级扫描仪，用于验证二进制文件的编译器设置、链接器设置和其他与安全相关的特征。 此生成任务在 binskim.exe 控制台应用程序周围提供命令行包装器。 BinSkim 是一个开源工具。 有关详细信息，请参阅[GitHub 上的 BinSkim。](https://github.com/Microsoft/binskim)

### <a name="credential-scanner"></a>凭据扫描程序

存储在源代码中的密码和其他机密是一个严重的问题。 凭据扫描程序是一种专有的静态分析工具，可帮助解决此问题。 该工具检测源代码和生成输出中的凭据、机密、证书和其他敏感内容。

### <a name="microsoft-security-risk-detection"></a>微软安全风险检测

Microsoft 安全风险检测 （MSRD） 是一种基于云的模糊测试服务。 它标识软件中的可利用安全错误。 此服务需要单独的订阅和激活。 有关详细信息，请参阅[MSRD 开发人员中心](https://docs.microsoft.com/security-risk-detection/)。

### <a name="roslyn-analyzers"></a>Roslyn 分析器

Roslyn 分析器是 Microsoft 的编译器集成工具，用于静态分析托管 C# 和可视化基本代码。 有关详细信息，请参阅[基于罗斯林的分析仪](https://docs.microsoft.com/dotnet/standard/analyzers/)。

### <a name="tslint"></a>茨林特

TSLint 是一种可扩展的静态分析工具，用于检查 TypeScript 代码的可读性、可维护性和功能上的错误。 它得到了现代编辑器和构建系统的广泛支持。 您可以使用自己的绒毛规则、配置和事事对其进行自定义。 TSLint 是一个开源工具。 有关详细信息，请参阅[GitHub 上的 TSLint。](https://github.com/palantir/tslint)

## <a name="analysis-and-post-processing-of-results"></a>结果分析和后处理

Microsoft 安全代码分析扩展也有三个后处理任务。 这些任务可帮助您分析安全工具任务的结果。 添加到管道时，这些任务通常遵循所有其他工具任务。

### <a name="publish-security-analysis-logs"></a>发布安全分析日志

发布安全分析日志生成任务保留生成期间运行的安全工具的日志文件。 您可以阅读这些日志进行调查和跟进。

可以将日志文件作为 .zip 文件发布到 Azure 项目。 您还可以将它们复制到来自私有生成代理的可访问文件共享。

### <a name="security-report"></a>安全报告

安全报告生成任务解析日志文件。 这些文件由生成期间运行的安全工具创建。 然后，生成任务将创建单个摘要报表文件。 此文件显示分析工具发现的所有问题。

您可以将此任务配置为报告特定工具或所有工具的结果。 您还可以选择要报告的问题级别，例如仅错误或错误和警告。

### <a name="post-analysis-build-break"></a>分析后（生成中断）

使用"分析后"生成任务时，可以注入有意导致生成失败的生成中断。 如果一个或多个分析工具报告代码中的问题，则注入生成中断。

可以配置此任务以中断特定工具或所有工具发现的问题的生成。 您还可以根据发现的问题（如错误或警告）的严重性对其进行配置。

>[!NOTE]
>根据设计，如果任务成功完成，则每个生成任务都会成功。 无论工具是否发现问题，都是如此，因此，通过允许运行所有工具，生成可以运行到完成。

## <a name="next-steps"></a>后续步骤

有关如何载入和安装 Microsoft 安全代码分析的说明，请参阅我们的[载入和安装指南](security-code-analysis-onboard.md)。

有关配置生成任务的详细信息，请参阅我们的[配置指南](security-code-analysis-customize.md)或[YAML 配置指南](yaml-configuration.md)。

如果您对扩展和提供的工具有任何疑问，请查看我们的[常见问题页面](security-code-analysis-faq.md)。

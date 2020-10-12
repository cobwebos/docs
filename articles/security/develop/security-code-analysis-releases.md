---
title: Microsoft 安全代码分析版本
description: 本文介绍即将发布的 Microsoft 安全代码分析扩展
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 33ce2a496caa52609d8bdf8c92e29064ca4ae349
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85362034"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft 安全代码分析版本和路线图

Microsoft 安全代码分析团队与开发人员支持部门合作，自豪地宣布 MSCA 扩展近期即将推出增强功能。


## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v2.0：2020 年 4 月发布

### <a name="innovations--improvements"></a>创新和改进

- **核心引擎**

   - 平均性能提升 25%，运行时间接近线性
   - 使用基于上下文/证据的搜索和排名以提高准确性
   - 改进了对显而易见的占位符（例如，fakePassword）的常规密码检测和匹配逻辑

- **覆盖范围** - 支持 25 种以上的机密类型，包括下述最常请求的：

   - Fabric 帐户证书通行短语
   - 客户端密码/API 密钥
   - HTTP 授权标头
   - Amazon S3 客户端密码访问密钥
   - Azure Active Directory 客户端访问令牌
   - Azure 函数主密钥/API 密钥
   - Power BI 访问密钥
   - Azure 资源管理器模板密码模式

- **输出**

   - 支持 SARIF 2.1 和 CSV 文件输出文件格式

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v1.6.0：2020 年 4 月发布

### <a name="improvements"></a>改进

- 功能：更新为最终 SARIF v2（版本 2.1.16）。 此更新在命令行上传递哈希时启用结果缓存，并在递归分析具有多个扫描目标副本的目录时显著提高性能。
- BUG 修复：修复了 BA2021.DoNotMarkWritableSectionsAsExecutable 输出中的拼写错误。
- 性能：为托管程序集（包括 IL 库（已预先编译）二进制文件）消除了所有非混合模式的 PDB 加载。
- 误报修复：验证与二进制文件一起放置的 PDB 是否确实与所分析的二进制文件匹配
- 功能：提供了 --local-symbol-directories 参数，用于指定其他（本地、非符号服务器）PDB 查找位置
- 假正修复：跳过对生成的 .NET Core 本机引导程序 exe（不是用户可控代码）的 PDB 驱动型分析。

## <a name="whats-next-in-q3-cy20"></a>第三季度 CY20 中的下一步是什么？

- Java 安全分析工具
- Python 安全分析工具
- 对于 TypeScript 和 JavaScript，请使用 ES Lint 替换 TS Lint
- 资源管理器模板分析工具

## <a name="tool-deprecation-notification"></a>工具弃用通知

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>26 2020 年6月，Microsoft 安全风险检测 (MSRD) 弃用。

不推荐使用的 MSRD 模糊化服务将替换为适用于 Azure 的开源自承载开发人员模糊化平台。 当前正在与许多 Microsoft 核心产品团队合作开发和测试此平台。 此模糊化平台将集成 sanitizers，并允许自适应的学习/CD 管道中内置的自适应模糊测试，这些测试随时间推移随软件项目一起增长。 此平台的开源发行版计划为2020的后半部分。

## <a name="next-steps"></a>后续步骤

有关如何载入和安装 Microsoft 安全代码分析的说明，请参阅我们的 [载入和安装指南](security-code-analysis-onboard.md)。

对于所提供的扩展和工具，如果仍有疑问，请查看我们的[常见问题解答页](security-code-analysis-faq.md)。

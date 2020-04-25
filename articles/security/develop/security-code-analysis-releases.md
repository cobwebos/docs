---
title: Microsoft 安全代码分析版本
description: 本文介绍 Microsoft 安全代码分析扩展的即将发布版本
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
ms.openlocfilehash: d4281d3b6132e551283a71cd1801ef462fbfc68c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146130"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft 安全代码分析版本和路线图

与开发人员支持的合作关系中的 Microsoft 安全代码分析团队非常自豪地宣布 MSCA 扩展的近期和即将推出的增强功能。 请参阅下面的路线图。

![发行版本](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential 扫描器 v2.0：2020年4月发布

### <a name="innovations--improvements"></a>创新 & 改进

- **核心引擎**

   - 平均性能升级为25%，接近线性运行时间
   - 基于上下文/证据的搜索和排名以提高准确性
   - 对常规占位符的一般密码检测和匹配逻辑的改进（例如，fakePassword）

- **覆盖范围**-支持25个以上的机密类型，包括以下请求的顶级内容：

   - Fabric 帐户证书密码
   - 客户端密码/API 密钥
   - HTTP 授权标头
   - Amazon S3 客户端密钥访问密钥
   - Azure Active Directory 客户端访问令牌
   - Azure 函数主机/API 密钥
   - Power BI 访问密钥
   - Azure 资源管理器模板密码模式

- **Outputs**

   - 支持 SARIF 2.1 和 CSV 文件输出文件格式

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0：2020年4月发布

### <a name="improvements"></a>改进

- 功能：更新为最终的 SARIF v2 （版本2.1.16）。 这将在命令行上传递哈希时启用结果缓存，并在递归分析具有多个扫描目标副本的目录时显著提高性能。
- BUG 修复：修复 BA2021 中的拼写错误。DoNotMarkWritableSectionsAsExecutable 输出。
- 性能：消除了托管程序集的所有非混合模式（包括 IL 库（预先编译的）二进制文件）的 PDB 加载。
- FALSE 否定修复：验证与二进制文件一起放置的 PDB 是否与分析下的二进制文件完全匹配
- 功能：提供-------------------------------------
- 误报修复：跳过生成的 .NET core 本机引导 exe （不是用户可控制的代码）的 PDB 驱动的分析。

## <a name="whats-next-in-fy20"></a>FY20 中的下一步是什么？

- Java 安全分析工具
- Python 安全分析工具
- 不起毛的不起毛的 TypeScript 和 JavaScript 替代 TS

## <a name="next-steps"></a>后续步骤

有关如何载入和安装 Microsoft 安全代码分析的说明，请参阅我们的[载入和安装指南](security-code-analysis-onboard.md)。

如果你有关于扩展和提供的工具的更多问题，请查看我们的[常见问题页面](security-code-analysis-faq.md)。

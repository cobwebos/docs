---
title: 微软安全代码分析版本
description: 本文介绍了即将发布的 Microsoft 安全代码分析扩展
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462030"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>微软安全代码分析发布和路线图

Microsoft 安全代码分析团队与开发人员支持部门合作，自豪地宣布最近和即将推出的 MSCA 扩展的增强功能。 请参阅下面的路线图。

![发行版本](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>凭据扫描器 v2.0： 2020 年 4 月 1 日发布

### <a name="innovations--improvements"></a>创新&改进

- **核心引擎**

   - 平均性能提升 25%，接近线性运行时间
   - 基于上下文/证据的搜索和排名，提高准确性
   - 对明显占位符（例如，假密码）的一般密码检测和匹配逻辑的改进

- **覆盖范围**- 支持 25 多种机密类型，包括以下顶级请求：

   - 结构帐户证书密码短语
   - 客户端密钥/API 密钥
   - HTTP 授权标头
   - 亚马逊 S3 客户端密钥
   - Azure 活动目录客户端访问令牌
   - Azure 函数主/API 密钥
   - 电源 BI 访问密钥
   - Azure 资源管理器模板密码模式

- **Outputs**

   - 支持 SARIF 2.1 和 CSV 文件输出文件格式

## <a name="binskim-v160-to-be-released-on-april-2020"></a>宾斯基姆 v1.6.0： 将于 2020 年 4 月发布

### <a name="improvements"></a>改进

- 特点：更新到最终的 SARIF v2（版本 2.1.16）。 这样，当传递命令行上的哈希时，可实现结果缓存，这在递归分析具有多个扫描目标副本的目录时显著提高性能。
- BUG 修复：修复 BA2021 中的拼写错误。不可标记可执行节作为可执行输出。
- 性能：消除托管程序集的所有非混合模式的 PDB 加载，包括 IL 库（提前编译）二进制文件。
- FALSE 否定修复：验证放置在二进制文件旁边的 PDB 是否与正在分析的二进制文件实际匹配
- 特点：提供 --本地符号目录参数，以指定其他（本地、非符号服务器）PDB 查找位置
- FALSE 积极修复：跳过 PDB 驱动的分析，用于生成的 .NET 核心本机引导 exe（这是用户可控制的代码）。

## <a name="whats-next-in-fy20"></a>20 财年的下一步是什么？

- Java 安全分析工具
- Python 安全分析工具
- ES Lint 用于替换 TS Lint 以进行类型脚本和 JavaScript

## <a name="next-steps"></a>后续步骤

有关如何载入和安装 Microsoft 安全代码分析的说明，请参阅我们的[载入和安装指南](security-code-analysis-onboard.md)。

如果您对扩展和提供的工具有任何疑问，请查看我们的[常见问题页面](security-code-analysis-faq.md)。

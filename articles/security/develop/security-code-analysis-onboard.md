---
title: 微软安全代码分析载入指南
description: 本文介绍安装 Microsoft 安全代码分析扩展
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
ms.openlocfilehash: 6132aab98cc8145cb99cf153c64f20fbac00131c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196999"
---
# <a name="onboarding-and-installing"></a>载入和安装

开始使用 Microsoft 安全代码分析的先决条件：

- 符合以下部分的 Microsoft 统一支持产品。
- Azure DevOps 组织。
- 安装扩展到 Azure DevOps 组织的权限。
- 可同步到云托管的 Azure DevOps 管道的源代码。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>加入 Microsoft 安全代码分析扩展

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>有兴趣购买 Microsoft 安全代码分析扩展？

如果您有以下支持产品之一，请联系您的技术客户经理购买或交换现有时间以访问扩展：

- 统一支持高级层
- 统一支持绩效层
- 对开发人员的卓越支持
- 对合作伙伴的卓越支持
- 企业卓越支持

如果您没有上述支持协议之一，您可以从我们的合作伙伴之一购买延期。

**后续步骤：**

请从以下列表中联系合作伙伴，并要求购买 Microsoft 安全代码分析扩展。

>**合作 伙伴：**

- 区域 - 联系方式：cloudsupport@zones.com

### <a name="become-a-partner"></a>成为合作伙伴

Microsoft 安全代码分析团队希望通过合作伙伴支持高级协议加入合作伙伴。 合作伙伴将帮助 Azure DevOps 客户通过向希望购买扩展但未与 Microsoft 签订企业支持协议的客户销售扩展，从而功能更安全地开发。 有兴趣的合作伙伴可以[在这里](http://www.microsoftpartnersupport.com/msrd/opin)注册。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安装 Microsoft 安全代码分析扩展

1. 与 Azure DevOps 组织共享扩展后，转到 Azure DevOps 组织页面。 此类页面的示例 URL 是`https://dev.azure.com/contoso`。
1. 选择您姓名旁边的右上角的购物袋图标，然后选择 **"管理扩展**"。
1. 选择 **"共享**"。
1. 选择 Microsoft 安全代码分析扩展名，选择**安装**。
1. 从下拉列表中，选择要安装扩展的 Azure DevOps 组织。
1. 选择**安装**。 安装完成后，您可以开始使用扩展。

>[!NOTE]
> 即使您没有安装扩展的访问权限，请继续安装步骤。 您可以在安装过程中向 Azure DevOps 组织管理员请求访问权限。

安装扩展后，安全开发生成任务可见，可用于添加到 Azure 管道中。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>向 Azure DevOps 管道添加特定的生成任务

1. 从 Azure DevOps 组织中，打开团队项目。
1. 选择**管道** > **生成**。
1. 选择要向其添加扩展生成任务的管道：
   - 新管道：选择 **"新建"，** 然后按照详细步骤创建新管道。
   - 编辑管道：选择现有管道，然后选择 **"编辑"** 以开始编辑管道。
1. 选择**+** 并转到"**添加任务"** 窗格。
1. 从列表中或使用搜索框查找要添加的生成任务。 选择“添加”****。
1. 指定任务所需的参数。
1. 将新的生成排队。
   >[!NOTE]
   >文件和文件夹路径与源存储库的根目录相关。 如果将输出文件和文件夹指定为参数，则它们将被替换到我们在生成代理上定义的公共位置。

> [!TIP]
>
> - 要在生成后运行分析，请将 Microsoft 安全代码分析生成任务放在生成发布的生成项目步骤之后。 这样，您的生成就可以在运行静态分析工具之前完成并发布结果。
> - 始终为安全开发生成任务选择 **"出错时继续**"。 即使一个工具出现故障，其他工具也可以运行。 工具之间没有相互依赖性。
> - 仅当工具无法成功运行时，Microsoft 安全代码分析生成任务才会失败。 但是，即使工具识别代码中的问题，它们也会成功。 通过使用"分析后"生成任务，可以将生成配置为在工具识别代码中的问题时失败。
> - 通过发布管道运行时，不支持某些 Azure DevOps 生成任务。 更具体地说，Azure DevOps 不支持在发布管道中发布项目的任务。
> - 有关可以指定为参数的 Azure DevOps 团队生成中预定义变量的列表，请参阅[Azure DevOps 生成变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)。

## <a name="next-steps"></a>后续步骤

有关配置生成任务的详细信息，请参阅我们的[配置指南](security-code-analysis-customize.md)或[YAML 配置指南](yaml-configuration.md)。

如果您对扩展和提供的工具有任何疑问，请查看我们的[常见问题页面](security-code-analysis-faq.md)。

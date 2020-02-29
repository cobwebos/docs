---
title: Microsoft 安全代码分析载入指南
description: 本文介绍如何安装 Microsoft 安全代码分析扩展
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196999"
---
# <a name="onboarding-and-installing"></a>载入和安装

Microsoft 安全代码分析入门的先决条件：

- 符合条件的 Microsoft 统一支持产品，如以下部分中所述。
- Azure DevOps 组织。
- 将扩展安装到 Azure DevOps 组织的权限。
- 可同步到云托管的 Azure DevOps 管道的源代码。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>载入 Microsoft 安全代码分析扩展

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>对购买 Microsoft 安全代码分析扩展感兴趣吗？

如果你有以下支持服务之一，请联系你的技术客户经理，以购买或交换现有小时以访问扩展：

- 统一支持高级层
- 统一的支持性能级别
- 面向开发人员的顶级支持
- 合作伙伴顶级支持
- 适用于企业的顶级支持

如果你没有上述支持协议中的一种，你可以从我们的合作伙伴之一购买此扩展。

**后续步骤：**

与以下列表中的合作伙伴联系，要求购买 Microsoft 安全代码分析扩展。

>**群体**

- 区域-联系人详细信息： cloudsupport@zones.com

### <a name="become-a-partner"></a>成为合作伙伴

Microsoft 安全代码分析团队正在寻求加入合作伙伴协议顶级支持的加入合作伙伴。 合作伙伴将通过向要购买客户的客户销售扩展来帮助 Azure DevOps 客户更安全地进行开发，但不会与 Microsoft 建立企业支持协议。 相关合作伙伴可[在此处](http://www.microsoftpartnersupport.com/msrd/opin)注册。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安装 Microsoft 安全代码分析扩展

1. 将扩展与 Azure DevOps 组织共享后，请参阅 Azure DevOps 组织页。 `https://dev.azure.com/contoso`此类页面的示例 URL。
1. 选择名称旁边的右上角的 "购物袋" 图标，然后选择 "**管理扩展**"。
1. 选择 "**共享**"。
1. 选择 Microsoft 安全代码分析扩展，然后选择 "**安装**"。
1. 从下拉列表中，选择要在其上安装扩展的 Azure DevOps 组织。
1. 选择“安装”。 安装完成后，可以开始使用扩展。

>[!NOTE]
> 即使您没有安装该扩展的访问权限，也可以继续执行安装步骤。 你可以在安装过程中请求 Azure DevOps 组织管理员的访问权限。

安装该扩展后，安全开发生成任务将可见，并且可添加到 Azure Pipelines 中。

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>将特定生成任务添加到 Azure DevOps 管道

1. 在 Azure DevOps 组织中打开团队项目。
1. 选择 > **生成**的**管道**。
1. 选择要向其中添加扩展生成任务的管道：
   - 新管道：选择 "**新建**"，然后按照详细介绍的步骤创建新管道。
   - 编辑管道：选择现有管道，然后选择 "**编辑**" 开始编辑管道。
1. 选择 **+** 并中转到 "**添加任务**" 窗格。
1. 从列表或使用 "搜索" 框中，找到要添加的生成任务。 选择 **添加** 。
1. 指定任务所需的参数。
1. 将新的生成排队。
   >[!NOTE]
   >文件和文件夹路径相对于源存储库的根目录。 如果将输出文件和文件夹指定为参数，则会将它们替换为在生成代理上定义的常见位置。

> [!TIP]
>
> - 若要在生成后运行分析，请将 Microsoft 安全代码分析生成任务置于生成的 "发布生成项目" 步骤之后。 这样一来，您的生成就可以在运行静态分析工具之前完成并发布结果。
> - 对于安全开发生成任务，始终选择 **"出错时继续"** 。 即使某个工具出现故障，其他工具也能运行。 工具之间没有任何依赖关系。
> - 仅当工具无法成功运行时，Microsoft 安全代码分析生成任务才会失败。 但即使工具在代码中发现问题，它们仍会成功。 使用后期分析生成任务，可以将生成配置为在工具识别代码中的问题时失败。
> - 通过发布管道运行时，不支持某些 Azure DevOps 生成任务。 更具体地说，Azure DevOps 不支持在发布管道内发布项目的任务。
> - 有关可指定为参数的 Azure DevOps Team Build 中预定义变量的列表，请参阅[Azure DevOps Build variables](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)。

## <a name="next-steps"></a>后续步骤

有关配置生成任务的详细信息，请参阅我们的[配置指南](security-code-analysis-customize.md)或[YAML 配置指南](yaml-configuration.md)。

如果你有关于扩展和提供的工具的更多问题，请查看我们的[常见问题页面](security-code-analysis-faq.md)。

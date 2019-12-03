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
ms.openlocfilehash: 77be6d8518774a1e2656e23024490dd9c1431865
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707254"
---
# <a name="onboarding-and-installing"></a>载入和安装

Microsoft 安全代码分析入门的先决条件：

- 符合条件的 Microsoft 统一支持产品，如以下部分中所述。
- Azure DevOps 组织。
- 将扩展安装到 Azure DevOps 组织的权限。
- 可同步到云托管的 Azure DevOps 管道的源代码。

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>载入 Microsoft 安全代码分析扩展

- 如果你有以下支持服务之一，请联系你的技术客户经理，以购买或交换现有小时以访问扩展：
   - 统一支持高级层
   - 统一的支持性能级别
   - 面向开发人员的顶级支持
   - 合作伙伴顶级支持
   - 适用于企业的顶级支持
- 如果你安装了以下支持服务之一或没有 Microsoft 支持计划，则必须升级到符合条件的支持产品/服务：
   - 针对合作伙伴的 Azure 支持
   - Azure 基本支持
   - Azure 开发人员支持
   - Azure 标准支持
   - Azure 专业直接
   - 统一的支持核心层
- 若要购买合格的支持产品/服务，请参阅我们的[支持服务主页](https://www.microsoft.com/enterprise/services/support)。
- 支持合同准备就绪后，请与你的技术客户经理联系以帮助你开始使用并帮助你收集所有必需的详细信息。

>[!NOTE]
> 如果你是在 Microsoft 合作伙伴网络中注册的合作伙伴，则有资格购买顶级支持合作伙伴。 否则，你必须购买前面提到的一个符合要求的支持产品。

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>安装 Microsoft 安全代码分析扩展

1. 将扩展与 Azure DevOps 组织共享后，请参阅 Azure DevOps 组织页。 `https://dev.azure.com/contoso`此类页面的示例 URL。
1. 选择名称旁边的右上角的 "购物袋" 图标，然后选择 "**管理扩展**"。
1. 选择 "**共享**"。
1. 选择 Microsoft 安全代码分析扩展，然后选择 "**安装**"。
1. 从下拉列表中，选择要在其上安装扩展的 Azure DevOps 组织。
1. 选择“安装”。 安装完成后，可以开始使用扩展。

>[!NOTE]
> 即使您没有安装该扩展的访问权限，也可以继续执行安装步骤。 你可以在安装过程中请求 Azure DevOps 组织管理员的访问权限。

安装该扩展后，安全开发生成任务将可见，并且可添加到 Azure 管道。

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

有关配置生成任务的详细信息，请参阅我们的[配置指南](security-code-analysis-customize.md)。

如果你有关于扩展和提供的工具的更多问题，请查看我们的[常见问题页面](security-code-analysis-faq.md)。

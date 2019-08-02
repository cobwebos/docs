---
title: Microsoft Azure 安全代码分析载入指南
description: 本文介绍如何安装安全代码分析扩展
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
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718296"
---
# <a name="how-to-onboarding-and-installing"></a>如何：载入和安装

Microsoft 安全代码分析入门的先决条件
  - 合格的 Microsoft 统一支持服务产品 (下面的详细信息)
  - Azure DevOps 组织
  - 将扩展安装到 Azure DevOps 组织的权限
  - 可同步到云托管的 Azure DevOps 管道的源代码


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>加入 Microsoft 安全代码分析扩展

- 如果你已拥有以下支持服务之一, 只需联系你的技术客户经理并 purchase\swap 现有的时间, 即可访问该扩展。
   - 统一支持–高级和性能层, 顶级支持面向开发人员、顶级支持用于合作伙伴, 或适用于企业的顶级支持。
- 如果你拥有以下支持服务之一或没有 Microsoft 的任何支持计划, 你将需要升级到符合条件的支持产品/服务:
   - 面向合作伙伴、Azure 基本、Azure 开发人员、Azure 标准、Azure 专业直接或统一支持–核心层的 azure 支持
- 若要购买合格的支持产品/服务, 请访问我们的[支持服务主页](https://www.microsoft.com/enterprise/services/support)
- 支持合同准备就绪后, 请与你的技术客户经理联系以帮助你入门, 并收集所有所需的详细信息。
 
>[!NOTE]
> 只有 Microsoft 合作伙伴网络中的注册合作伙伴有资格购买顶级支持合作伙伴, 否则请购买前面提到的一项符合条件的支持产品

## <a name="installing-microsoft-security-code-analysis-extension"></a>正在安装 Microsoft 安全代码分析扩展

1. 将扩展与 Azure DevOps 组织共享后, 导航到 Azure DevOps 组织页面 (例如 http://dev.azure.com/contoso)
2. 单击名称旁边的右上角的 "购物袋" 图标, 然后单击 "管理扩展" 
3. 单击 Microsoft 安全代码分析扩展, 并启动 Azure DevOps UI 向导来开始安装。
4. 从下拉列表中选择要安装扩展的 Azure DevOps 组织
5. 单击 "安装"。 完成后, 可以继续使用扩展

>[!NOTE]
> 即使你没有访问权限, 也可以继续执行安装步骤, 并且在该过程中, 你将能够请求 Azure DevOps 组织管理员的访问权限。
>
安装扩展后, 安全开发生成任务将可见, 并且可添加到 Azure Pipelines 中。

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>将特定生成任务添加到 DevOps 管道

1. 从 Azure DevOps 组织打开你的团队项目。
2. 导航到**管道**下的 "**生成**" 选项卡 
3. 选择要向其中添加扩展生成任务的管道。 
   - 新建-单击 "**新建**", 然后按照详细介绍的步骤创建新管道。
   - 编辑-选择管道, 然后单击 "**编辑**" 开始编辑现有管道。
4. 单击 "+" 以导航到 "**添加任务**" 窗格。
5. 从列表中或使用搜索框查找要添加的生成任务, 然后单击 "**添加**"。 
6. 现在, 你可以继续指定任务所需的参数。
>[!NOTE]
>文件或目录路径相对于源存储库的根目录, 指定输出文件夹/文件的参数将替换为在生成代理上定义的常见位置。

7. 将新的生成排队。
> [!TIP]
>  - 若要在生成后运行分析, 请将 Microsoft 安全代码分析生成任务置于生成的 "发布生成项目" 步骤之后。 这样一来, 您的生成就可以在运行静态分析工具之前完成并发布结果。
>  - 请始终检查安全开发生成任务的 **"出错时继续"** 选项。 即使某个工具出现故障, 其他人也可以运行。 没有相互依赖关系。
>  - 仅当工具无法成功运行时, Microsoft 安全代码分析生成任务才会失败, 但如果以及当工具发现代码中的问题时, 它们将不会失败。 使用**后期分析**生成任务, 可以将生成配置为在工具识别代码中的问题时中断。
>  - 通过 "Release" 管道运行时, 不支持某些 Azure DevOps 生成任务。 这是 Azure DevOps 的限制。 它们不支持从发布管道内发布项目的任务。
>  - 有关 Azure DevOps Team Build 中可指定为参数的预定义变量的列表, 请参阅[Azure DevOps Build 变量](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)

## <a name="next-steps"></a>后续步骤

有关配置生成任务的详细信息, 请参阅我们的[配置指南](security-code-analysis-customize.md)

如果对扩展和所提供的工具有更多的问题, 请[查看我们的 "常见问题" 页。](security-code-analysis-faq.md)



---
title: "从 Orchestrator 迁移到 Azure 自动化 | Microsoft Docs"
description: "介绍如何将 Runbook 和集成包从 System Center Orchestrator 迁移到 Azure 自动化。"
services: automation
documentationcenter: 
author: bwren
manager: stevenka
editor: tysonn
ms.assetid: 1a7da58c-7a98-49b5-9d9d-001a9f6e631a
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: bwren
ms.openlocfilehash: 457888b4d38875b912ad87d44e96ab727e3ee3ee
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>从 Orchestrator 迁移到 Azure 自动化 (Beta)
[System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) 中的 Runbook 基于专为 Orchestrator 编写的集成包中的活动，而 Azure 自动化中的 Runbook 则基于 Windows PowerShell。  Azure 自动化中的[图形 Runbook](automation-runbook-types.md#graphical-runbooks) 具有的外观类似于其活动用于表示 PowerShell cmdlet、子 Runbook 和资产的 Orchestrator Runbook。


            [System Center Orchestrator 迁移工具包](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) 包含的工具可帮助将 Runbook 从 Orchestrator 转换为 Azure 自动化。  除了转换 Runbook 本身，还必须将包含 Runbook 所用活动的集成包转换为包含 Windows PowerShell cmdlet 的集成模块。  

下面是将 Orchestrator Runbook 转换为 Azure 自动化的基本过程。  这每个步骤都在下面的相应部分进行了详细介绍。

1. 下载包含本文讨论的工具和模块的 [System Center Orchestrator 迁移工具包](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all)。
2. 将[标准活动模块](#standard-activities-module)导入 Azure 自动化中。  这包括标准 Orchestrator 活动的转换后版本，可供已转换的 Runbook 使用。
3. 针对访问 System Center 的 Runbook 使用的集成包，将 [System Center Orchestrator 集成模块](#system-center-orchestrator-integration-modules)导入到 Azure 自动化中。
4. 使用[集成包转换器](#integration-pack-converter)转换自定义的和第三方的集成包，并导入到 Azure 自动化中。
5. 使用 [Runbook 转换器](#runbook-converter)来转换 Orchestrator Runbook，并在 Azure 自动化中进行安装。
6. 由于 Runbook 转换器不转换这些资源，因此请在 Azure 自动化中手动创建所需的 Orchestrator 资产。
7. 在本地的数据中心配置[混合 Runbook 辅助角色](#hybrid-runbook-worker)，运行将要访问本地资源、经转换的 Runbook。

## <a name="service-management-automation"></a>Service Management 自动化
[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) 在本地数据中心（如 Orchestrator）运行 Runbook，并使用相同的集成模块（如 Azure 自动化）。 [Runbook 转换器](#runbook-converter)将 Orchestrator Runbook 转换为图形 Runbook，虽然 SMA 中并不支持图形 Runbook。  仍可将[标准活动模块](#standard-activities-module)和 [System Center Orchestrator 集成模块](#system-center-orchestrator-integration-modules)安装到 SMA，但是必须手动[重新编写 Runbook](http://technet.microsoft.com/library/dn469262.aspx)。

## <a name="hybrid-runbook-worker"></a>混合 Runbook 辅助角色
Orchestrator 中的 Runbook 存储在数据库服务器上，运行在 Runbook 服务器上，这两种服务器都位于本地数据中心。  Azure 自动化中的 Runbook 存储在 Azure 云中，并可使用[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)运行在本地数据中心。  这是通常情况下运行从 Orchestrator 转换过来的 Runbook 的方式，因为这些 Runbook 是设计在本地服务器上运行的。

## <a name="integration-pack-converter"></a>集成包转换器
集成包转换器会将使用 [Orchestrator 集成工具包 (OIT)](http://technet.microsoft.com/library/hh855853.aspx) 创建的集成包转换成基于 Windows PowerShell 的集成模块，该模块可导入 Azure 自动化或 Service Management Automation。  

运行集成包转换器时，系统会显示一个向导，可以通过该向导选择集成包 (.oip) 文件。  然后，该向导会列出该集成包中包括的活动，并允许选择要迁移的活动。  完成向导的操作后，向导会创建一个集成模块，其中包含原始集成包中每个活动的相应 cmdlet。

### <a name="parameters"></a>Parameters
集成包中活动的任何属性都会转换为集成模块中相应 cmdlet 的参数。  Windows PowerShell cmdlet 有一组可以用于所有 cmdlet 的[通用参数](http://technet.microsoft.com/library/hh847884.aspx)。  例如，-Verbose 参数会导致 cmdlet 输出关于其操作的详细信息。  cmdlet 的参数与通用参数不能有相同的名称。  如果某个活动的属性与通用参数具有相同的名称，向导会提示为参数提供另一个名称。

### <a name="monitor-activities"></a>监视活动
在 Orchestrator 中监视 Runbook 以[监视活动](http://technet.microsoft.com/library/hh403827.aspx)开头，并会持续运行，等待被特定事件调用。  Azure 自动化不支持监视 Runbook，因此集成包中的任何监视活动都不会进行转换。  与之相反，系统会在集成模块中为监视活动创建一个占位符 cmdlet。  此 cmdlet 没有任何功能，但可以通过它来安装使用它的任何已转换 Runbook。  此 Runbook 不能在 Azure 自动化中运行，但可以进行安装，因此可以对其进行修改。

### <a name="integration-packs-that-cannot-be-converted"></a>不能转换的集成包
不是使用 OIT 创建的集成包无法使用集成包转换器来进行转换。 另外还有一些 Microsoft 提供的集成包目前无法使用此工具转换。  这些集成包的已转换版本[已提供下载](#system-center-orchestrator-integration-modules) ，以便可以将它们安装在 Azure 自动化或 Service Management Automation 中。

## <a name="standard-activities-module"></a>标准活动模块
Orchestrator 包括一组[标准活动](http://technet.microsoft.com/library/hh403832.aspx)，这些活动未包括在集成包中，而是由多个 Runbook 使用。  “标准活动”模块是一个集成模块，其中包含每个此类活动的 cmdlet 等效项。  在导入任何使用标准活动的已转换 Runbook 之前，必须在 Azure 自动化中安装此集成模块。

除了支持转换后的 Runbook，标准活动模块中的 cmdlet 还可由熟悉 Orchestrator 的人用来在 Azure 自动化中构建新的 Runbook。  虽然可以使用 cmdlet 来执行所有标准活动的功能，但这些活动可能会以不同方式运行。  转换后的标准活动模块中的 cmdlet 的工作方式与其相应活动的工作方式相同，并使用相同的参数。  这可以帮助现在的 Orchestrator Runbook 作者过渡到 Azure 自动化 Runbook。

## <a name="system-center-orchestrator-integration-modules"></a>System Center Orchestrator 集成模块
Microsoft 提供[集成包](http://technet.microsoft.com/library/hh295851.aspx)用于构建 Runbook 来自动化 System Center 组件和其他产品。  其中的某些集成包目前基于 OIT，但由于已知的问题，无法转换为集成模块。  
            [System Center Orchestrator 集成模块](https://www.microsoft.com/download/details.aspx?id=49555)包含这些集成包的已转换版本，可以将这些包导入 Azure 自动化和 Service Management Automation 中。  

在发布此工具的 RTM 版本时，将发布可使用集成包转换器转换、基于 OIT 的集成包的更新版本。  此外，还会提供指导以帮助你使用来自非基于 OIT 的集成包的活动转换 Runbook。

## <a name="runbook-converter"></a>Runbook 转换器
Runbook 转换器将 Orchestrator Runbook 转换为可导入 Azure 自动化的[图形 Runbook](automation-runbook-types.md#graphical-runbooks)。  

Runbook 转换器使用可执行转换、名为 **ConvertFrom-SCORunbook** 的 cmdlet 实现为 PowerShell 模块。  安装该工具后，它创建可加载 cmdlet 的 PowerShell 会话的快捷方式。   

下面是将 Orchestrator Runbook 转换并导入 Azure 自动化的基本过程。  以下部分提供了有关使用该工具和处理已转换 Runbook 的更多详细信息。

1. 从 Orchestrator 导出一个或多个 Runbook。
2. 获取 Runbook 中所有活动的集成模块。
3. 转换所导出文件中的 Orchestrator Runbook。
4. 查看日志中的信息，以验证转换并确定任何所需的手动任务。
5. 将转换的 Runbook 导入 Azure 自动化。
6. 在 Azure 自动化中创建任何所需的资产。
7. 在 Azure 自动化中编辑 Runbook，以修改任何所需的活动。

### <a name="using-runbook-converter"></a>使用 Runbook 转换器
**ConvertFrom-SCORunbook** 的语法如下：

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>

* RunbookPath - 包含要转换的 Runbook 的导出文件的路径。
* Module - 包含 Runbook 中活动的集成模块的逗号分隔列表。
* OutputFolder - 用于创建要转换的图形 Runbook 的文件夹的路径。

以下示例命令在名为 **MyRunbooks.ois_export** 的导出文件中转换 Runbook。  这些 Runbook 使用 Active Directory 和 Data Protection Manager 集成包。

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"


### <a name="log-files"></a>日志文件
Runbook 转换器在与转换的 Runbook 所在的相同位置创建以下日志文件。  如果文件已存在，则以最后一个转换的信息进行覆盖。

| 文件 | 内容 |
|:--- |:--- |
| Runbook 转换器 - Progress.log |转换的详细步骤，包括成功转换的每个活动的信息，以及未转换的每个活动的警告。 |
| Runbook 转换器 - Summary.log |最后一个转换的摘要，包括任何警告，以及需要执行的后续任务，例如，创建转换的 Runbook 所需的变量。 |

### <a name="exporting-runbooks-from-orchestrator"></a>从 Orchestrator 导出 Runbook
Runbook 转换器可以处理 Orchestrator 中包含一个或多个 Runbook 的导出文件。  它将为导出文件中的每个 Orchestrator Runbook 创建相应的 Azure 自动化 Runbook。  

要从 Orchestrator 导出 Runbook，请在 Runbook 设计器中右键单击 Runbook 名称，并选择“导出”。  要导出文件夹中的所有 Runbook，请右键单击文件夹名称，并选择“导出”。

### <a name="runbook-activities"></a>Runbook 活动
Runbook 转换器将 Orchestrator Runbook 中的每个活动转换成 Azure 自动化中的相应活动。  对于无法转换的活动，会在 Runbook 中创建占位符活动，并提供警告文本。  将转换的 Runbook 导入 Azure 自动化之后，必须将这些活动替换为可执行所需功能的有效活动。

将转换[标准活动模块](#standard-activities-module)中的所有 Orchestrator 活动。  不过，此模块中有一些标准 Orchestrator 活动不会转换。  例如，**发送平台事件**没有 Azure 自动化对等项，因为该事件特定于 Orchestrator。

[监视活动](https://technet.microsoft.com/library/hh403827.aspx)不会转换，因为它们在 Azure 自动化中没有对等项。  异常是[已转换集成包](#integration-pack-converter)中的监视活动，会将其转换成占位符活动。

如果使用 [modules](#integration-pack-converter) 参数提供集成模块路径，则转换来自 **已转换集成包** 的任何活动。  对于 System Center 集成包，可以使用 [System Center Orchestrator 集成模块](#system-center-orchestrator-integration-modules)。

### <a name="orchestrator-resources"></a>Orchestrator 资源
Runbook 转换器仅转换 Runbook，而不转换其他 Orchestrator 资源，例如计数器、变量或连接。  Azure 自动化不支持计数器。  支持变量和连接，但你必须手动创建它们。  日志文件会通知 Runbook 是否需要此类资源，并指定你需要在 Azure 自动化中创建哪些相应资源，才能让转换后的 Runbook 正常运行。

例如，Runbook 可能使用变量来填充活动中的特定值。  已转换的 Runbook 会转换该活动，并以与 Orchestrator 变量名称相同的名称指定 Azure 自动化中的变量资产。  这会记录在转换后创建的文件 **Runbook 转换器 - Summary.log** 中。  在使用 Runbook 之前，需要在 Azure 自动化中手动创建此变量资产。

### <a name="input-parameters"></a>输入参数
Orchestrator 中的 Runbook 接受具有**初始化数据**活动的输入参数。  如果要转换的 Runbook 包含此活动，则在 Azure 自动化 Runbook 中为活动中的每个参数创建[输入参数](automation-graphical-authoring-intro.md#runbook-input-and-output)。  在检索并返回每个参数的转换 Runbook 中创建[工作流脚本控制](automation-graphical-authoring-intro.md#activities)活动。  Runbook 中使用输入参数的任何活动都会引用此活动的输出。

使用此策略的原因是能够最好地镜像 Orchestrator Runbook 中的功能。  新的图形 Runbook 中的活动应使用 Runbook 输入数据源直接引用输入参数。

### <a name="invoke-runbook-activity"></a>调用 Runbook 活动
Orchestrator 中的 Runbook 使用**调用 Runbook** 活动来启动其他 Runbook。 如果要转换的 Runbook 包含此活动，并且设置了“等待完成”选项，则在转换后的 Runbook 中为其创建 Runbook 活动。  如果未设置“等待完成”选项，则创建使用 **Start-AzureAutomationRunbook** 的工作流脚本活动来启动 Runbook。  将转换的 Runbook 导入 Azure 自动化之后，必须以活动中指定的信息修改此活动。

## <a name="related-articles"></a>相关文章
* [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)
* [Orchestrator 标准活动](http://technet.microsoft.com/library/hh403832.aspx)
* [下载 System Center Orchestrator 迁移工具包](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

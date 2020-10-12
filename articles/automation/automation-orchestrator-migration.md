---
title: 从 Orchestrator 迁移到 Azure 自动化 (Beta)
description: 本文介绍如何将 Runbook 和集成包从 Orchestrator 迁移到 Azure 自动化。
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: a47f720344a16d0f77559d6aabfb2b0245e62976
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89426327"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>从 Orchestrator 迁移到 Azure 自动化 (Beta)

[System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) 中的 Runbook 基于专为 Orchestrator 编写的集成包中的活动，而 Azure 自动化中的 Runbook 则基于 Windows PowerShell。 Azure 自动化中的[图形 Runbook](automation-runbook-types.md#graphical-runbooks) 具有的外观类似于其活动用于表示 PowerShell cmdlet、子 Runbook 和资产的 Orchestrator Runbook。 除了转换 Runbook 本身，还必须将包含 Runbook 所用活动的集成包转换为包含 Windows PowerShell cmdlet 的集成模块。 

[Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) 在本地数据中心（如 Orchestrator）运行 Runbook，并使用相同的集成模块（如 Azure 自动化）。 Runbook 转换器将 Orchestrator Runbook 转换为图形 Runbook，而 SMA 不支持图形 Runbook。 仍可将标准活动模块和 System Center Orchestrator 集成模块安装到 SMA，但是必须手动[重新编写 Runbook](/system-center/sma/authoring-automation-runbooks)。

## <a name="download-the-orchestrator-migration-toolkit"></a>下载 Orchestrator 迁移工具包

迁移的第一步是下载 [System Center Orchestrator 迁移工具包](https://www.microsoft.com/download/details.aspx?id=47323)。 此工具包包含有助于将 Runbook 从 Orchestrator 转换为 Azure 自动化的工具。  

## <a name="import-the-standard-activities-module"></a>导入标准活动模块

将[标准活动模块](/system-center/orchestrator/standard-activities?view=sc-orch-2019)导入 Azure 自动化中。 这包括转换后的图形 runbook 可以使用的标准 Orchestrator 活动的转换版本。

## <a name="import-orchestrator-integration-modules"></a>导入 Orchestrator 集成模块

Microsoft 提供[集成包](/previous-versions/system-center/packs/hh295851(v=technet.10))用于构建 Runbook 来自动化 System Center 组件和其他产品。 其中的某些集成包目前基于 OIT，但由于已知的问题，无法转换为集成模块。 针对访问 System Center 的 Runbook 使用的集成包，将 [System Center Orchestrator 集成模块](https://www.microsoft.com/download/details.aspx?id=49555)导入到 Azure 自动化中。 此包包含这些集成包的已转换版本，可以将其导入 Azure 自动化和 Service Management Automation。  

## <a name="convert-integration-packs"></a>转换集成包

使用[集成包转换器](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard?view=sc-orch-2019)将使用 [Orchestrator Integration Toolkit (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) 创建的任何集成包转换为可导入到 Azure 自动化或 Service Management Automation 中的基于 PowerShell 的集成模块。 运行集成包转换器时，系统会显示一个向导，可以通过该向导选择集成包 (.oip) 文件。 然后，该向导会列出该集成包中包括的活动，并允许选择要迁移的活动。 完成向导的操作后，向导会创建一个集成模块，其中包含原始集成包中每个活动的相应 cmdlet。

> [!NOTE]
> 不能使用集成包转换器转换未使用 OIT 创建的集成包。 另外还有一些 Microsoft 提供的集成包目前无法使用此工具转换。 已支持下载这些集成包的已转换版本，以便将其安装在 Azure 自动化或 Service Management Automation 中。

### <a name="parameters"></a>参数

集成包中活动的任何属性都将转换为集成模块中相应 cmdlet 的参数。  Windows PowerShell cmdlet 有一组可以用于所有 cmdlet 的[通用参数](/powershell/module/microsoft.powershell.core/about/about_commonparameters)。 例如，-Verbose 参数会导致 cmdlet 输出关于其操作的详细信息。  cmdlet 的参数与通用参数不能有相同的名称。 如果某个活动的属性与通用参数具有相同的名称，向导会提示为参数提供另一个名称。

### <a name="monitor-activities"></a>监视活动

在 Orchestrator 中监视 Runbook 以[监视活动](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12))开头，并会持续运行，等待被特定事件调用。 Azure 自动化不支持监视 Runbook，因此集成包中的任何监视活动都不会进行转换。 与之相反，系统会在集成模块中为监视活动创建一个占位符 cmdlet。  此 cmdlet 没有任何功能，但可以通过它来安装使用它的任何已转换 Runbook。 此 Runbook 将不能在 Azure 自动化中运行，但可以进行安装，因此可以对其进行修改。

Orchestrator 包括一组[标准活动](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))，这些活动未包括在集成包中，而是由多个 Runbook 使用。  “标准活动”模块是一个集成模块，其中包含每个此类活动的 cmdlet 等效项。 在导入任何使用标准活动的已转换 Runbook 之前，必须在 Azure 自动化中安装此集成模块。

除了支持转换后的 Runbook，标准活动模块中的 cmdlet 还可由熟悉 Orchestrator 的人用来在 Azure 自动化中构建新的 Runbook。 虽然可以使用 cmdlet 来执行所有标准活动的功能，但这些活动可能会以不同方式运行。 转换后的标准活动模块中的 cmdlet 的工作方式与其相应活动的工作方式相同，并使用相同的参数。 这可以帮助你过渡到 Azure 自动化 runbook。

## <a name="convert-orchestrator-runbooks"></a>转换 Orchestrator runbook

Orchestrator Runbook 转换器将 Orchestrator Runbook 转换为可导入 Azure 自动化的[图形 Runbook](automation-runbook-types.md#graphical-runbooks)。 Runbook 转换器是作为 PowerShell 模块实现的，其 cmdlet `ConvertFrom-SCORunbook` 用于进行转换。 安装转换器时，它会创建加载 cmdlet 的 PowerShell 会话的快捷方式。   

以下是转换 runbook 并将其导入 Azure 自动化的基本步骤。 本节稍后将提供有关使用 cmdlet 的详细信息。

1. 从 Orchestrator 导出一个或多个 Runbook。
2. 获取 Runbook 中所有活动的集成模块。
3. 转换所导出文件中的 Orchestrator Runbook。
4. 查看日志中的信息，以验证转换并确定任何所需的手动任务。
5. 将转换的 Runbook 导入 Azure 自动化。
6. 在 Azure 自动化中创建任何所需的资产。
7. 在 Azure 自动化中编辑 Runbook，以修改任何所需的活动。

`ConvertFrom-SCORunbook` 的语法为：

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - 包含要转换的 Runbook 的导出文件的路径。
* Module - 包含 Runbook 中活动的集成模块的逗号分隔列表。
* OutputFolder - 用于创建要转换的图形 Runbook 的文件夹的路径。

以下示例命令在名为 **MyRunbooks.ois_export** 的导出文件中转换 Runbook。  这些 Runbook 使用 Active Directory 和 Data Protection Manager 集成包。

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>使用 Runbook 转换器日志文件

Runbook 转换器在与转换的 Runbook 所在的相同位置创建以下日志文件。  如果文件已存在，则以最后一个转换的信息进行覆盖。

| 文件 | 目录 |
|:--- |:--- |
| Runbook 转换器 - Progress.log |转换的详细步骤，包括成功转换的每个活动的信息，以及未转换的每个活动的警告。 |
| Runbook 转换器 - Summary.log |最后一个转换的摘要，包括任何警告，以及需要执行的后续任务，例如，创建转换的 Runbook 所需的变量。 |

### <a name="export-runbooks-from-orchestrator"></a>从 Orchestrator 导出 Runbook

Runbook 转换器可以处理 Orchestrator 中包含一个或多个 Runbook 的导出文件。  它为导出文件中的每个 Orchestrator Runbook 创建相应的 Azure 自动化 Runbook。  

要从 Orchestrator 导出 Runbook，请在 Runbook 设计器中右键单击 Runbook 名称，并选择“导出”。  要导出文件夹中的所有 Runbook，请右键单击文件夹名称，并选择“导出”。

### <a name="convert-runbook-activities"></a>转换 runbook 活动

Runbook 转换器将 Orchestrator Runbook 中的每个活动转换成 Azure 自动化中的相应活动。  对于无法转换的活动，会在 Runbook 中创建占位符活动，并提供警告文本。  将转换的 Runbook 导入 Azure 自动化之后，必须将这些活动替换为可执行所需功能的有效活动。

将转换标准活动模块中的所有 Orchestrator 活动。 不过，此模块中有一些标准 Orchestrator 活动不会转换。 例如，`Send Platform Event` 没有 Azure 自动化对等项，因为该事件特定于 Orchestrator。

[监视活动](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12))不会转换，因为它们在 Azure 自动化中没有对等项。 异常是已转换集成包中的监视活动，会将其转换成占位符活动。

如果使用 `modules` 参数提供集成模块路径，将转换来自已转换集成包的任何活动。 对于 System Center 集成包，可以使用 System Center Orchestrator 集成模块。

### <a name="manage-orchestrator-resources"></a>管理 Orchestrator 资源

Runbook 转换器仅转换 Runbook，而不转换其他 Orchestrator 资源，例如计数器、变量或连接。  Azure 自动化不支持计数器。  支持变量和连接，但必须手动创建它们。 日志文件会通知 Runbook 是否需要此类资源，并指定需要在 Azure 自动化中创建哪些相应资源，才能让转换后的 Runbook 正常运行。

例如，Runbook 可能使用变量来填充活动中的特定值。  已转换的 Runbook 会转换该活动，并以与 Orchestrator 变量名称相同的名称指定 Azure 自动化中的变量资产。 此操作记录在转换后创建的文件“Runbook 转换器 - Summary.log”中。 在使用 Runbook 之前，必须在 Azure 自动化中手动创建此变量资产。

### <a name="work-with-orchestrator-input-parameters"></a>使用 Orchestrator 输入参数

Orchestrator 中的 Runbook 接受具有 `Initialize Data` 活动的输入参数。  如果要转换的 Runbook 包含此活动，则在 Azure 自动化 Runbook 中为活动中的每个参数创建[输入参数](automation-graphical-authoring-intro.md#handle-runbook-input)。  在检索并返回每个参数的转换 Runbook 中创建[工作流脚本控制](automation-graphical-authoring-intro.md#use-activities)活动。 Runbook 中使用输入参数的任何活动将引用此活动的输出。

使用此策略的原因是能够最好地镜像 Orchestrator Runbook 中的功能。  新的图形 Runbook 中的活动应使用 Runbook 输入数据源直接引用输入参数。

### <a name="invoke-runbook-activity"></a>调用 Runbook 活动

Orchestrator 中的 Runbook 使用 `Invoke Runbook` 活动来启动其他 Runbook。 如果要转换的 Runbook 包含此活动，并且设置了 `Wait for completion` 选项，则在转换后的 Runbook 中为其创建 Runbook 活动。  如果未设置 `Wait for completion`选项，则创建使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) 的工作流脚本活动来启动 Runbook。 将转换的 Runbook 导入 Azure 自动化之后，必须以活动中指定的信息修改此活动。

## <a name="create-orchestrator-assets"></a>创建 Orchestrator 资产

Runbook 转换器不转换 Orchestrator 资产。 必须在 Azure 自动化中手动创建任何必需的 Orchestrator 资产。

## <a name="configure-hybrid-runbook-worker"></a>配置混合 Runbook 辅助角色

Orchestrator 将 runbook 存储在数据库服务器上，并在本地数据中心的 runbook 服务器上运行它们。 Azure 自动化中的 Runbook 存储在 Azure 云中，并可使用[混合 Runbook 辅助角色](automation-hybrid-runbook-worker.md)运行在本地数据中心。 配置工作进程以运行从 Orchestrator 转换过来的 Runbook，因为这些 runbook 设计为在本地服务器上运行并访问本地资源。

## <a name="related-articles"></a>相关文章

* 有关 Orchestrator 的详细信息，请参阅 [System Center 2012 - Orchestrator](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))。
* 了解有关在 [Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) 中自动化服务管理的更多信息。
* Orchestrator 活动的详细信息可以在 [Orchestrator 标准活动](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))中找到。
* 若要获取 Orchestrator 迁移工具包，请参阅[下载 System Center Orchestrator 迁移工具包](https://www.microsoft.com/download/details.aspx?id=47323)。
* 有关 Azure 自动化混合 Runbook 辅助角色概述，请参阅[混合 Runbook 辅助角色概述](automation-hybrid-runbook-worker.md)。

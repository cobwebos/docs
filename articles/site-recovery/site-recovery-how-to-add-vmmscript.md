---
title: "如何在 Azure Site Recovery 中将脚本添加到恢复计划 | Microsoft Docs"
description: "介绍在 Azure 中将新脚本以 VMM 形式添加到恢复计划的先决条件"
services: site-recovery
documentationcenter: 
author: ruturaj
manager: shons
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/13/2017
ms.author: ruturaj
ms.openlocfilehash: 60c6eebd9323028c63f42bd8a0996e3c0a2a1cf1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="add-vmm-scripts-to-a-recovery-plan"></a>将 VMM 脚本添加到恢复计划


本文介绍如何在 [Azure Site Recovery](site-recovery-overview.md) 中创建恢复计划并为其添加 VMM 脚本。

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="prerequisites-of-adding-a-script-to-recovery-plan"></a>向恢复计划添加脚本的先决条件

可在恢复计划中使用 PowerShell 脚本。 需要创作脚本并将其置于 VMM 库，以便从恢复计划访问。 以下是编写脚本时的一些注意事项。

* 确保脚本使用的是 try-catch 块，以便恰当地处理异常。
    - 如果脚本中出现异常，则脚本将停止运行且任务显示为失败。
    - 如果发生错误，则脚本的剩余部分都不会运行。
    - 如果在运行未计划的故障转移时发生错误，将继续执行恢复计划。
    - 如果在运行计划的故障转移时发生错误，则将停止恢复计划。 需要修复脚本，检查它是否按预期运行，并重新运行恢复计划。
        - Write-Host 命令不适用于恢复计划脚本，脚本会失败。 若要创建输出，请创建转而运行主脚本的代理脚本。 确保所有输出均通过“>>”命令进行传输。
        - 如果脚本未在 600 秒内返回，则脚本超时。
        - 如果有任何内容写出到 STDERR，则脚本归类为失败。 此信息显示在脚本执行详细信息中。

* 恢复计划中的脚本在 VMM 服务帐户的上下文中运行。 确保此帐户可读取脚本所在的远程共享。 测试要在 VMM 服务帐户特权级别运行的脚本。
* Windows PowerShell 模块中随附了 VMM cmdlet。 安装 VMM 控制台时会随附安装模块。 可在脚本中通过以下命令将其加载到脚本中：
   - Import-Module -Name virtualmachinemanager。 [了解详细信息](https://technet.microsoft.com/library/hh875013.aspx)。
* 确保 VMM 部署中至少有一个库服务器。 VMM 服务器的库共享路径默认位于本地的 VMM 服务器，其文件夹名称为 MSCVMMLibrary。
    * 如果库共享路径在远程位置（或在本地，但不与 MSCVMMLibrary 共享），请按如下所示配置共享（例如使用 \\\libserver2.contoso.com\share\）：
      * 打开注册表编辑器并导航到 **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**。
      * 编辑值 **ScriptLibraryPath**，将其设置为 \\libserver2.contoso.com\share\. 指定完整的 FQDN。 提供对共享位置的权限。 请注意，这是共享的根节点。 **若要验证这点，可在 VMM 的根节点浏览库。打开的路径即为路径根 - 将在变量中使用。
      * 确保测试脚本时所用的用户帐户具有与 VMM 服务帐户相同的权限。 这会检查独立测试的脚本是否按其在恢复计划中的相同方式进行运行。 在 VMM 服务器上，将执行策略设置为绕过，如下所示：
        * 使用提升的权限打开 64 位 Windows PowerShell 控制台。
        * 键入：**Set-executionpolicy bypass**。 [了解详细信息](https://technet.microsoft.com/library/ee176961.aspx)。

> [!IMPORTANT]
> 应仅在 64 位 PowerShell 上将执行策略设置为“免验证”。 如果为 32 位 PowerShell 进行此设置，则不会执行脚本。

## <a name="add-the-script-to-the-vmm-library"></a>将脚本添加到 VMM 库

如果有一个 VMM 源站点，则可在 VMM 服务器上创建脚本，并将其纳入恢复计划。

1. 在库共享中新建文件夹。 例如，\<VMMServerName>\MSSCVMMLibrary\RPScripts。 将其放到源和目标 VMM 服务器上。
2. 创建脚本（例如，RPScript），并验证其按预期运行。
3. 将该脚本放在源和目标 VMM 服务器上的 \<VMMServerName>\MSSCVMMLibrary 位置。

## <a name="add-the-script-to-a-recovery-plan"></a>将脚本添加到恢复计划

向组添加 VM 或复制组并创建该计划后，即可向该组添加脚本。

1. 打开恢复计划。
2. 在“步骤”列表中单击某项，并单击“脚本”或“手动操作”。
3. 指定是要在选定项的前面还是后面添加该脚本或操作。 使用“上移”和“下移”按钮，上下移动脚本的位置。
4. 如果添加 VMM 脚本，请选择“故障转移到 VMM 脚本”。 在“脚本路径”中，键入共享的相对路径。 在以下 VMM 示例中指定路径：**\RPScripts\RPScript.PS1**。
5. 要添加 Azure 自动化 Runbook，请指定该 Runbook 所在的 Azure 自动化帐户，并选择相应的 Azure Runbook 脚本。
6. 执行恢复计划的测试故障转移，确保脚本按预期运行。


## <a name="next-steps"></a>后续步骤

[详细了解](site-recovery-failover.md)如何运行故障转移。

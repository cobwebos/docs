---
title: 在 Azure Site Recovery 中将脚本添加到还原计划 | Microsoft Docs
description: 了解在 Azure 中将新的 System Center Virtual Machine Manager (VMM) 脚本添加到还原计划的先决条件。
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: rochakm
editor: ''
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 71991347ffaf036065aae9e1a93b7eb83a14b15c
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917316"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>将 VMM 脚本添加到还原计划

本文介绍了如何创建 System Center Virtual Machine Manager (VMM) 脚本且如何在 [Azure Site Recovery](site-recovery-overview.md) 中将其添加到还原计划中。

请在本文底部或在 [Azure 还原服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)上发布评论或填写疑惑。

## <a name="prerequisites"></a>先决条件

可在恢复计划中使用 PowerShell 脚本。 必须编写脚本并将脚本置于 VMM 库中，才可从还原计划中进行访问。 编写脚本时，请记住以下注意事项：

* 确保脚本使用的是 try-catch 块，以便恰当地处理异常。
    - 如果脚本中出现异常，脚本将停止运行，且任务显示为失败。
    - 如果发生错误，则不运行脚本的剩余部分。
    - 如果在运行未计划的故障转移时发生错误，将继续执行恢复计划。
    - 如果在运行计划的故障转移时发生错误，则将停止恢复计划。 请修复脚本，检查它是否按预期运行，然后重新运行还原计划。
        - `Write-Host` 命令在还原计划脚本中不起作用。 如果在脚本中使用 `Write-Host`，则脚本将失败。 若要创建输出，请创建转而运行主脚本的代理脚本。 请使用 \>\> 命令来确保所有输出均已传输。
        - 如果脚本未在 600 秒内返回，则脚本超时。
        - 如果向 STDERR 写入了内容，则脚本归类为失败。 此信息显示在脚本执行详细信息中。

* 需使用 VMM 服务帐户才能运行还原计划中的脚本。 确保此帐户可读取包含脚本的远程共享。 测试要运行的脚本与 VMM 服务帐户处于同一用户权限级别。
* Windows PowerShell 模块中随附了 VMM cmdlet。 安装 VMM 控制台时会随附安装模块。 要将模块加载到脚本中，请在脚本中使用以下命令： 

    `Import-Module -Name virtualmachinemanager`

    有关详细信息，请参阅 [Windows PowerShell 和 VMM 入门](https://technet.microsoft.com/library/hh875013.aspx)。
* 确保 VMM 部署中至少有一个库服务器。 VMM 服务器的库共享路径默认位于本地的 VMM 服务器。 其文件夹名称为 MSCVMMLibrary。

  如果库共享路径在远程位置（或在本地但不与 MSCVMMLibrary 共享），请按如下所示配置共享（例如使用 \\libserver2.contoso.com\share\）：
  
  1. 打开注册表编辑器，然后转到HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration。

  2. 将 ScriptLibraryPath 的值更改为 \\\libserver2.contoso.com\share\\。 指定完整的 FQDN。 提供对共享位置的权限。 指共享的根节点。 要在 VMM 中查看根节点，请转到库中的根节点。 打开的路径就是路径的根。 必须在变量中使用此路径。

  3. 使用与 VMM 服务帐户处于同一用户权限级别的用户帐户测试脚本。 使用这些用户权限验证经过测试的独立脚本按在还原计划中运行的方式运行。 在 VMM 服务器上，将执行策略设置为绕过，如下所示：

     a. 以管理员身份打开 64 位 Windows PowerShell 控制台。
     
     b. 输入 Set-executionpolicy bypass。 有关详细信息，请参阅[使用 Set-ExecutionPolicy cmdlet](https://technet.microsoft.com/library/ee176961.aspx)。

     > [!IMPORTANT]
     > 仅在 64 位 PowerShell 控制台中设置 Set-executionpolicy bypass。 若为 32 位 PowerShell 控制台设置此项，脚本不会运行。

## <a name="add-the-script-to-the-vmm-library"></a>将脚本添加到 VMM 库

如果具备 VMM 源站点，即可在 VMM 服务器上创建脚本。 然后，将脚本置于还原计划中。

1. 在库共享中新建文件夹。 例如 \<VMM server name>\MSSCVMMLibrary\RPScripts。 将文件夹放到源和目标 VMM 服务器上。
2. 创建脚本。 例如，将脚本命名为 RPScript。 验证脚本是否按预期运行。
3. 将脚本放到源和目标 VMM 服务器的 \<VMM server name>\MSSCVMMLibrary 文件夹中。

## <a name="add-the-script-to-a-recovery-plan"></a>将脚本添加到恢复计划

将 VM 或复制组添加到还原计划中并创建好计划后，可将脚本添加到该组。

1. 打开恢复计划。
2. 在“步骤”列表中，选择一个项。 然后，选择“脚本”或“手动操作”。
3. 指定将脚本或操作添加到的位置（所选项的前面或后面）。 要将脚本上移或下移，请选择“上移”或“下移”按钮。
4. 如果添加 VMM 脚本，请选择“故障转移到 VMM 脚本”。 在“脚本路径”中，输入共享的相对路径。 例如，输入 \RPScripts\RPScript.PS1。
5. 如果添加 Azure 自动化 Runbook，请指定包含 Runbook 的自动化帐户。 然后，选择想要使用的 Azure Runbook 脚本。
6. 请执行还原计划的测试故障转移，确保脚本按预期运行。


## <a name="next-steps"></a>后续步骤
* 详细了解如何[运行故障转移](site-recovery-failover.md)。


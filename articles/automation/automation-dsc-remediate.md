---
title: 修正不符合的 Azure 自动化状态配置服务器
description: 如何对配置状态为偏移的服务器重新应用按需配置
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614493"
---
# <a name="remediate-non-compliant-dsc-servers"></a>修正不符合 DSC 的服务器

在 Azure 自动化状态配置中注册服务器时, "配置模式" 设置为 ApplyOnly、ApplyandMonitor 或 ApplyAndAutoCorrect。
如果未将模式设置为 "自动更正", 则在手动更正之前, 由于任何原因, 从符合状态偏移的服务器将保持不相容状态。

Azure 计算提供了一个名为 "运行命令" 的功能, 该功能使客户能够在虚拟机中运行脚本。
本文档提供了在手动更正配置偏移时此功能的示例脚本。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>使用 PowerShell 更正 Windows 虚拟机的偏移

有关在 Windows 虚拟机上使用 "运行命令" 功能的分步说明, 请参阅文档页使用 "[运行" 命令在 WINDOWS VM 中运行 PowerShell 脚本](/azure/virtual-machines/windows/run-command)。

若要强制 Azure 自动化状态配置节点下载并应用最新的配置, 请使用`Update-DscConfiguration` cmdlet。
有关详细信息, 请参阅 cmdlet 文档[更新-start-dscconfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Linux 虚拟机的正确偏移

类似的功能当前不适用于 Linux 服务器。
唯一的选择是重复注册过程。
对于 Azure 节点, 可以从门户或使用 Az 自动化 cmdlet 来完成此操作。
有关此过程的详细信息, 请参见[用于管理 Azure 自动化状态配置的计算机中的管理计算机](/azure/automation/automation-dsc-onboarding#azure-portal)。
对于混合节点, 可以使用所包含的 Python 脚本来完成偏差更正。
请参阅适用于[Linux 的 POWERSHELL DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)存储库中的文档。

## <a name="next-steps"></a>后续步骤

- 有关 PowerShell cmdlet 参考，请参阅 [Azure Automation State Configuration cmdlet](/powershell/module/azurerm.automation/#automation)
- 若要查看在持续部署管道中使用 Azure Automation State Configuration 的示例，请参阅[使用 Azure Automation State Configuration 和 Chocolatey 进行持续部署](automation-dsc-cd-chocolatey.md)

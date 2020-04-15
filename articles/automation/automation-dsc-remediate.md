---
title: 修复不符合的 Azure 自动化状态配置服务器
description: 如何将配置根据需要重新应用到配置状态已偏移的服务器
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: dfe62c54bfb10d70f1dbf19daec90eec68e66431
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383158"
---
# <a name="remediate-noncompliant-dsc-servers"></a>修复不合规的 DSC 服务器

当服务器注册到 Azure 自动化状态配置时，配置模式设置为`ApplyOnly`。 `ApplyandMonitor` `ApplyAndAutoCorrect` 如果未将模式设置为`ApplyAndAutoCorrect`，则由于任何原因从兼容状态漂移的服务器在手动更正之前保持不合规。

Azure 计算提供名为“运行命令”的功能，允许客户在虚拟机中运行脚本。
本文档提供手动纠正配置偏移时需要用到的此功能的示例脚本。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>使用 PowerShell 纠正 Windows 虚拟机的偏移

有关使用 Windows 虚拟机上的"运行命令"功能的分步说明，请参阅[使用运行命令 在 Windows VM 中运行 PowerShell 脚本](/azure/virtual-machines/windows/run-command)的文档页。

要强制 Azure 自动化状态配置节点下载并应用它，请使用[Update-Dsc配置](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration)cmdlet。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>纠正 Linux 虚拟机的偏移

目前未为 Linux 服务器提供类似的功能。
唯一选项是重复注册过程。
对于 Azure 节点，可以更正从 Azure 门户或使用 Az 模块 cmdlet 的漂移。 有关此过程的详细信息记录在[载入计算机中，供 Azure 自动化状态配置进行管理](automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal)。
对于混合节点，您可以使用包含的 Python 脚本更正漂移。
有关[Linux 回购，请参阅 PowerShell DSC。](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)

## <a name="next-steps"></a>后续步骤

- 有关 PowerShell cmdlet 引用，请参阅[Az.自动化](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 要查看在连续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置进行持续部署和巧克力](automation-dsc-cd-chocolatey.md)。
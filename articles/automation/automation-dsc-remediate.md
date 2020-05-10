---
title: 修正不相容的 Azure 自动化状态配置服务器
description: 如何将配置根据需要重新应用到配置状态已偏移的服务器
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f871b406793e455c857ca14c83434c9ed3e004df
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993844"
---
# <a name="remediate-noncompliant-dsc-servers"></a>修正不相容的 DSC 服务器

将服务器注册到 Azure 自动化状态配置后，配置模式将设置为`ApplyOnly`、 `ApplyandMonitor`或。 `ApplyAndAutoCorrect` 如果未将模式设置为`ApplyAndAutoCorrect`，则在任何原因中偏离符合状态的服务器将保持不相容状态，直到它们被手动更正。

Azure 计算提供名为“运行命令”的功能，允许客户在虚拟机中运行脚本。
本文档提供手动纠正配置偏移时需要用到的此功能的示例脚本。

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>使用 PowerShell 纠正 Windows 虚拟机的偏移

有关在 Windows 虚拟机上使用 "运行命令" 功能的分步说明，请参阅文档页使用 "[运行" 命令在 WINDOWS VM 中运行 PowerShell 脚本](/azure/virtual-machines/windows/run-command)。

若要强制 Azure 自动化状态配置节点下载并应用最新的配置，请使用[start-dscconfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) cmdlet。

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>纠正 Linux 虚拟机的偏移

目前未为 Linux 服务器提供类似的功能。
唯一选项是重复注册过程。
对于 Azure 节点，可以从 Azure 门户或使用 Az module cmdlet 来纠正偏移。 有关此过程的详细信息，请参见[用于管理 Azure 自动化状态配置的计算机](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal)。
对于混合节点，你可以使用包含的 Python 脚本来纠正偏移。
请参阅[适用于 Linux 的 POWERSHELL DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer)存储库。

## <a name="next-steps"></a>后续步骤

- 有关 PowerShell cmdlet 参考，请参阅 [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)。
- 若要查看在持续部署管道中使用 Azure 自动化状态配置的示例，请参阅[使用 Azure 自动化状态配置和 Chocolatey 进行连续部署](automation-dsc-cd-chocolatey.md)。
---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "67173409"
---
如果遇到任何设备问题，你可以从系统日志创建支持包。 Microsoft 支持部门使用此包对问题进行故障排除。 按照以下步骤创建支持包：

1. [连接到设备的 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsNodeSupportPackage`命令创建支持包。 此 cmdlet 的用法如下所示：

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    Cmdlet 从设备收集日志，并将这些日志复制到指定的网络或本地共享。

    使用的参数如下所示：

    - `-Path`-指定要将支持包复制到的网络或本地路径。 （必需）
    - `-Credential`-指定用于访问受保护路径的凭据。
    - `-Zip`-指定以生成 zip 文件。
    - `-Include`-指定要包括在支持包中的组件。 如果未指定， `Default`则假定为。
    - `-IncludeArchived`-指定在支持包中包含存档的日志。
    - `-IncludePeriodicStats`-指定在支持包中包含周期性的状态日志。

    

---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161155"
---
如果遇到任何设备问题，您可以从系统日志中创建支持包。 Microsoft 支持部门使用此包的问题进行疑难解答。 请按照下列步骤以创建支持包：

1. [连接到你的设备的 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsNodeSupportPackage`命令以创建支持包。 Cmdlet 的用法如下所示：

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

    该 cmdlet 从你的设备收集日志并将这些日志复制到指定的网络或本地共享。

    使用的参数如下所示：

    - `-Path` -指定的网络或本地路径复制到支持包。 (必需)
    - `-Credential` -指定用于访问受保护的路径的凭据。
    - `-Zip` -指定要生成的 zip 文件。
    - `-Include` -指定包含要包括在支持包的组件。 如果未指定，`Default`假定。
    - `-IncludeArchived` -指定要包括在支持包中的已存档的日志。
    - `-IncludePeriodicStats` -指定要包括在支持包中的定期统计信息日志。

    

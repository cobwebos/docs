---
title: "对 Azure 文件同步（预览版）进行故障排除 | Microsoft Docs"
description: "对 Azure 文件同步的常见问题进行故障排除"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: cf3f3cf63cafc3b883d26144a53066ee421eb2a6
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="troubleshoot-azure-file-sync-preview"></a>对 Azure 文件同步（预览版）进行故障排除
可使用 Azure 文件同步（预览版）将共享复制到本地或 Azure 中的 Windows Server。 用户即可通过诸如 SMB 或 NFS 共享之类的 Windows Server 访问文件共享。 这尤其适用于要在远离 Azure 数据中心的位置访问和修改数据的方案，例如分支机构方案。 可在多个 Windows Server 终结点（例如多个分支机构）之间复制数据。

本文旨在帮助排查和解决在 Azure 文件同步部署中遇到的问题。 否则，本指南说明如何从系统收集重要日志，以帮助进行更深入的问题调查。 提供了以下选项来获取 Azure 文件同步支持：

- Microsoft 支持部门：若要创建新的支持案例，请在 Azure 门户上导航到“帮助 + 支持”选项卡，然后单击“新建支持请求”。
- [Azure 存储论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowsazuredata)

## <a name="how-to-troubleshoot-agent-installation-failures"></a>如何对代理安装失败进行故障排除
如果 Azure 文件同步代理安装失败，请从提升的命令提示符运行以下命令以在代理安装过程中启用日志记录：

```
StorageSyncAgent.msi /l*v Installer.log
```

一旦安装失败，检查 installer.log 以确定原因。

## <a name="server-is-not-listed-under-registered-servers-in-the-azure-portal"></a>服务器未在 Azure 门户中的“已注册的服务器”下列出
如果对于存储同步服务，服务器未在“已注册的服务器”下列出，请执行以下步骤：
1. 登录要注册的服务器。
2. 打开文件资源管理器并导航到存储同步代理安装目录（默认位置是 `C:\Program Files\Azure\StorageSyncAgent`）。 
3. 运行 ServerRegistration.exe 并按照向导向存储同步服务注册服务器。

## <a name="server-registration-displays-the-following-message-after-installing-the-azure-file-sync-agent-this-server-is-already-registered"></a>服务器注册会在安装 Azure 文件同步代理之后显示以下消息：“此服务器已注册”
![包含“服务器已注册”错误消息的服务器注册对话框屏幕截图](media/storage-sync-files-troubleshoot/server-registration-1.png)

如果以前向存储同步服务注册了服务器，则会显示此消息。 若要向当前存储同步服务注销服务器并向新存储同步服务进行注册，请执行步骤[向 Azure 文件同步注销服务器](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)。

## <a name="how-to-troubleshoot-sync-not-working-on-a-server"></a>如何对同步未在服务器上正常运行进行故障排除
如果同步在服务器上失败，则执行以下操作：
- 验证对于要同步到 Azure 文件共享的目录，Azure 门户中是否存在服务器终结点：
    
    ![在 Azure 门户中具有云和服务器终结点的同步组屏幕截图](media/storage-sync-files-troubleshoot/sync-troubleshoot-1.png)

- 使用事件查看器检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志。
- 确认服务器具有 Internet 连接。
- 通过打开服务 MMC 管理单元来验证 Azure 文件同步服务是否正在服务器上运行，并验证存储同步代理服务 (FileSyncSvc) 是否正在运行。

## <a name="how-to-troubleshoot-individual-files-failing-to-sync"></a>如何对单个文件未能同步进行故障排除 
如果单个文件未能同步，请执行以下操作：
- 在事件查看器中检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志
- 验证文件上是否不存在打开的句柄
    - 注意：Azure 文件同步会定期拍摄 VSS 快照以同步具有打开的句柄的文件

## <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>如何对未能分层的文件进行故障排除
如果文件未能分层到 Azure 文件，请执行以下步骤：

- 验证文件是否存在于 Azure 文件共享中
    - 注意：文件必须先同步到 Azure 文件共享，然后才能进行分层
- 在事件查看器中检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志
- 确认服务器具有 Internet 连接 
- 验证 Azure 文件同步筛选器驱动程序 (StorageSync.sys & StorageSyncGuard.sys) 是否正在运行
    - 打开提升的命令提示符，运行 `fltmc` 并验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否列出

## <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>如何对未能召回的文件进行故障排除
如果文件未能召回，请执行以下步骤：
- 在事件查看器中检查位于 `Applications and Services\Microsoft\FileSync\Agent` 下的操作和诊断事件日志
- 验证文件是否存在于 Azure 文件共享中
- 确认服务器具有 Internet 连接 
- 验证 Azure 文件同步筛选器驱动程序 (StorageSync.sys & StorageSyncGuard.sys) 是否正在运行
    - 打开提升的命令提示符，运行 `fltmc` 并验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否列出

## <a name="how-to-troubleshoot-files-being-unexpectedly-recalled-on-a-server"></a>如何对服务器上意外召回的文件进行故障排除
读取大量文件的防病毒、备份和其他应用程序会导致不需要的召回，除非它们遵从脱机属性并跳过读取这些文件的内容。 跳过支持它的这些产品的脱机文件可帮助避免在执行防病毒软件扫描或备份作业这类操作时出现不需要的召回。

请与软件供应商协商如何配置其解决方案以跳过读取脱机文件。

在其他情况（如在文件资源管理器中浏览文件）下可能会发生其他不需要的召回。 在服务器上的文件资源管理器中打开包含云分层文件的文件夹可能会导致不需要的召回，在服务器上启用了防病毒时更是如此。

## <a name="general-troubleshooting"></a>常规故障排除
如果在服务器上遇到 Azure 文件同步问题，则首先执行以下操作：
- 在事件查看器中检查诊断和操作事件日志
    - 同步、分层和召回问题会记录在位于 `Applications and Services\Microsoft\FileSync\Agent` 下的诊断和操作事件日志中
    - 管理服务器（例如配置设置）方面的问题会记录在位于 `Applications and Services\Microsoft\FileSync\Management` 下的诊断和操作事件日志中
- 验证 Azure 文件同步服务是否正在服务器上运行
    - 打开服务 MMC 管理单元，然后验证存储同步代理服务 (FileSyncSvc) 是否正在运行
- 验证 Azure 文件同步筛选器驱动程序 (StorageSync.sys & StorageSyncGuard.sys) 是否正在运行
    - 打开提升的命令提示符，运行 fltmc 并验证 StorageSync.sys 和 StorageSyncGuard.sys 文件系统筛选器驱动程序是否列出

如果在执行以上步骤之后未解决问题，请通过执行以下步骤来运行 AFSDiag 工具：
1. 创建将用于保存 AFSDiag 输出的目录（例如，c:\output）。
2. 打开提升的 PowerShell 窗口并运行以下命令（在输入每个命令之后按 Enter）：

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: use the path created in step 1
    ```

3. 对于 Azure 文件同步内核模式跟踪级别中，输入 1（除非指定为创建更详细的跟踪），然后按 Enter。
4. 对于 Azure 文件同步用户模式跟踪级别中，输入 1（除非指定为创建更详细的跟踪），然后按 Enter。
5. 重现问题，然后在完成时按 D。
6. 包含日志和跟踪文件的 .zip 文件会处于指定的输出目录中。

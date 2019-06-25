---
title: include 文件
description: include 文件
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67173023"
---
无法从服务器访问 Azure 文件同步服务时，则会发生此错误。 可以执行以下步骤来排查此错误：

1. 验证 Windows 服务 `FileSyncSvc.exe` 未被防火墙阻止。
2. 验证端口 443 对 Azure 文件同步服务的传出连接开启。 可以使用 `Test-NetConnection` cmdlet 执行此操作。 以下 `<azure-file-sync-endpoint>` 占位符的 URL 可以在 [Azure 文件同步代理和防火墙设置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)文档中找到。 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. 确保按预期设置代理配置。 使用 `Get-StorageSyncProxyConfiguration` cmdlet 可实现此目的。 有关为 Azure 文件同步配置代理配置的详细信息，可在 [Azure 文件同步代理和防火墙设置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall)中找到。

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. 有关网络连接故障排除的其他帮助，请与网络管理员联系。
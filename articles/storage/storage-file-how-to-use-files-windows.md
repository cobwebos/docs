---
title: "在 Windows 中装载 Azure 文件共享并对其进行访问 | Microsoft Docs"
description: "在 Windows 中装载 Azure 文件共享并对其进行访问。"
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: a6d3a6f6e3457c84c5a7dc7d3601ef9495c060fe
ms.contentlocale: zh-cn
ms.lasthandoff: 07/12/2017

---

# 在 Windows 中装载 Azure 文件共享并对其进行访问
<a id="mount-an-azure-file-share-and-access-the-share-in-windows" class="xliff"></a>
[Azure 文件存储](storage-dotnet-how-to-use-files.md)是 Microsoft 推出的易用云文件系统。 可以在 Windows 和 Windows Server 中装载 Azure 文件共享。 本文介绍了三种在 Windows 中装载 Azure 文件共享的不同方式：使用文件资源管理器 UI、通过 PowerShell，以及通过命令提示符。 

若要将 Azure 文件共享装载到其被托管时所在的 Azure 区域之外（例如本地或其他 Azure 区域），OS 必须支持 SMB 3.x。 下表显示最近发行的 Windows 的 SMB 版本：

| Windows 版本 | SMB 版本 | 支持从 Azure VM 装载 | 支持从本地装载 | 最小建议 KB |
|----|----|----|----|----|
| Windows 10 版本 1703 | SMB 3.1.1 | 是 | 是 | |
| Windows Server 2016 | SMB 3.1.1 | 是 | 是 | [KB4015438](https://support.microsoft.com/help/4015438) |
| Windows 10 版本 1607 | SMB 3.1.1 | 是 | 是 | [KB4015438](https://support.microsoft.com/help/4015438) | 
| Windows 10 版本 1511 | SMB 3.1.1 | 是 | 是 | [KB4013198](https://support.microsoft.com/help/4013198) |
| Windows 10 版本 1507 | SMB 3.1.1 | 是 | 是 | [KB4012606](https://support.microsoft.com/help/4012606) | 
| Windows 8.1 | SMB 3.0.2 | 是 | 是 | [KB4012216](https://support.microsoft.com/help/4012216) |
| Windows Server 2012 R2 | SMB 3.0.2 | 是 | 是 | [KB4012216](https://support.microsoft.com/help/4012216) |
| Windows Server 2012 | SMB 3.0 | 是 | 是 | [KB4012214](https://support.microsoft.com/help/4012214) |
| Windows 7 | SMB 2.1 | 是 | 否 | [KB4012215](https://support.microsoft.com/help/4012215) |
| Windows Server 2008 R2 | SMB 2.1 | 是 | 否 | [KB4012215](https://support.microsoft.com/help/4012215) |

> [!Note]  
> 我们始终建议你使用相对于 Windows 版本来说最新的 KB。 设置最小建议 KB 的目的是为不喜欢更新的 IT 管理员提供包含 SMB 修补程序的最新包。

## </a>在 Windows 中装载 Azure 文件共享的先决条件
<a id="aprerequisites-for-mounting-azure-file-share-with-windows" class="xliff"></a> 
* 存储帐户名称：需提供存储帐户的名称才能装载 Azure 文件共享。

* 存储帐户密钥：需提供主要（或辅助）存储帐户密钥才能装载 Azure 文件共享。 目前不支持使用 SAS 密钥进行装载。

* 确保端口 445 处于打开状态：Azure 文件存储使用 SMB 协议。 SMB 通过 TCP 端口 445 通信 - 请查看防火墙是否未阻止 TCP 端口 445 与客户端计算机通信。

## 使用文件资源管理器装载 Azure 文件共享
<a id="mount-the-azure-file-share-with-file-explorer" class="xliff"></a>
> [!Note]  
> 请注意，以下说明是在 Windows 10 上显示的，在较旧的版本上可能稍有不同。 

1. 打开文件资源管理器：若要执行此操作，请从“开始”菜单打开，或者按 Win+E 快捷方式。

2. 导航到窗口左侧的“此电脑”项。这样会更改功能区中的可用菜单。在“计算机”菜单中，选择“映射网络驱动器”。
    
    ![“映射网络驱动器”下拉菜单的屏幕截图](media/storage-file-how-to-use-files-windows/1_MountOnWindows10.png)

3. 在 Azure 门户中从“连接”窗格复制 UNC 路径：有关如何查找此信息的详细说明，可参阅[此文](storage-file-how-to-use-files-portal.md#connect-to-file-share)。

    ![Azure 文件存储“连接”窗格中的 UNC 路径](media/storage-file-how-to-use-files-windows/portal_netuse_connect.png)

4. 选择驱动器号并输入 UNC 路径。 
    
    ![“映射网络驱动器”对话框的屏幕截图](media/storage-file-how-to-use-files-windows/2_MountOnWindows10.png)

5. 使用带 `Azure\` 前缀的存储帐户名称作为用户名，使用存储帐户密钥作为密码。
    
    ![网络凭据对话框的屏幕快照](media/storage-file-how-to-use-files-windows/3_MountOnWindows10.png)

6. 根据需要使用 Azure 文件共享。
    
    ![Azure 文件共享现已装载](media/storage-file-how-to-use-files-windows/4_MountOnWindows10.png)

7. 做好卸载（或断开连接）Azure 文件共享的准备以后，即可在文件资源管理器中右键单击“网络位置”下对应于共享的条目，然后选择“断开连接”。

## 使用 PowerShell 装载 Azure 文件共享
<a id="mount-the-azure-file-share-with-powershell" class="xliff"></a>
1. 使用以下命令装载 Azure 文件共享：记住将 `<storage-account-name>`、`<share-name>`、`<storage-account-key>`、`<desired-drive-letter>` 替换为相应的内容。

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. 根据需要使用 Azure 文件共享。

3. 完成后，使用以下命令卸载 Azure 文件共享。

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> 可以对 `New-PSDrive` 使用 `-Persist` 参数，使 Azure 文件共享在装载后对 OS 的其余部分可见。

## 使用命令提示符装载 Azure 文件共享
<a id="mount-the-azure-file-share-with-command-prompt" class="xliff"></a>
1. 使用以下命令装载 Azure 文件共享：记住将 `<storage-account-name>`、`<share-name>`、`<storage-account-key>`、`<desired-drive-letter>` 替换为相应的内容。

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. 根据需要使用 Azure 文件共享。

3. 完成后，使用以下命令卸载 Azure 文件共享。

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> 可以将 Azure 文件共享配置为在重启后自动重新连接，只需将凭据持久保存在 Windows 中即可。 以下命令将持久保存凭据：
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## 后续步骤
<a id="next-steps" class="xliff"></a>
请参阅以下链接以获取有关 Azure 文件存储的更多信息。

* [常见问题](storage-files-faq.md)
* [故障排除](storage-troubleshoot-file-connection-problems.md)

### 概念性文章和视频
<a id="conceptual-articles-and-videos" class="xliff"></a>
* [Azure File storage: a frictionless cloud SMB file system for Windows and Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)（Azure 文件存储：适用于 Windows 和 Linux 的顺畅的云 SMB 文件系统）
* [如何将 Azure 文件存储与 Linux 配合使用](storage-how-to-use-files-linux.md)

### Azure 文件存储的工具支持
<a id="tooling-support-for-azure-file-storage" class="xliff"></a>
* [对 Azure 存储空间使用 Azure PowerShell](storage-powershell-guide-full.md)
* [如何对 Microsoft Azure 存储使用 AzCopy](storage-use-azcopy.md)
* [将 Azure CLI 用于 Azure 存储](storage-azure-cli.md#create-and-manage-file-shares)
* [排查 Azure 文件存储问题](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### 博客文章
<a id="blog-posts" class="xliff"></a>
* [Azure 文件存储现已正式发布](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File storage](https://azure.microsoft.com/blog/inside-azure-file-storage/)（Azure 文件存储内部）
* [Microsoft Azure 文件服务简介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Migrating data to Azure File](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)（将数据迁移到 Azure 文件）

### 引用
<a id="reference" class="xliff"></a>
* [.NET 存储客户端库参考](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [文件服务 REST API 参考](http://msdn.microsoft.com/library/azure/dn167006.aspx)

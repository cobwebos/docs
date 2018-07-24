---
title: 设置 Microsoft Azure Data Box 磁盘 | Microsoft Docs
description: 通过本教程了解如何设置 Azure Data Box 磁盘
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7f382e3b6e70aadf8c6a090a3d5c049f6b5c0337
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010355"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>教程：打开包装、连接和解锁 Azure Data Box 磁盘

本教程介绍如何打开包装、连接和解锁 Azure Data Box 磁盘。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 打开 Data Box 磁盘的包装
> * 连接和解锁 Data Box 磁盘。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：订购 Azure Data Box 磁盘](data-box-disk-deploy-ordered.md)。
2. 已收到磁盘，并且门户中的作业状态已更新为“已交付”。
3. 有一台可以安装 Data Box 磁盘解锁工具的主机。 该主机必须
    - 运行[支持的操作系统](data-box-disk-system-requirements.md)。
    - [装有 Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855)。
    - [装有 .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)。

## <a name="unpack-your-disks"></a>打开磁盘的包装

 执行以下步骤打开磁盘的包装。

1. Data Box 磁盘在一个小包装箱中寄送。 请打开包装箱，并取出其中的物件。 检查包装中是否包含 1 到 5 块固态硬盘 (SSD)，每块磁盘配有一根 USB 连接线。 检查包装是否有任何改动的痕迹或任何明显的损坏。 

    ![Data Box 磁盘发货包装](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. 如果包装箱被改动或有严重损坏，请不要打开包装箱。 联系 Microsoft 支持部门，让他们帮助评估磁盘是否正常，或者是否需要更换磁盘。
3. 检查包装箱中是否有一个透明套管，其中包含退件用的发货标签（在当前标签的下面）。 如果此标签丢失或损坏，始终可以从 Azure 门户下载并打印新的发货标签。 

    ![Data Box 磁盘发货标签](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. 保管好包装箱和泡沫材料，以便在退回磁盘时使用。

## <a name="connect-and-unlock-your-disks"></a>连接和解锁磁盘

执行以下步骤，以连接并解锁磁盘。

1. 使用随附的数据线将磁盘连接到运行先决条件部分中所述受支持 OS 的 Windows 计算机。 

    ![Data Box 磁盘连接](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. 在 Azure 门户中，转到“常规”>“设备详细信息”。 
3. 单击“下载 Data Box 磁盘解锁工具”。 

    ![Data Box 磁盘解锁工具下载](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. 在同一台计算机上提取用于复制数据的工具。
5. 打开命令提示符窗口，或者在同一台计算机上以管理员身份运行 Windows PowerShell。
6. （可选）若要验证用于解锁磁盘的计算机是否满足操作系统要求，请运行系统检查命令。 下面显示了示例输出。 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. 在 Azure 门户中，转到“常规”>“设备详细信息”。 使用复制图标复制支持密钥。
8. 运行 `DataBoxDiskUnlock.exe` 并提供支持密钥。 此时会显示分配给磁盘的驱动器号。 下面显示了示例输出。

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. 以后每次重新插入磁盘，都要重复步骤 6 至 8。 如需 Data Box 磁盘解锁工具的帮助，请使用 help 命令。   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. 解锁磁盘后，可以查看磁盘的内容。    

    ![Data Box 磁盘内容](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 磁盘的主题，例如：

> [!div class="checklist"]
> * 打开 Data Box 磁盘的包装
> * 连接和解锁 Data Box 磁盘


请继续学习下一篇教程，了解如何复制 Data Box 磁盘上的数据。

> [!div class="nextstepaction"]
> [复制 Data Box 磁盘上的数据](./data-box-disk-deploy-copy-data.md)


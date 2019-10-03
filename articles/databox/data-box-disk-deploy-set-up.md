---
title: 教程：打开包装、连接和解锁 Azure Data Box Disk | Microsoft Docs
description: 通过本教程了解如何设置 Azure Data Box 磁盘
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 91baec55cb976a3bc64ac60b1759c80e64fd18a3
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375883"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>教程：打开包装、连接和解锁 Azure Data Box Disk

本教程介绍如何打开包装、连接和解锁 Azure Data Box 磁盘。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 打开 Data Box 磁盘的包装
> * 连接到磁盘并获取支持密钥
> * 在 Windows 客户端上解锁磁盘
> * 在 Linux 客户端上解锁磁盘

::: zone-end

::: zone target="chromeless"

## <a name="unpack-connect-and-unlock-azure-data-box-disk"></a>打开包装、连接和解锁 Azure Data Box Disk

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：订购 Azure Data Box Disk](data-box-disk-deploy-ordered.md)。
2. 已收到磁盘，并且门户中的作业状态已更新为“已交付”。 
3. 有一台可以安装 Data Box Disk 解锁工具的客户端计算机。 客户端计算机必须：
    - 运行[支持的操作系统](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)。
    - 已安装其他[所需软件](data-box-disk-system-requirements.md#other-required-software-for-windows-clients)（如果是 Windows 客户端）。  

## <a name="unpack-your-disks"></a>打开磁盘的包装

 执行以下步骤打开磁盘的包装。

1. Data Box 磁盘在一个小包装箱中寄送。 请打开包装箱，并取出其中的物件。 检查包装中是否包含 1 到 5 块固态硬盘 (SSD)，每块磁盘配有一根 USB 连接线。 检查包装是否有任何改动的痕迹或任何明显的损坏。 

    ![Data Box 磁盘发货包装](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. 如果包装箱被改动或有严重损坏，请不要打开包装箱。 联系 Microsoft 支持部门，让他们帮助评估磁盘是否正常，或者是否需要更换磁盘。
3. 检查包装箱中是否有一个透明套管，其中包含退件用的发货标签（在当前标签的下面）。 如果此标签丢失或损坏，始终可以从 Azure 门户下载并打印新的发货标签。 

    ![Data Box 磁盘发货标签](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. 保管好包装箱和泡沫材料，以便在退回磁盘时使用。

## <a name="connect-to-disks-and-get-the-passkey"></a>连接到磁盘并获取支持密钥 

1. 使用随附的数据线将磁盘连接到运行先决条件部分中所述受支持 OS 的客户端计算机。 

    ![Data Box 磁盘连接](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. 在 Azure 门户中，转到“常规”>“设备详细信息”。  使用复制图标复制支持密钥。 此支持密钥用于解锁磁盘。

    ![Data Box Disk 解锁支持密钥](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

根据是要连接到 Windows 还是 Linux 客户端，解锁磁盘的步骤有所不同。

## <a name="unlock-disks-on-windows-client"></a>在 Windows 客户端上解锁磁盘

执行以下步骤，以连接并解锁磁盘。
     
1. 在 Azure 门户中，转到“常规”>“设备详细信息”。  
2. 下载对应于 Windows 客户端的 Data Box Disk 工具集。 此工具集包含 3 个工具：Data Box Disk 解锁工具、Data Box Disk 验证工具和 Data Box Disk 拆分复制工具。 

    在此过程中，将仅使用 Data Box Disk 解锁工具。 其他两个工具将在以后使用。

    > [!div class="nextstepaction"]
    > [下载适用于 Windows 的 Data Box Disk 工具集](https://aka.ms/databoxdisktoolswin)         

3. 在将用于复制数据的同一台计算机上提取工具集。 
4. 打开命令提示符窗口，或者在同一台计算机上以管理员身份运行 Windows PowerShell。
5. （可选）若要验证用于解锁磁盘的计算机是否满足操作系统要求，请运行系统检查命令。 下面显示了示例输出。 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. 运行 `DataBoxDiskUnlock.exe`，并提供在[连接到磁盘并获取支持密钥](#connect-to-disks-and-get-the-passkey)中获取的支持密钥。 此时会显示分配给磁盘的驱动器号。 下面显示了示例输出。

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. 将来每次重新插入磁盘，都需要重复解锁步骤。 如需 Data Box Disk 解锁工具的帮助，请使用 `help` 命令。   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. 解锁磁盘后，可以查看磁盘的内容。    

    ![Data Box 磁盘内容](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

如果解锁磁盘时遇到任何问题，请参阅如何[排查解锁问题](data-box-disk-troubleshoot-unlock.md)。 

## <a name="unlock-disks-on-linux-client"></a>在 Linux 客户端上解锁磁盘

1. 在 Azure 门户中，转到“常规”>“设备详细信息”。  
2. 下载对应于 Linux 客户端的 Data Box Disk 工具集。  

    > [!div class="nextstepaction"]
    > [下载适用于 Linux 的 Data Box Disk 工具集](https://aka.ms/databoxdisktoolslinux) 

3. 在 Linux 客户端上打开终端。 导航到软件下载到的文件夹。 更改文件权限，以便可以执行这些文件。 输入以下命令： 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    下面显示了示例输出。 运行 chmod 命令后，可以通过运行 `ls` 命令来验证文件权限是否已更改。 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. 执行脚本，以安装 Data Box Disk 解锁软件所需的所有二进制文件。 使用 `sudo` 以 root 身份运行该命令。 成功安装二进制文件后，终端上会显示相关的说明。

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    该脚本首先检查客户端计算机是否在运行受支持的操作系统。 下面显示了示例输出。 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. 键入 `y` 继续安装。 脚本安装的包为： 
   - **epel-release** - 包含以下三个包的存储库。 
   - **dislocker 和 fuse-dislocker** - 这些实用工具可帮助解密 BitLocker 加密的磁盘。 
   - **ntfs-3g** - 帮助装载 NTFS 卷的包。 
 
     成功安装包后，终端将显示相关的通知。     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. 运行 Data Box Disk 解锁工具。 在 Azure 门户中提供在[连接到磁盘并获取支持密钥](#connect-to-disks-and-get-the-passkey)中获取的支持密钥。 （可选）指定要解锁 BitLocker 加密卷列表。 应在单引号中指定支持密钥和卷列表。 

    键入以下命令。
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’          

    下面显示了示例输出。 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    其中显示了可将数据复制到的卷的装入点。

7. 将来每次重新插入磁盘，都需要重复解锁步骤。 如需 Data Box Disk 解锁工具的帮助，请使用 `help` 命令。 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    下面显示了示例输出。 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. 解锁磁盘后，可以转到装入点并查看磁盘的内容。 现在，可将数据复制到 *BlockBlob* 或 *PageBlob* 文件夹。 

    ![Data Box 磁盘内容](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


如果解锁磁盘时遇到任何问题，请参阅如何[排查解锁问题](data-box-disk-troubleshoot-unlock.md)。 

::: zone-end

::: zone target="chromeless"

1. 打开磁盘包装并使用附带的电缆将磁盘连接到客户端计算机。
2. 下载 Data Box Disk 工具集并将其解压缩到将用于复制数据的同一台计算机上。

    > [!div class="nextstepaction"]
    > [下载适用于 Windows 的 Data Box Disk 工具集](https://aka.ms/databoxdisktoolswin)

    或
    > [!div class="nextstepaction"]
    > [下载适用于 Linux 的 Data Box Disk 工具集](https://aka.ms/databoxdisktoolslinux) 

3. 若要在 Windows 客户端上解锁磁盘，请打开命令提示符窗口，或者在同一台计算机上以管理员身份运行 Windows PowerShell：

    - 在安装了 Data Box Disk 解锁工具的同一文件夹中键入以下命令。

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  从 Azure 门户中的“常规”>“设备详细信息”  获取密钥，并在此处提供。 此时会显示分配给磁盘的驱动器号。 
4. 若要在 Linux 客户端上解锁磁盘，请打开终端。 转到软件下载到的文件夹。 键入以下命令以更改文件权限，以便可以执行这些文件： 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    执行脚本以安装所有必需的二进制文件。

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    运行 Data Box Disk 解锁工具。 从 Azure 门户中的“常规”>“设备详细信息”  获取密钥，并在此处提供。 （可选）在单引号内指定要解锁的 BitLocker 加密卷列表。

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’
    ```      
5. 将来每次重新插入磁盘，都需要重复解锁步骤。 如需 Data Box 磁盘解锁工具的帮助，请使用 help 命令。

解锁磁盘后，可以查看磁盘的内容。

有关如何设置和解锁磁盘的详细信息，请参阅[设置 Data Box Disk](data-box-disk-deploy-set-up.md)。

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 磁盘的主题，例如：

> [!div class="checklist"]
> * 打开 Data Box 磁盘的包装
> * 连接到磁盘并获取支持密钥
> * 在 Windows 客户端上解锁磁盘
> * 在 Linux 客户端上解锁磁盘


请继续学习下一篇教程，了解如何复制 Data Box 磁盘上的数据。

> [!div class="nextstepaction"]
> [复制 Data Box 磁盘上的数据](./data-box-disk-deploy-copy-data.md)

::: zone-end


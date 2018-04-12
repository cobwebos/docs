---
title: 备份 Azure Linux VMs | Microsoft Docs
description: 使用 Azure 备份来备份 Linux VM，从而为其提供保护。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4bd532a570a978715ba61880047f3a7e49b446ba
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="back-up-linux--virtual-machines-in-azure"></a>在 Azure 中备份 Linux 虚拟机

可以通过定期创建备份来保护数据。 Azure 备份可创建恢复点，这些恢复点存储在异地冗余的恢复保管库中。 从恢复点还原时，可以还原整个 VM，或只是还原特定的文件。 本文介绍如何将单个文件还原到运行 nginx 的 Linux VM。 如果没有可用的 VM，可以参考 [Linux 快速入门](quick-create-cli.md)创建一个 VM。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建 VM 的备份
> * 计划每日备份
> * 从备份还原文件



## <a name="backup-overview"></a>备份概述

当 Azure 备份服务启动备份时，将触发备份扩展来创建时间点快照。 Azure 备份服务在 Linux 中使用 _VMSnapshotLinux_ 扩展。 该扩展是在首次 VM 备份（如果 VM 正在运行）期间安装的。 如果 VM 未运行，备份服务将创建基础存储的快照（因为在 VM 停止时不会发生任何应用程序写入）。

默认情况下，Azure 备份为 Linux VM 创建文件系统一致备份，但可以[使用前脚本和后脚本框架将其配置为创建应用程序一致备份](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent)。 Azure 备份服务创建快照后，数据将传输到保管库。 为最大限度地提高效率，服务仅标识和传输自上次备份以后已更改的数据块。

数据传输完成后，会删除快照并创建恢复点。


## <a name="create-a-backup"></a>创建备份
在恢复服务保管库中创建一个简单的已计划每日备份。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧菜单中选择“虚拟机”。 
3. 从列表中选择要备份的 VM。
4. 在 VM 边栏选项卡上的“设置”部分中，单击“备份”。 此时会打开“启用备份”边栏选项卡。
5. 在“恢复服务保管库”中，单击“新建”并为新保管库提供名称。 将在与虚拟机相同的资源组和位置中创建新保管库。
6. 单击“备份策略”。 对于本示例，请保留默认值，并单击“确定”。
7. 在“启用备份”边栏选项卡中，单击“启用备份”。 这会根据默认的计划创建每日备份。
10. 若要创建初始恢复点，请在“备份”边栏选项卡中单击“立即备份”。
11. 在“立即备份”边栏选项卡中单击日历图标，使用日历控件选择保留此恢复点的最后一天，并单击“备份”。
12. 在 VM 的“备份”边栏选项卡中，可以看到已完成的恢复点数。

    ![恢复点](./media/tutorial-backup-vms/backup-complete.png)

首次备份大约需要 20 分钟。 完成备份后，请继续学习本教程的下一部分。

## <a name="restore-a-file"></a>还原文件

如果意外删除或更改了某个文件，可以使用文件恢复从备份保管库恢复该文件。 文件恢复使用一个在 VM 上运行的脚本将恢复点装载为本地驱动器。 这些驱动器将保持装载 12 小时，以便可以从恢复点复制文件并将其还原到 VM。  

本示例演示如何恢复默认 nginx 网页 /var/www/html/index.nginx-debian.html。 本示例中的 VM 的公共 IP 地址为 *13.69.75.209*。 可使用以下命令找到 VM 的 IP 地址：

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. 在本地计算机上打开浏览器，并键入 VM 的公共 IP 地址查看默认的 nginx 网页。

    ![默认的 nginx 网页](./media/tutorial-backup-vms/nginx-working.png)

1. 通过 SSH 连接到 VM。

    ```bash
    ssh 13.69.75.209
    ```
2. 删除 /var/www/html/index.nginx-debian.html。

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. 在本地计算机上按 CTRL + F5 刷新浏览器，检查默认的 nginx 页面是否已消失。

    ![默认的 nginx 网页](./media/tutorial-backup-vms/nginx-broken.png)
    
1. 在本地计算机上登录到 [Azure 门户](https://portal.azure.com/)。
6. 在左侧菜单中选择“虚拟机”。 
7. 从列表中选择 VM。
8. 在 VM 边栏选项卡上的“设置”部分中，单击“备份”。 此时会打开“备份”边栏选项卡。 
9. 在边栏选项卡顶部的菜单中，选择“文件恢复”。 此时会打开“文件恢复”边栏选项卡。
10. 在“步骤 1: 选择恢复点”中，从下拉列表中选择恢复点。
11. 在“步骤 2: 下载脚本以浏览并恢复文件”中，单击“下载可执行文件”按钮。 将下载的文件保存到本地计算机。
7. 单击“下载脚本”在本地下载脚本文件。
8. 打开 Bash 提示符并键入以下命令。请将 *Linux_myVM_05 05 2017.sh* 替换为下载的脚本的正确路径和文件名，将 *azureuser* 替换为 VM 的用户名，将 *13.69.75.209* 替换为 VM 的公共 IP 地址。
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. 在本地计算机上打开 VM 的 SSH 连接。

    ```bash
    ssh 13.69.75.209
    ```
    
10. 在 VM 上，添加对脚本文件的执行权限。

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. 在 VM 上，运行该脚本将恢复点装载为文件系统。

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. 脚本的输出将提供装入点的路径。 输出与下面类似：

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. 在 VM 上，将 nginx 默认网页从装入点复制回到删除该文件的位置。

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. 在本地计算机上，打开已从中连接到 VM 的 IP 地址、显示 nginx 默认页面的浏览器标签页。 按 CTRL + F5 刷新浏览器页面。 现在，应会看到默认页面再次正常运行。

    ![默认的 nginx 网页](./media/tutorial-backup-vms/nginx-working.png)

18. 在本地计算机上，返回到 Azure 门户的浏览器选项卡，并在“步骤 3: 恢复后卸载磁盘”中单击“卸载磁盘”按钮。 如果忘记执行此步骤，与装入点的连接会在 12 小时后自动关闭。 在这 12 个小时后，若要创建新的装入点，需要下载新脚本。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建 VM 的备份
> * 计划每日备份
> * 从备份还原文件

请转到下一教程，了解如何监视虚拟机。

> [!div class="nextstepaction"]
> [控制虚拟机](tutorial-govern-resources.md)


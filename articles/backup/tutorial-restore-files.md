---
title: "使用 Azure 备份将文件还原到 VM | Microsoft 文档"
description: "了解如何使用备份和恢复服务在 Azure VM 上执行文件级还原。"
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e1bbae56b70c50fcf691db47efd9dc587686e7da
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>将文件还原到 Azure 中的虚拟机
Azure 备份可创建恢复点，这些恢复点存储在异地冗余的恢复保管库中。 从恢复点还原时，可以还原整个 VM，也可以还原单个文件。 本文将详细介绍如何还原单个文件。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 列出和选择恢复点
> * 将恢复点连接到 VM
> * 从恢复点还原文件

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程需要你运行 Azure CLI 2.0.18 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0](/cli/azure/install-azure-cli)。 


## <a name="prerequisites"></a>先决条件
本教程需要使用 Azure 备份所保护的 Linux VM。 若要模拟意外文件删除和恢复过程，请从 Web 服务器中删除一页。 如果需要运行 Web 服务器且已使用 Azure 备份保护的 Linux VM，请参阅[在 Azure 中使用 CLI 备份虚拟机](quick-backup-vm-cli.md)。


## <a name="backup-overview"></a>备份概述
当 Azure 启动备份时，VM 上的备份扩展将获取时点快照。 请求第一个备份时，将在 VM 上安装备份扩展。 如果进行备份时 VM 未运行，则 Azure 备份可能还需要获取基础存储的快照。

默认情况下，Azure 备份采用文件系统一致的备份。 Azure 备份获取快照后，数据将传输到恢复服务保管库。 为最大限度地提高效率，Azure 备份仅标识和传输自上次备份以后已更改的数据块。

数据传输完成后，会删除快照并创建恢复点。


## <a name="delete-a-file-from-a-vm"></a>从 VM 中删除文件
如果意外删除文件或对文件进行了更改，则可以从恢复点还原单个文件。 此过程允许你浏览恢复点中备份的文件并仅还原所需的文件。 在此示例中，我们从 Web 服务器中删除文件来演示文件级恢复过程。

1. 若要连接到 VM，请使用 [az vm show](/cli/azure/vm?view=azure-cli-latest#az_vm_show) 获取 VM 的 IP 地址：

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. 若要确认网站当前是否正常运行，请打开 Web 浏览器到 VM 的公共 IP 地址。 保持 Web 浏览器窗口处于打开状态。

    ![默认的 NGINX 网页](./media/tutorial-restore-files/nginx-working.png)

3. 使用 SSH 连接到 VM。 将 publicIpAddress 替换为你在前一个命令中获取的公共 IP 地址：

    ```bash
    ssh publicIpAddress
    ```

4. 从 Web 服务器中的 /var/www/html/index.nginx-debian.html 删除默认页面，如下所示：

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. 在 Web 浏览器中，刷新网页。 网站不再加载该页面，如下例中所示：

    ![NGINX 网站不再加载默认页面](./media/tutorial-restore-files/nginx-broken.png)

6. 关闭与 VM 之间的 SSH 会话，如下所示：

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>生成文件恢复脚本
为了还原文件，Azure 备份提供了一个脚本，以在将恢复点连接为本地驱动器的 VM 上运行。 你可以浏览该本地驱动器，将文件还原到该 VM，然后断开恢复点。 Azure 备份将根据计划和保留的分配策略继续备份数据。

1. 若要列出 VM 的恢复点，请使用 az backup recoverypoint list[](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list) 命令。 在此示例中，我们为在 myRecoveryServicesVault 中受保护的名为 myVM 的 VM 选择最近的恢复点：

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. 若要获取将恢复点连接或装载到 VM 的脚本，请使用 az backup restore files mount-rp[](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp) 命令。 下面的示例可为在 myRecoveryServicesVault 中受保护的名为 myVM 的 VM 获取脚本。

    将 myRecoveryPointName 替换为你在前一个命令中获取的恢复点的名称：

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    下载该脚本并显示密码，如下面的示例中所示：

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. 若要将该脚本传输到 VM，请使用安全复制 (SCP)。 提供已下载脚本的名称，并将 publicIpAddress 替换为 VM 的公共 IP 地址。 请确保在 SCP 命令的末尾包括尾部 `:`，如下所示：

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>将文件还原到 VM
将恢复脚本复制到 VM 后，即可连接恢复点并还原文件。

1. 使用 SSH 连接到 VM。 将 publicIpAddress 替换为 VM 的公共 IP 地址，如下所示：

    ```bash
    ssh publicIpAddress
    ```

2. 为了使得脚本正确运行，请使用 chmod 添加执行权限。 输入你自己的脚本名称：

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. 若要装载恢复点，请运行脚本。 输入你自己的脚本名称：

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    在脚本运行时，系统会提示你输入密码以访问恢复点。 输入在上一个生成恢复脚本的 az backup restore files mount-rp[](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp) 命令输出中显示的密码。

    脚本的输出将提供恢复点的路径。 下面的示例输出显示恢复点已装入 /home/azureuser/myVM-20170919213536/Volume1：

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. 使用 cp 将 NGINX 默认网页从已装入的恢复点复制回到原始文件位置。 将 /home/azureuser/myVM-20170919213536/Volume1 装入点替换为你自己的位置：

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. 在 Web 浏览器中，刷新网页。 网站现在重新正确加载，如下例中所示：

    ![NGINX 网站现在正确加载](./media/tutorial-restore-files/nginx-restored.png)

7. 关闭与 VM 之间的 SSH 会话，如下所示：

    ```bash
    exit
    ```

8. 使用 az backup restore files unmount-rp[](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_unmount_rp) 从 VM 卸载恢复点。 下面的示例从 myRecoveryServicesVault 中名为 myVM 的 VM 卸载恢复点。

    将 myRecoveryPointName 替换为你在之前命令中获取的恢复点的名称。
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>后续步骤
在本教程中，你已将恢复点连接到 VM，并且还原了 Web 服务器的文件。 你已了解如何：

> [!div class="checklist"]
> * 列出和选择恢复点
> * 将恢复点连接到 VM
> * 从恢复点还原文件

请进入下一个教程了解如何将 Windows Server 备份到 Azure。

> [!div class="nextstepaction"]
> [将 Windows Server 备份到 Azure](tutorial-backup-windows-server-to-azure.md)


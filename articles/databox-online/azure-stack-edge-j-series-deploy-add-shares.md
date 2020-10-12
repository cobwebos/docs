---
title: 教程：使用 Azure Stack Edge Pro GPU 传输要共享的数据 | Microsoft Docs
description: 了解如何在 Azure Stack Edge Pro GPU 设备上添加共享并与其连接。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: cd288926e6f7a3f454be2788479b5c9d414b167a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440278"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>教程：使用 Azure Stack Edge Pro GPU 通过共享传输数据

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

本教程介绍如何在 Azure Stack Edge Pro 设备上添加共享并与其连接。 添加共享后，Azure Stack Edge Pro 可将数据传输到 Azure。

此过程可能需要大约 10 分钟来完成。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 添加共享
> * 连接到共享

## <a name="prerequisites"></a>先决条件

在向 Azure Stack Edge Pro 添加共享之前，请确保：

* 已按照[安装 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) 中所述安装物理设备。

* 已按照[激活 Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md) 中所述激活物理设备。

## <a name="add-a-share"></a>添加共享

若要创建共享，请执行以下过程：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择自己的 Azure Stack Edge 资源，然后转到“概览”。  设备应为“联机”状态。

   ![联机的设备](./media/azure-stack-edge-j-series-deploy-add-shares/device-online-1.png)

2. 在设备命令栏中，选择“+ 添加共享”  。

   ![添加共享](./media/azure-stack-edge-j-series-deploy-add-shares/select-add-share-1.png)

3. 在“添加共享”  窗格中，执行以下过程：

    a. 在“名称”框中，提供共享的唯一名称。   
    共享名称只能包含字母、数字和连字符。 它必须包含 3 到 63 个字符，并以字母或数字开头。 紧邻连字符的字符必须为字母或数字。
    
    b. 选择共享的**类型**。  
    类型可以是“SMB”或“NFS”，默认为“SMB”。   SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。  
    根据你选择 SMB 共享还是 NFS 共享，其余选项会略有不同。 

    c. 提供一个可供共享驻留在其中的存储帐户。

    d. 在“存储服务”下拉列表中，选择“块 Blob”、“页 Blob”或“文件”。      
    所选服务类型取决于数据需要在 Azure 中采用何种格式。 在此示例中，我们希望数据以块 Blob 的形式存储在 Azure 中，因此选择“块 Blob”。  如果选择“页 Blob”，请确保数据按 512 字节对齐。  例如，VHDX 始终按 512 字节对齐。

   > [!IMPORTANT]
   > 确保未对所用的 Azure 存储帐户设置不可变策略，才可将该帐户用于 Azure Stack Edge Pro 或 Data Box Gateway 设备。 有关详细信息，请参阅[为 blob 存储设置和管理不可变策略](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)。

    e. 创建新的 Blob 容器，或使用下拉列表中的现有容器。 如果创建 Blob 容器，请提供容器名称。 如果容器尚不存在，系统会使用新创建的共享名称在存储帐户中创建一个容器。
   
    f. 根据你已创建的是 SMB 共享还是 NFS 共享，请执行以下步骤之一： 
     
    - **SMB 共享**：在“所有特权本地用户”下选择“新建”或“使用现有”。    如果创建新的本地用户，请输入用户名和密码，然后确认密码。 此操作为本地用户分配权限。 在此处分配权限以后，即可使用文件资源管理器修改它们。
    如果针对此共享数据选中“仅允许读取操作”复选框，则可以指定只读用户。 
    
        ![添加 SMB 共享](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS 共享**：输入允许访问共享的客户端的 IP 地址。

        ![添加 NFS 共享](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-nfs-1.png)
   
4. 选择“创建”  以创建共享。
    
    系统会通知你正在创建共享。 使用指定的设置创建共享后，“共享”  磁贴会更新以反映新共享。
    

## <a name="connect-to-the-share"></a>连接到共享

现在可以连接到在上一步创建的一个或多个共享。 根据使用的是 SMB 还是 NFS 共享，步骤可能有所不同。

第一步是要确保在使用 SMB 或 NFS 共享时可以解析设备名称。

### <a name="modify-host-file-for-name-resolution"></a>修改主机文件以进行名称解析

现在要将设备 IP 以及在设备的本地 Web UI 上定义的设备易记名称添加到：

- 客户端上的主机文件，或
- DNS 服务器上的主机文件

> [!IMPORTANT]
> 建议在 DNS 服务器上修改主机文件以进行设备名称解析。

请在用于连接到设备的 Windows 客户端上执行以下步骤：

1. 以管理员身份启动**记事本**，并打开位于 `C:\Windows\System32\Drivers\etc` 的 **hosts** 文件。

    ![Windows 资源管理器 hosts 文件](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-1.png)


2. 将以下条目添加到 hosts 文件，替换为设备的适当值： 

    ```
    <Device IP>   <device friendly name>
    ``` 
    可以从“网络”中获取设备 IP，从本地 Web UI 中的“设备”页面获取设备易记名称 。 下面的 hosts 文件屏幕截图显示了该条目：

    ![Windows 资源管理器 hosts 文件 2](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>连接到 SMB 共享

在连接到 Azure Stack Edge Pro 设备的 Windows Server 客户端上输入以下命令，以便连接到 SMB 共享：


1. 在命令窗口中键入以下命令：

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > 可以仅通过设备名称（而不通过设备 IP 地址）连接到 SMB 共享。

2. 根据系统提示输入共享的密码。  
   下面提供此命令的示例输出。

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. 在键盘上选择“Windows + R”。

4. 在“运行”窗口中指定  `\\<device name>`，然后选择“确定”。   

    ![Windows“运行”对话框](media/azure-stack-edge-j-series-deploy-add-shares/run-window-1.png)

   此时会打开文件资源管理器。 此时应该能够查看作为文件夹创建的共享。 在文件资源管理器中，双击要查看其内容的共享（文件夹）。
 
    ![连接到 SMB 共享](./media/azure-stack-edge-j-series-deploy-add-shares/file-explorer-smbshare-1.png)

    数据在生成时写入到这些共享，然后设备将数据推送到云。

### <a name="connect-to-an-nfs-share"></a>连接到 NFS 共享

在连接到 Azure Stack Edge Pro 设备的 Linux 客户端上，执行以下过程：

1. 确保此客户端已安装 NFSv4 客户端。 若要安装 NFS 客户端，请使用以下命令：

   `sudo apt-get install nfs-common`

    有关详细信息，请转到[安装 NFSv4 客户端](https://help.ubuntu.com/community/NFSv4Howto)。

2. 安装 NFS 客户端后，使用以下命令将创建的 NFS 共享装载到 Azure Stack Edge Pro 设备上：

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    可以从本地 Web UI 的“网络”页面获取设备 IP。

    > [!IMPORTANT]
    > 在装载共享时使用 `sync` 选项可以改进大文件的传输速率。
    > 装载共享之前，请确保已创建要在本地计算机上充当装入点的目录。 这些目录不应包含任何文件或子文件夹。

    以下示例演示如何通过 NFS 连接到 Azure Stack Edge Pro 设备上的共享。 设备 IP 为 `10.10.10.60`。 共享 `mylinuxshare2` 装载在 ubuntuVM 上。 共享装入点为 `/home/azurestackedgeubuntuhost/edge`。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> 以下注意事项适用于此版本：
> - 不支持在共享中创建文件后重命名该文件。 
> - 从共享中删除文件时，并不会删除 Azure存储帐户中的条目。
> - 使用 `rsync` 通过 NFS 进行复制时，请使用 `--inplace` 标志。 

## <a name="next-steps"></a>后续步骤

本教程介绍了以下 Azure Stack Edge Pro 主题：

> [!div class="checklist"]
> * 添加共享
> * 连接到共享

若要了解如何使用 Azure Stack Edge Pro 转换数据，请继续学习下一教程：

> [!div class="nextstepaction"]
> [使用 Azure Stack Edge Pro 转换数据](./azure-stack-edge-j-series-deploy-configure-compute.md)



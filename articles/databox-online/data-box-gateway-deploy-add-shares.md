---
title: 使用 Azure Data Box Gateway 传输数据 | Microsoft Docs
description: 了解如何在 Data Box Gateway 设备上添加共享并与其连接。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 4817db0ce9723f46ceac4f4720915a9bfddcf915
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561710"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>教程：使用 Azure Data Box Gateway 传输数据


## <a name="introduction"></a>简介

本文介绍如何在 Data Box Gateway 上添加共享并与其连接。 添加共享后，Data Box Gateway 设备可将数据传输到 Azure。

此过程可能需要大约 10 分钟来完成。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
>
> * 添加共享
> * 连接到共享

## <a name="prerequisites"></a>先决条件

在向 Data Box Gateway 添加共享之前，请确保：

- 已根据[在 Hyper-V 中预配 Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) 或[在 VMware 中预配 Data Box Gateway](data-box-gateway-deploy-provision-vmware.md) 中的详细说明预配和连接虚拟设备。

- 已根据[连接和激活 Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md) 中所述激活虚拟设备。

- 设备已准备就绪，可在其上创建共享和传输数据。

## <a name="add-a-share"></a>添加共享

若要创建共享，请执行以下过程：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择自己的 Data Box Gateway 资源，然后转到“概览”。  设备应为“联机”状态。 在设备命令栏中，选择“+ 添加共享”  。
   
   ![添加共享](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. 在“添加共享”中，执行以下过程： 

    1. 提供共享的唯一名称。 共享名称只能包含小写字母、数字和连字符。 共享名称必须为 3 到 63 个字符长，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。
    
    2. 选择共享的**类型**。 类型可以是 SMB 或 NFS，默认为 SMB。 SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。 根据你选择 SMB 共享还是 NFS 共享，显示的选项略有不同。

    3. 提供一个可供共享驻留在其中的存储帐户。 如果容器尚不存在，系统会使用新创建的共享名称在存储帐户中创建一个容器。 如果容器已存在，则使用该容器。
       > [!IMPORTANT]
       > 确保所用的 Azure 存储帐户没有设置不可变策略（如果要将它用于 Azure Stack Edge 或 Data Box Gateway 设备）。 有关详细信息，请参阅[为 blob 存储设置和管理不可变策略](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)。
    
    4. 从块 Blob、页 Blob 或文件中选择“存储服务”。  所选服务的类型取决于数据需要以何种格式驻留在 Azure 中。 例如，在此实例中，我们希望数据以 Blob 块的形式驻留在 Azure 中，因此选择“块 Blob”。 如果选择“页 Blob”，必须确保数据按 512 字节对齐。 例如，VHDX 始终按 512 字节对齐。
   
    5. 此步骤取决于你是创建 SMB 共享还是 NFS 共享。
     
    - **SMB 共享** - 在“所有特权本地用户”下选择“新建”或“使用现有”。    如果创建新的本地用户，请输入用户名和密码，然后确认密码。    此操作为本地用户分配权限。 当前不支持修改共享级权限。
    
        ![添加 SMB 共享](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        如果针对此共享数据选中“仅允许读取操作”复选框，则可以指定只读用户。 
        
    - **NFS 共享** - 输入允许访问共享的客户端的 IP 地址。

        ![添加 NFS 共享](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. 选择“创建”  以创建共享。
    
    系统会通知你正在创建共享。 使用指定的设置创建共享后，“共享”  磁贴会更新以反映新共享。
    
    ![“更新的共享”磁贴](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>连接到共享

现在可以连接到在上一步创建的一个或多个共享。 根据使用的是 SMB 还是 NFS 共享，步骤可能有所不同。

### <a name="connect-to-an-smb-share"></a>连接到 SMB 共享

在连接到 Data Box Gateway 的 Windows Server 客户端上输入以下命令，以便连接到 SMB 共享：


1. 在命令窗口中键入以下命令：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    出现提示时，请输入共享的密码。 下面提供此命令的示例输出。

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. 在键盘上选择“Windows + R”。 
3. 在“运行”窗口中指定  `\\<device IP address>`，然后选择“确定”。  此时会打开文件资源管理器。 此时应该能够查看作为文件夹创建的共享。 在文件资源管理器中，双击要查看其内容的共享（文件夹）。
 
    ![连接到 SMB 共享](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    数据在生成时写入到这些共享，然后设备将数据推送到云。

### <a name="connect-to-an-nfs-share"></a>连接到 NFS 共享

在连接到 Azure Stack Edge 设备的 Linux 客户端上，执行以下过程：

1. 确保此客户端已安装 NFSv4 客户端。 若要安装 NFS 客户端，请使用以下命令：

   `sudo apt-get install nfs-common`

    有关详细信息，请转到[安装 NFSv4 客户端](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)。

2. 安装 NFS 客户端以后，请使用以下命令将创建的 NFS 共享装载到 Data Box Gateway 设备上：

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    在设置装载之前，请确保将要在本地计算机上充当装入点的目录已经创建且不含任何文件或子文件夹。

    以下示例演示如何通过 NFS 连接到 Gateway 设备上的共享。 虚拟设备 IP 为 `10.10.10.60`，共享 `mylinuxshare2` 装载在 ubuntuVM 上，装入点为 `/home/databoxubuntuhost/gateway`。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> 以下注意事项适用于此版本：
> - 不支持在共享中创建文件后重命名该文件。
> - 从共享中删除文件并不删除存储帐户中的条目。
> - 如果使用 `rsync` 复制数据，则不支持 `rsync -a` 选项。

## <a name="next-steps"></a>后续步骤

本教程介绍了多个 Data Box Gateway 主题，例如：

> [!div class="checklist"]
> * 添加共享
> * 连接到共享


请继续学习下一教程，了解如何管理 Data Box Gateway。

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Data Box Gateway](https://aka.ms/dbg-docs)



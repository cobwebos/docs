---
title: 使用 Azure Data Box Gateway 传输数据 | Microsoft Docs
description: 了解如何在 Data Box Gateway 设备上添加共享并与其连接。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 0ceff87cd3075d517ee1c0027e19dbf423e44f5c
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108749"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>教程：使用 Azure Data Box Gateway（预览版）传输数据


## <a name="introduction"></a>介绍

本文介绍如何在 Data Box Gateway 上添加共享并与其连接。 添加共享以后，Data Box Gateway 设备就可以将数据传输到 Azure。

此过程可能需要大约 10 分钟来完成。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 添加共享
> * 连接到共享

> [!IMPORTANT]
> - Data Box Gateway 为预览版。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
 
## <a name="prerequisites"></a>先决条件

在向 Data Box Gateway 添加共享之前，请确保：

* 已根据[在 Hyper-V 中预配 Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) 或[在 VMware 中预配 Data Box Gateway](data-box-gateway-deploy-provision-vmware.md) 中的详细说明预配和连接虚拟设备。 

    虚拟设备已根据[连接和激活 Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md) 中的详细说明激活，可供用户创建共享和传输数据。


## <a name="add-a-share"></a>添加共享

在 [Azure 门户](https://portal.azure.com/)中执行以下步骤，以创建共享。

1. 返回到 Azure 门户。 转到“所有资源”，搜索 Data Box Gateway 资源。
    
2. 在资源的筛选列表中选择 Data Box Gateway 资源，然后导航到“概览”。 在设备命令栏中，单击“+ 添加共享”。
   
   ![添加共享](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. 在“添加共享”中指定共享设置。 提供共享的唯一名称。 

   共享名称只能包含数字、小写字母和连字符。 共享名称必须为 3 到 63 个字符长，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。
    
5. 选择共享的**类型**。 类型可以是 SMB 或 NFS，默认为 SMB。 SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。 根据你选择 SMB 共享还是 NFS 共享，显示的选项略有不同。 

6. 必须提供一个可供共享驻留在其中的存储帐户。 如果还没有容器，则使用共享名称在存储帐户中创建一个容器。 如果该容器已存在，则使用现有容器。 
    
7. 从块 Blob、页 Blob 或文件中选择“存储服务”。 所选服务的类型取决于数据需要以何种格式驻留在 Azure 中。 例如，在此实例中，我们希望数据以 Blob 块的形式驻留在 Azure 中，因此选择“块 Blob”。 如果选择“页 Blob”，必须确保数据按 512 字节对齐。 请注意，VHDX 始终按 512 字节对齐。
   
8. 此步骤取决于你是创建 SMB 共享还是 NFS 共享。 
     
    - **如果创建 SMB 共享** - 在“所有特权本地用户”字段中，选择“新建”或“使用现有”。 如果创建新的本地用户，请提供**用户名**、**密码**和**确认密码**。 这样就会为本地用户分配权限。 在此处分配权限以后，即可使用文件资源管理器修改这些权限。
    
        ![添加 SMB 共享](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        如果针对此共享数据勾选“仅允许读取操作”，则可指定只读用户。
        
    - **如果创建 NFS 共享** - 需提供允许的客户端（能够访问共享）的 IP 地址。

        ![添加 NFS 共享](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. 单击“创建”以创建共享。 
    
    将通知你“正在创建共享”。 使用指定的设置创建共享后，“共享”边栏选项卡会更新以反映新共享。 
    
    ![更新的共享列表](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>连接到共享

在连接到 Data Box Gateway 的 Windows Server 客户端上执行这些步骤，以便连接到共享。


1. 打开命令窗口。 在命令提示符处，键入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    出现提示时，请输入共享的密码。 下面提供此命令的示例输出。

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. 按 Windows+R。在“运行”窗口中指定 `\\<device IP address>`。 单击“确定”。 此时会打开文件资源管理器。 此时应能看到作为文件夹创建的共享。 选择并双击要查看其内容的共享（文件夹）。
 
    ![连接到 SMB 共享](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    数据在生成时写入到这些共享，然后设备将数据推送到云。

### <a name="connect-to-an-nfs-share"></a>连接到 NFS 共享

在连接到 Data Box Edge 的 Linux 客户端上执行这些步骤。

1. 确保客户端已安装 NFSv4 客户端。 若要安装 NFS 客户端，请使用以下命令：

   `sudo apt-get install nfs-common`

    有关详细信息，请转到[安装 NFSv4 客户端](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)。

2. 安装 NFS 客户端以后，请使用以下命令将创建的 NFS 共享装载到 Data Box Gateway 设备上：

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    在设置装载之前，请确保将要在本地计算机上充当装入点的目录已经创建且不含任何文件或子文件夹。

    以下示例演示如何通过 NFS 连接到 Gateway 设备上的共享。 虚拟设备 IP 为 `10.10.10.60`，共享 `mylinuxshare2` 装载在 ubuntuVM 上，装入点为 `/home/databoxubuntuhost/gateway`。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> 以下注意事项适用于预览版：
> - 在共享中创建文件以后，不允许重命名文件。 
> - 从共享中删除文件并不删除存储帐户中的条目。

## <a name="next-steps"></a>后续步骤

本教程介绍了多个 Data Box Gateway 主题，例如：

> [!div class="checklist"]
> * 添加共享
> * 连接到共享


请继续学习下一教程，了解如何管理 Data Box Gateway。

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Data Box Gateway](https://aka.ms/dbg-docs)



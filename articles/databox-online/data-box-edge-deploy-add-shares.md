---
title: 教程：使用 Azure Data Box Edge 传输数据以进行共享 | Microsoft Docs
description: 了解如何在 Data Box Edge 设备上添加共享并与其连接。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 7a15db6bbbcd9dfd43b025b780fda5a8b1d79da2
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78946144"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>教程：使用 Azure Data Box Edge 传输数据

本文介绍如何在 Data Box Edge 设备上添加共享并与其连接。 添加共享后，Data Box Edge 可将数据传输到 Azure。

此过程可能需要大约 10 分钟来完成。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 添加共享
> * 连接到共享

 
## <a name="prerequisites"></a>先决条件

在向 Data Box Edge 添加共享之前，请确保：

- 已根据[安装 Azure Data Box Edge](data-box-edge-deploy-install.md) 中的说明安装物理设备。

- 已根据[连接、设置和激活 Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md) 中所述激活物理设备。


## <a name="add-a-share"></a>添加共享

若要创建共享，请执行以下过程：

1. 在 [Azure 门户](https://portal.azure.com/) 中，选择自己的 Data Box Edge 资源，然后转到“概览”。  设备应为“联机”状态。

   ![联机的设备](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. 在设备命令栏中，选择“+ 添加共享”  。

   ![添加共享](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. 在“添加共享”  窗格中，执行以下过程：

    a. 在“名称”框中，提供共享的唯一名称。   
    共享名称只能包含小写字母、数字和连字符。 它必须包含 3 到 63 个字符，并以字母或数字开头。 紧邻连字符的字符必须为字母或数字。
    
    b. 选择共享的**类型**。  
    类型可以是“SMB”或“NFS”，默认为“SMB”。   SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。  
    根据你选择 SMB 共享还是 NFS 共享，其余选项会略有不同。 

    c. 提供一个可供共享驻留在其中的存储帐户。 

    
    d. 在“存储服务”下拉列表中，选择“块 Blob”、“页 Blob”或“文件”。      
    所选服务类型取决于数据需要在 Azure 中采用何种格式。 在此示例中，我们希望数据以块 Blob 的形式存储在 Azure 中，因此选择“块 Blob”。  如果选择“页 Blob”，请确保数据按 512 字节对齐。  例如，VHDX 始终按 512 字节对齐。

    e. 创建新的 Blob 容器，或使用下拉列表中的现有容器。 如果创建 Blob 容器，请提供容器名称。 如果容器尚不存在，系统会使用新创建的共享名称在存储帐户中创建一个容器。
   
    f. 根据你已创建的是 SMB 共享还是 NFS 共享，请执行以下步骤之一： 
     
    - **SMB 共享**：在“所有特权本地用户”下选择“新建”或“使用现有”。    如果创建新的本地用户，请输入用户名和密码，然后确认密码。 此操作为本地用户分配权限。 当前不支持修改共享级权限。

        如果针对此共享数据选中“仅允许读取操作”复选框，则可以指定只读用户。 

        ![添加 SMB 共享](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS 共享**：输入允许访问共享的客户端的 IP 地址。

        ![添加 NFS 共享](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. 选择“创建”  以创建共享。
    
    系统会通知你正在创建共享。 使用指定的设置创建共享后，“共享”  磁贴会更新以反映新共享。
    

## <a name="connect-to-the-share"></a>连接到共享

现在可以连接到在上一步创建的一个或多个共享。 根据使用的是 SMB 还是 NFS 共享，步骤可能有所不同。

### <a name="connect-to-an-smb-share"></a>连接到 SMB 共享

在连接到 Data Box Edge 设备的 Windows Server 客户端上输入以下命令，以便连接到 SMB 共享：


1. 在命令窗口中键入以下命令：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. 根据系统提示输入共享的密码。  
   下面提供此命令的示例输出。

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. 在键盘上选择“Windows + R”。

4. 在“运行”窗口中指定  `\\<device IP address>`，然后选择“确定”。   
   此时会打开文件资源管理器。 此时应该能够查看作为文件夹创建的共享。 在文件资源管理器中，双击要查看其内容的共享（文件夹）。
 
    ![连接到 SMB 共享](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    数据在生成时写入到这些共享，然后设备将数据推送到云。

### <a name="connect-to-an-nfs-share"></a>连接到 NFS 共享

在连接到 Data Box Edge 设备的 Linux 客户端上，执行以下过程：

1. 确保此客户端已安装 NFSv4 客户端。 若要安装 NFS 客户端，请使用以下命令：

   `sudo apt-get install nfs-common`

    有关详细信息，请转到[安装 NFSv4 客户端](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client)。

2. 安装 NFS 客户端后，使用以下命令将创建的 NFS 共享装载到 Data Box Edge 设备上：

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > 在装载共享时使用 `sync` 选项可以改进大文件的传输速率。
    > 装载共享之前，请确保已创建要在本地计算机上充当装入点的目录。 这些目录不应包含任何文件或子文件夹。

    以下示例演示如何通过 NFS 连接到 Data Box Edge 设备上的共享。 设备 IP 为 `10.10.10.60`。 共享 `mylinuxshare2` 装载在 ubuntuVM 上。 共享装入点为 `/home/databoxubuntuhost/edge`。

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> 以下注意事项适用于此版本：
> - 不支持在共享中创建文件后重命名该文件。 
> - 从共享中删除文件并不删除存储帐户中的条目。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下 Data Box Edge 主题：

> [!div class="checklist"]
> * 添加共享
> * 连接到共享

若要了解如何使用 Data Box Edge 来转换数据，请继续学习下一教程：

> [!div class="nextstepaction"]
> [使用 Data Box Edge 转换数据](./data-box-edge-deploy-configure-compute.md)



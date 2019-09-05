---
title: 教程：使用 Azure Data Box 将数据从 VHD 复制到托管磁盘 | Microsoft Docs
description: 了解如何将数据从本地 VM 工作负荷中的 VHD 复制到 Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.openlocfilehash: 4b7182d1fa70a146da1c01273ffe1032f2982546
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240465"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>教程：在 Azure 中使用 Data Box 将数据导入为托管磁盘

本教程介绍如何在 Azure 中使用 Azure Data Box 将本地 VHD 迁移到托管磁盘。 本地 VM 中的 VHD 将作为页 Blob 复制到 Data Box，并作为托管磁盘上传到 Azure。 然后，可将这些托管磁盘附加到 Azure VM。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 查看先决条件
> * 连接到 Data Box
> * 将数据复制到 Data Box


## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

1. 已完成[教程：设置 Azure Data Box](data-box-deploy-set-up.md)。
2. 已收到 Data Box，并且门户中的订单状态为“已送达”。 
3. 已连接到高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，可以使用 1-GbE 数据链路，但复制速度会受影响。
4. 已查看：

    - [“Azure 对象大小限制”中的“支持的托管磁盘大小”](data-box-limits.md#azure-object-size-limits)。
    - [Azure 托管磁盘简介](/azure/virtual-machines/windows/managed-disks-overview)。 

## <a name="connect-to-data-box"></a>连接到 Data Box

Data Box 根据指定的资源组为每个关联的资源组创建一个共享。 例如，如果下单时创建了 `mydbmdrg1` 和 `mydbmdrg2`，则会创建以下共享：

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

在每个共享中，将创建对应于存储帐户中的容器的以下三个文件夹。

- 高级·SSD
- 标准 HDD
- 标准 SSD

下表显示了共享在 Data Box 中的 UNC 路径。
 
|        连接协议           |             共享的 UNC 路径                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

根据是使用 SMB 还是 NFS 连接到 Data Box 共享，连接步骤会有所不同。

> [!NOTE]
> 此功能不支持通过 REST 进行连接。

### <a name="connect-to-data-box-via-smb"></a>通过 SMB 连接到 Data Box

如果使用 Windows Server 主机，请按照以下步骤连接到 Data Box。

1. 第一步是进行身份验证并启动会话。 转到“连接和复制”。  单击“获取凭据”，获取与资源组关联的共享的访问凭据。  也可以通过 Azure 门户中的“设备详细信息”获取访问凭据。 

    > [!NOTE]
    > 托管磁盘的所有共享的凭据是相同的。

    ![获取共享凭据 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. 在“访问共享和复制数据”对话框中，复制共享的“用户名”和“密码”。   单击“确定”。 
    
    ![获取共享凭据 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. 若要从主机访问与资源（在以下示例中为 *mydbmdrg1*）关联的共享，请打开命令窗口。 在命令提示符处，键入：

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    在本示例中，UNC 共享路径如下：

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. 出现提示时，请输入共享的密码。 以下示例演示如何通过前面的命令连接到共享。

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. 按 Windows+R。在“运行”窗口中指定 `\\<device IP address>\<ShareName>`。  单击“确定”打开文件资源管理器  。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    现在，每个共享中应会显示以下预先创建的文件夹。
    
    ![通过文件资源管理器连接到共享 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>通过 NFS 连接到 Data Box

如果使用 Linux 主机，请执行以下步骤将 Data Box 配置为允许 NFS 客户端访问。

1. 提供允许访问共享的客户端的 IP 地址。 在本地 Web UI 中，转到“连接和复制”页。  在“NFS 设置”下，单击“NFS 客户端访问”。  

    ![配置 NFS 客户端访问 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. 提供 NFS 客户端的 IP 地址，然后单击“添加”。  可以重复此步骤为多个 NFS 客户端配置访问。 单击“确定”。 

    ![配置 NFS 客户端访问 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. 确保 Linux 主机上已安装[受支持版本](data-box-system-requirements.md)的 NFS 客户端。 使用特定版本的 Linux 分发版。

3. 安装 NFS 客户端后，使用以下命令在 Data Box 设备上装载 NFS 共享：

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    以下示例演示如何通过 NFS 连接到 Data Box 共享。 Data Box 设备 IP 为 `169.254.250.200`，共享 `mydbmdrg1_MDisk` 装载在 ubuntuVM 上，装入点为 `/home/databoxubuntuhost/databox`。

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>将数据复制到 Data Box

连接到数据服务器后，下一步是复制数据。 VHD 文件将作为页 Blob 复制到临时存储帐户。 然后，页 Blob 将转换为托管磁盘，并转移到某个资源组中。

在开始复制数据之前，请查看以下注意事项：

- 始终将 VHD 复制到某个预先创建的文件夹。 如果将 VHD 复制到这些文件夹以外或者复制到你自己创建的文件夹中，则 VHD 将作为页 Blob 而不是托管磁盘上传到 Azure 存储帐户中。
- 只能上传固定的 VHD 来创建托管磁盘。 不支持 VHDX 文件或者动态 VHD 和差异 VHD。
- 在一个资源组的所有预先创建的文件夹中，只能包含一个具有给定名称的托管磁盘。 这意味着，上传到预先创建的文件夹的 VHD 应具有唯一的名称。 确保给定的名称与资源组中现有的托管磁盘不匹配。
- 查看 [Azure 对象大小限制](data-box-limits.md#azure-object-size-limits)中的托管磁盘限制。

根据是通过 SMB 还是 NFS 进行连接，可以执行以下操作：

- [通过 SMB 复制数据](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [通过 NFS 复制数据](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

等待复制作业完成。 在转到下一步骤之前，请确保复制作业已完成且未出错。

![“连接并复制”页上未显示错误](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

如果复制过程中出错，请从“连接和复制”页下载日志。 

- 如果复制的文件未经过 512 字节对齐，该文件不会作为页 Blob 上传到临时存储帐户。 日志中会出现错误。 请删除该文件，并复制经过 512 字节对齐的文件。

- 如果复制的 VHDX（不支持这些文件）使用很长的名称，日志中会出现错误。

    ![“连接和复制”页上的日志中的错误](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    请先解决这些错误，然后再继续下一步。

为确保数据完整性，复制数据时将以内联方式计算校验和。 复制完成后，检查设备上的已用空间和可用空间。
    
![在仪表板上检查可用空间和已用空间](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

复制作业完成后，可以转到“准备交付”。 


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 查看先决条件
> * 连接到 Data Box
> * 将数据复制到 Data Box


请继续学习下一篇教程，了解如何将 Data Box 寄回 Microsoft。

> [!div class="nextstepaction"]
> [将 Azure Data Box 寄送到 Microsoft](./data-box-deploy-picked-up.md)


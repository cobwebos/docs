---
title: Azure VMware 解决方案 - 使用 Azure 数据框进行迁移
description: 如何使用 Azure 数据框将数据批量迁移到 Azure VMware 解决方案。
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019751"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>使用 Azure 数据框将数据迁移到 Azure VMware 解决方案

Microsoft Azure 数据盒云解决方案允许您以快速、廉价和可靠的方式向 Azure 发送 TB 数据。 通过向你发送专有的 Data Box 存储设备来加速安全数据传输。 每个存储设备的最大可用存储容量为 80 TB，并由区域运营商传输到数据中心。 该设备具有坚固的外壳，可在运输过程中保护数据。

通过使用数据框，您可以将 VMware 数据批量迁移到私有云。 来自本地 VMware vSphere 环境的数据通过网络文件系统 （NFS） 协议复制到数据盒。 批量数据迁移涉及将虚拟机、配置和相关数据的时间点副本保存到数据盒，然后手动将其传送到 Azure。

在本文中，您将了解：

* 设置数据框。
* 通过 NFS 将数据从本地 VMware 环境复制到数据盒。
* 准备返回数据框。
* 准备用于复制到 Azure VMware 解决方案的 blob 数据。
* 将数据从 Azure 复制到私有云。

## <a name="scenarios"></a>方案

在以下方案中使用数据框进行批量数据迁移：

* 将大量数据从本地迁移到 Azure VMware 解决方案。 此方法建立基线并在网络上同步差异。
* 迁移大量已关闭的虚拟机（冷虚拟机）。
* 迁移虚拟机数据以设置开发和测试环境。
* 迁移大量虚拟机模板、ISO 文件和虚拟机磁盘。

## <a name="before-you-begin"></a>开始之前

* 通过 Azure 门户检查先决条件并[订购数据框](../databox/data-box-deploy-ordered.md)。 在订购过程中，必须选择启用 Blob 存储的存储帐户。 收到 Data Box 设备后，将其连接到本地网络，然后使用 vSphere 管理网络可访问的 IP 地址[设置设备](../databox/data-box-deploy-set-up.md)。

* 在预配 Azure VMware 解决方案的同一区域中创建虚拟网络和存储帐户。

* 使用[ExpressRoute 按照将 Azure 虚拟网络连接到云简单](virtual-network-connection.md)中的步骤，从私有云创建[Azure 虚拟网络连接](cloudsimple-azure-network-connection.md)到虚拟网络，在虚拟网络创建存储帐户。

## <a name="set-up-data-box-for-nfs"></a>为 NFS 设置数据框

按照[教程"电缆](../databox/data-box-deploy-set-up.md)"部分"连接到设备"部分的步骤连接到数据盒本地 Web UI。  配置数据框以允许访问 NFS 客户端：

1. 在本地 Web UI 中，转到 **"连接"和"复制**"页。 在**NFS 设置**下，选择**NFS 客户端访问**。 

    ![配置 NFS 客户端访问 1](media/nfs-client-access.png)

2. 输入 VMware ESXi 主机的 IP 地址，然后选择 **"添加**"。 您可以通过重复此步骤来配置 vSphere 群集中所有主机的访问。 选择“确定”。

    ![配置 NFS 客户端访问 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 不能将文件直接复制到存储帐户中的*根*文件夹。

在块 blob 和页 blob 共享下，一级实体为容器，二级实体为 blob。 在 Azure 文件的共享下，第一级实体为共享，第二级实体为文件。

下表显示了 Data Box 上共享的 UNC 路径以及上传数据的 Azure 存储路径 URL。 最终的 Azure 存储路径 URL 可以从 UNC 共享路径派生。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 块 Blob | <li>UNC 共享路径：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 页 Blob  | <li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 文件       |<li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> 使用 Azure 块 blob 复制 VMware 数据。

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>将 NFS 共享作为数据存储装载到本地 vCenter 群集中并复制数据

数据框中的 NFS 共享必须作为数据存储装载到本地 vCenter 群集或 VMware ESXi 主机上，以便将数据复制到 NFS 数据存储：

1. 登录到本地 vCenter 服务器。

2. 右键单击 **"数据中心**"，选择 **"存储**"，选择 **"新数据存储**"，然后选择 **"下一步**"。

   ![添加新数据存储](media/databox-migration-add-datastore.png)

3. 在"添加数据存储"向导的步骤 1 中，在**类型**下选择**NFS。**

   ![添加新数据存储 - 类型](media/databox-migration-add-datastore-type.png)

4. 在向导的步骤 2 中，选择**NFS 3**作为 NFS 版本，然后选择 **"下一步**"。

   ![添加新数据存储 - NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在向导的步骤 3 中，指定数据存储、路径和服务器的名称。 您可以将数据框的 IP 地址用于服务器。 文件夹路径将以格式`/<StorageAccountName_BlockBlob>/<ContainerName>/`表示。

   ![添加新数据存储 - NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在向导的步骤 4 中，选择要装入数据存储的 ESXi 主机，然后选择 **"下一步**"。  在群集中，选择所有主机以确保虚拟机的迁移。

   ![添加新数据存储 - 选择主机](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在向导的步骤 5 中，查看摘要并选择 **"完成**"。

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>将数据复制到数据盒 NFS 数据存储

虚拟机可以迁移或克隆到新的数据存储。  可以使用**存储 vMotion**选项将要迁移的任何未使用的虚拟机迁移到数据盒 NFS 数据存储。 活动虚拟机可以克隆到数据盒 NFS 数据存储。

* 标识并列出可**移动**的虚拟机。
* 标识并列出必须**克隆的**虚拟机。

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>将虚拟机移动到数据盒数据存储

1. 右键单击要移动到数据框数据存储的虚拟机，然后选择 **"迁移**"。

    ![迁移虚拟机](media/databox-migration-vm-migrate.png)

2. 选择**仅更改**迁移类型的存储，然后选择 **"下一步**"。

    ![仅迁移虚拟机 - 仅存储](media/databox-migration-vm-migrate-change-storage.png)

3. 选择 **"数据箱-数据存储**"作为目标，然后选择 **"下一步**"。

    ![迁移虚拟机 - 选择数据存储](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 查看信息并选择 **"完成**"。

5. 对于其他虚拟机，重复步骤 1 到 4。

> [!TIP]
> 您可以选择多个处于相同电源状态（已打开或关闭）的虚拟机，并批量迁移它们。

虚拟机将从数据盒迁移到 NFS 数据存储。 迁移所有虚拟机后，可以关闭（关闭）活动虚拟机，为将数据迁移到 Azure VMware 解决方案做准备。

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>将虚拟机或虚拟机模板克隆到数据盒数据存储

1. 右键单击要克隆的虚拟机或虚拟机模板。 选择**克隆** > **到虚拟机**。

    ![虚拟机克隆](media/databox-migration-vm-clone.png)

2. 为克隆虚拟机或虚拟机模板选择名称。

3. 选择要放置克隆对象的文件夹，然后选择 **"下一步**"。

4. 选择要放置克隆对象的群集或资源池，然后选择 **"下一步**"。

5. 选择 **"数据箱-数据存储**"作为存储位置，然后选择 **"下一步**"。

    ![虚拟机克隆 - 选择数据存储](media/databox-migration-vm-clone-select-datastore.png)

6. 如果要为克隆对象自定义任何选项，请选择自定义选项，然后选择 **"下一步**"。

7. 查看配置并选择 **"完成**"。

8. 对于其他虚拟机或虚拟机模板，重复步骤 1 到 7。

虚拟机将从数据盒克隆并存储在 NFS 数据存储中。 克隆虚拟机后，请确保关闭虚拟机，以便将数据迁移到 Azure VMware 解决方案。

### <a name="copy-iso-files-to-the-data-box-datastore"></a>将 ISO 文件复制到数据盒数据存储

1. 从本地 vCenter Web UI 转到**存储**。  选择 **"数据箱-数据存储"，** 然后选择 **"文件**"。 创建新文件夹以存储 ISO 文件。

    ![复制 ISO - 创建新文件夹](media/databox-migration-create-folder.png)

2. 提供将存储 ISO 文件的文件夹的名称。

3. 双击新创建的文件夹以将其打开。

4. 选择 **"上传文件**"，然后选择要上载的 ISO 文件。
    
    ![复制 ISO - 上传文件](media/databox-migration-upload-iso.png)

> [!TIP]
> 如果本地数据存储中已有 ISO 文件，则可以选择要将文件复制**的文件并复制以**将文件复制到数据盒 NFS 数据存储。


## <a name="prepare-data-box-for-return"></a>准备返回的数据框

将所有虚拟机数据、虚拟机模板数据以及任何 ISO 文件复制到数据盒 NFS 数据存储后，可以将数据存储与 vCenter 断开连接。 在断开数据存储的连接之前，必须从库存中删除所有虚拟机和虚拟机模板。

### <a name="remove-objects-from-inventory"></a>从清单中删除对象

1. 从本地 vCenter Web UI 转到**存储**。 选择**数据箱-数据存储**，然后选择**VM。**

    ![从库存中删除虚拟机 - 已关闭](media/databox-migration-select-databox-vm.png)

2. 确保所有虚拟机都已关闭。

3. 选择所有虚拟机，右键单击，然后选择从**库存中删除**。

    ![从库存中删除虚拟机](media/databox-migration-remove-vm-from-inventory.png)

4. 在**文件夹中**选择 VM 模板，然后重复步骤 3。

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>从 vCenter 中删除数据框 NFS 数据存储

在准备返回之前，必须断开数据盒 NFS 数据存储与 VMware ESXi 主机的连接。

1. 从本地 vCenter Web UI 转到**存储**。

2. 右键单击 **"数据箱-数据存储"，** 然后选择 **"卸载数据存储**"。

    ![卸载数据框数据存储](media/databox-migration-unmount-datastore.png)

3. 选择装入数据存储的所有 ESXi 主机，然后选择 **"确定**"。

    ![卸载数据框数据存储 - 选择主机](media/databox-migration-unmount-datastore-select-hosts.png)

4. 查看并接受任何警告，并选择 **"确定**"。

### <a name="prepare-data-box-for-return-and-then-return-it"></a>准备数据框进行返回，然后返回它

按照"返回 Azure 数据框"一文中概述的步骤[操作，并验证将数据上载到 Azure](../databox/data-box-deploy-picked-up.md)以返回数据框。 检查数据副本到 Azure 存储帐户的状态。 状态显示为已完成后，可以验证 Azure 存储帐户中的数据。

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>将数据从 Azure 存储复制到 Azure VMware 解决方案

数据框的订单状态显示为已完成后，复制到数据框设备的数据将在 Azure 存储帐户上可用。 数据现在可以复制到 Azure VMware 解决方案。 存储帐户中的数据必须使用 NFS 协议复制到私有云的 vSAN 数据存储。 

首先，使用**AzCopy**将 Blob 存储数据复制到 Azure 中的 Linux 虚拟机上的托管磁盘。 通过 NFS 使托管磁盘可用，将 NFS 共享装载为私有云上的数据存储，然后复制数据。 此方法可更快地将数据复制到私有云。

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>使用 Linux 虚拟机和托管磁盘将数据复制到私有云，然后导出为 NFS 共享

1. 在创建存储帐户并具有与私有云的 Azure 虚拟网络连接的同一区域中的 Azure 中创建[Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)。

2. 创建存储容量大于 blob 数据量的托管磁盘，并将其[附加到 Linux 虚拟机](../virtual-machines/linux/attach-disk-portal.md)。  如果 Blob 数据量大于可用最大托管磁盘的容量，则必须以多个步骤或使用多个托管磁盘复制数据。

3. 连接到 Linux 虚拟机并装载托管磁盘。

4. [在 Linux 虚拟机上安装 AzCopy。](../storage/common/storage-use-azcopy-v10.md)

5. 使用 AzCopy 将数据从 Azure Blob 存储下载到托管磁盘上。  命令语法： `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  替换为`<storage-account-name>`Azure 存储帐户名称以及`<container-name>`保存通过数据框复制数据的容器。

6. 在 Linux 虚拟机上安装 NFS 服务器：

    - 在 Ubuntu/Debian 分布`sudo apt install nfs-kernel-server`： 上。
    - 在企业 Linux 发行`sudo yum install nfs-utils`版上： .

7. 更改复制 Azure Blob 存储中的数据的托管磁盘上的文件夹的权限。  更改要作为 NFS 共享导出的所有文件夹的权限。

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. 通过编辑文件为客户端 IP 地址分配访问 NFS 共享`/etc/exports`的权限。

    ```bash
    sudo vi /etc/exports
    ```
    
    为私有云的每个 ESXi 主机 IP 在文件中输入以下行。  如果要为多个文件夹创建共享，则添加所有文件夹。

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. 使用`sudo exportfs -a`命令导出 NFS 共享。

10. 使用`sudo systemctl restart nfs-kernel-server`命令重新启动 NFS 内核服务器。


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>将 Linux 虚拟机 NFS 共享作为数据存储装载到私有云 vCenter 群集上，然后复制数据

Linux 虚拟机中的 NFS 共享必须作为数据存储装载到私有云 vCenter 群集上。 装载后，可以从 NFS 数据存储将数据复制到私有云 vSAN 数据存储。

1. 登录到私有云 vCenter 服务器。

2. 右键单击 **"数据中心**"，选择 **"存储**"，选择 **"新数据存储**"，然后选择 **"下一步**"。

   ![添加新数据存储](media/databox-migration-add-datastore.png)

3. 在"添加数据存储"向导的步骤 1 中，选择**NFS**类型。

   ![添加新数据存储 - 类型](media/databox-migration-add-datastore-type.png)

4. 在向导的步骤 2 中，选择**NFS 3**作为 NFS 版本，然后选择 **"下一步**"。

   ![添加新数据存储 - NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在向导的步骤 3 中，指定数据存储、路径和服务器的名称。  您可以将 Linux 虚拟机的 IP 地址用于服务器。  文件夹路径将以格式`/<folder>/<subfolder>/`表示。

   ![添加新数据存储 - NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在向导的步骤 4 中，选择要装入数据存储的 ESXi 主机，然后选择 **"下一步**"。  在群集中，选择所有主机以确保虚拟机的迁移。

   ![添加新数据存储 - 选择主机](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在向导的步骤 5 中，查看摘要，然后选择 **"完成**"。

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>将 NFS 数据存储中的虚拟机和虚拟机模板添加到库存中

1. 从私有云 vCenter Web UI 转到**存储**。  选择 Linux 虚拟机 NFS 数据存储，然后选择**文件**。

    ![从 NFS 数据存储中选择文件](media/databox-migration-datastore-select-files.png)

2. 选择包含虚拟机或虚拟机模板的文件夹。  在详细信息窗格中，为虚拟机选择 .vmx 文件，为虚拟机模板选择 .vmx 文件。

3. 选择 **"注册 VM"** 以在私有云 vCenter 上注册虚拟机。

    ![注册虚拟机](media/databox-migration-datastore-register-vm.png)

4. 选择要注册虚拟机的数据中心、文件夹和群集/资源池。

4. 对所有虚拟机和虚拟机模板重复步骤 3 和 4。

5. 转到包含 ISO 文件的文件夹。  选择 ISO 文件，然后选择 **"复制"以**将文件复制到 vSAN 数据存储上的文件夹。

虚拟机和虚拟机模板现在可在私有云 vCenter 上使用。 在打开这些虚拟机之前，必须将这些虚拟机从 NFS 数据存储移动到 vSAN 数据存储。 您可以使用**存储 vMotion**选项，并选择 vSAN 数据存储作为虚拟机的目标。

虚拟机模板必须从 Linux 虚拟机 NFS 数据存储克隆到 vSAN 数据存储。

### <a name="clean-up-your-linux-virtual-machine"></a>清理 Linux 虚拟机

将所有数据复制到私有云后，可以从私有云中删除 NFS 数据存储：

1. 确保所有虚拟机和模板都移动并克隆到 vSAN 数据存储。

2. 从清单中删除从 NFS 数据存储中的所有虚拟机模板。

3. 从私有云 vCenter 卸载 Linux 虚拟机数据存储。

4. 从 Azure 中删除虚拟机和托管磁盘。

5. 如果不想将数据框传输的数据保留在存储帐户中，请删除 Azure 存储帐户。  
    


## <a name="next-steps"></a>后续步骤

* 了解有关[数据框](../databox/data-box-overview.md)的更多。
* 详细了解将[工作负载迁移到私有云](migrate-workloads.md)的不同选项。

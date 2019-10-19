---
title: Azure VMware 解决方案-使用 Azure Data Box 迁移
description: 如何使用 Azure Data Box 将数据批量迁移到 Azure VMware 解决方案。
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72552905"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>使用 Azure Data Box 将数据迁移到 Azure VMware 解决方案

利用 Microsoft Azure Data Box 云解决方案，你可以快速、廉价和可靠的方式向 Azure 发送 tb （Tb）的数据。 通过向你发送专有的 Data Box 存储设备来加速安全数据传输。 每个存储设备具有 80 TB 的最大可用存储容量，并由区域运营商传输到你的数据中心。 该设备具有坚固的外壳，可在运输过程中保护数据。

通过使用 Data Box，你可以将 VMware 数据大容量迁移到私有云。 通过网络文件系统（NFS）协议将本地 VMware vSphere 环境中的数据复制到 Data Box。 大容量数据迁移涉及将虚拟机、配置和关联数据的时间点副本保存到 Data Box，然后手动将其传送到 Azure。

本文介绍以下内容：

* 设置 Data Box。
* 通过 NFS 将数据从本地 VMware 环境复制到 Data Box。
* 准备 Data Box 的返回。
* 准备 blob 数据以复制到 Azure VMware 解决方案。
* 将数据从 Azure 复制到私有云。

## <a name="scenarios"></a>方案

在以下情况下使用 Data Box 用于大容量数据迁移：

* 将大量数据从本地迁移到 Azure VMware 解决方案。 此方法通过网络建立基线并同步不同之处。
* 迁移大量虚拟机（冷虚拟机）。
* 迁移虚拟机数据以设置开发和测试环境。
* 迁移大量虚拟机模板、ISO 文件和虚拟机磁盘。

## <a name="before-you-begin"></a>开始之前

* 检查先决条件，并[按 Azure 门户顺序 Data Box](../databox/data-box-deploy-ordered.md) 。 在订单过程中，你必须选择启用 Blob 存储的存储帐户。 收到 Data Box 设备后，将其连接到本地网络，并使用可从 vSphere 管理网络访问的 IP 地址[设置设备](../databox/data-box-deploy-set-up.md)。

* 在预配 Azure VMware 解决方案的同一区域中创建虚拟网络和存储帐户。

* 按照[使用 ExpressRoute 将 azure 虚拟网络连接到 CloudSimple](virtual-network-connection.md)中的步骤，创建从私有云到虚拟网络的[Azure 虚拟网络连接](cloudsimple-azure-network-connection.md)。

## <a name="set-up-data-box-for-nfs"></a>设置 NFS Data Box

按照[教程：电缆和连接到你的 Azure Data Box](../databox/data-box-deploy-set-up.md)中 "连接到你的设备" 一节中的步骤操作，连接到 Data Box 本地 web UI。  配置 Data Box 以允许访问 NFS 客户端：

1. 在本地 web UI 中，请切换到 "**连接和复制**" 页面。 在 " **nfs 设置**" 下，选择 " **nfs 客户端访问**"。 

    ![配置 NFS 客户端访问 1](media/nfs-client-access.png)

2. 输入 VMware ESXi 主机的 IP 地址，然后选择 "**添加**"。 可以通过重复此步骤来配置 vSphere 群集中所有主机的访问权限。 选择“确定”。

    ![配置 NFS 客户端访问 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 不能将文件直接复制到存储帐户的*根*文件夹中。

在块 blob 和页 blob 共享下，一级实体为容器，二级实体为 blob。 在 "Azure 文件的共享" 下，"第一级" 实体是 "共享"，二级实体是 "文件"。

下表显示了 Data Box 上共享的 UNC 路径以及上传数据的 Azure 存储路径 URL。 最终的 Azure 存储路径 URL 可以从 UNC 共享路径派生。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 块 Blob | <li>UNC 共享路径：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 页 Blob  | <li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 文件       |<li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> 使用 Azure 块 blob 复制 VMware 数据。

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>装载 NFS 共享作为本地 vCenter 群集上的数据存储并复制数据

你的 Data Box 中的 NFS 共享必须装载为本地 vCenter 群集或 VMware ESXi 主机上的数据存储，以便将数据复制到 NFS 数据存储：

1. 登录到本地 vCenter 服务器。

2. 右键单击**数据中心**，选择 **"存储**"，选择 "**新建数据存储**"，然后选择 "**下一步**"。

   ![添加新数据存储](media/databox-migration-add-datastore.png)

3. 在 "添加数据存储向导" 的步骤1中，选择 "**类型**" 下的 " **NFS** "。

   ![添加新的数据存储-类型](media/databox-migration-add-datastore-type.png)

4. 在向导的步骤2中，选择**nfs 3**作为 nfs 版本，然后选择 "**下一步**"。

   ![添加新的数据存储-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在向导的步骤3中，指定数据存储、路径和服务器的名称。 你可以使用服务器的 Data Box 的 IP 地址。 文件夹路径将采用 `/<StorageAccountName_BlockBlob>/<ContainerName>/` 格式。

   ![添加新的数据存储-NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在向导的步骤4中，选择要将数据存储装载到的 ESXi 主机，然后选择 "**下一步**"。  在群集中，选择 "所有主机"，以确保虚拟机的迁移。

   ![添加新的数据存储-选择主机](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在向导的步骤5中，查看摘要，然后选择 "**完成**"。

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>将数据复制到 Data Box NFS 数据存储

可以将虚拟机迁移或克隆到新的数据存储。  要迁移的任何未使用的虚拟机都可以通过使用**Storage vMotion**选项迁移到 Data Box NFS 数据存储。 可以将活动虚拟机克隆到 Data Box NFS 数据存储。

* 标识并列出可**移动**的虚拟机。
* 标识并列出必须**克隆**的虚拟机。

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>将虚拟机移动到 Data Box 的数据存储

1. 右键单击要移动到 Data Box 数据存储的虚拟机，然后选择 "**迁移**"。

    ![迁移虚拟机](media/databox-migration-vm-migrate.png)

2. 为迁移类型选择 "**仅更改存储**"，然后选择 "**下一步**"。

    ![仅迁移虚拟机-存储](media/databox-migration-vm-migrate-change-storage.png)

3. 选择**Databox-数据存储**作为目标，然后选择 "**下一步**"。

    ![迁移虚拟机-选择数据存储](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 查看信息并选择 "**完成**"。

5. 对于其他虚拟机，重复步骤1到4。

> [!TIP]
> 你可以选择处于相同电源状态（打开或关闭）的多个虚拟机，并批量迁移这些虚拟机。

虚拟机将从 Data Box 迁移到 NFS 数据存储。 迁移所有虚拟机后，可以关闭（关闭）活动虚拟机，以便为将数据迁移到 Azure VMware 解决方案做好准备。

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>将虚拟机或虚拟机模板克隆到 Data Box 的数据存储

1. 右键单击要克隆的虚拟机或虚拟机模板。 选择**克隆** > **克隆到虚拟机**。

    ![虚拟机克隆](media/databox-migration-vm-clone.png)

2. 选择克隆的虚拟机或虚拟机模板的名称。

3. 选择要在其中放置克隆对象的文件夹，然后选择 "**下一步**"。

4. 选择要在其中放置克隆对象的群集或资源池，然后选择 "**下一步**"。

5. 选择**Databox-数据**存储作为存储位置，然后选择 "**下一步**"。

    ![虚拟机克隆-选择数据存储](media/databox-migration-vm-clone-select-datastore.png)

6. 如果要自定义克隆对象的任何选项，请选择 "自定义" 选项，然后选择 "**下一步**"。

7. 查看配置并选择 "**完成**"。

8. 对于其他虚拟机或虚拟机模板，请重复步骤1到7。

将从 Data Box 克隆虚拟机并将其存储在 NFS 数据存储上。 克隆虚拟机后，请确保已将这些虚拟机关闭，以便为将数据迁移到 Azure VMware 解决方案做好准备。

### <a name="copy-iso-files-to-the-data-box-datastore"></a>将 ISO 文件复制到 Data Box 数据存储

1. 从本地 vCenter web UI 中转到 "**存储**"。  选择**Databox-数据存储**，然后选择**文件**。 创建一个用于存储 ISO 文件的新文件夹。

    ![Copy ISO-新建文件夹](media/databox-migration-create-folder.png)

2. 提供将存储 ISO 文件的文件夹的名称。

3. 双击新创建的文件夹以将其打开。

4. 选择 "**上传文件**"，然后选择要上传的 ISO 文件。
    
    ![复制 ISO-上传文件](media/databox-migration-upload-iso.png)

> [!TIP]
> 如果你在本地数据存储中已有 ISO 文件，则可以选择文件并**将其复制**到，以将文件复制到 Data Box NFS 数据存储。


## <a name="prepare-data-box-for-return"></a>准备返回的 Data Box

将所有虚拟机数据、虚拟机模板数据和任何 ISO 文件复制到 Data Box NFS 数据存储后，可以将数据存储从 vCenter 断开连接。 断开数据存储之前，必须从清单中删除所有虚拟机和虚拟机模板。

### <a name="remove-objects-from-inventory"></a>从清单中删除对象

1. 从本地 vCenter web UI 中转到 "**存储**"。 选择**Databox-数据存储**，然后选择**vm**。

    ![从清单中删除虚拟机-已关闭](media/databox-migration-select-databox-vm.png)

2. 请确保所有虚拟机都已关闭。

3. 选择 "所有虚拟机"，右键单击，然后选择 "**从清单中删除**"。

    ![从清单中删除虚拟机](media/databox-migration-remove-vm-from-inventory.png)

4. 选择 **"文件夹" 中的 "VM 模板"** ，然后重复步骤3。

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>从 vCenter 中删除 Data Box NFS 数据存储

在准备返回之前，Data Box NFS 数据存储必须与 VMware ESXi 主机断开连接。

1. 从本地 vCenter web UI 中转到 "**存储**"。

2. 右键单击**Databox-数据存储**，然后选择 "**卸载数据存储**"。

    ![卸载 Data Box 数据存储](media/databox-migration-unmount-datastore.png)

3. 选择已装载数据存储的所有 ESXi 主机，然后选择 **"确定"** 。

    ![卸载 Data Box 数据存储-选择主机](media/databox-migration-unmount-datastore-select-hosts.png)

4. 查看并接受所有警告，然后选择 **"确定"** 。

### <a name="prepare-data-box-for-return-and-then-return-it"></a>准备返回的 Data Box，然后将其返回

按照[返回 Azure Data Box 和验证数据上传到 Azure](../databox/data-box-deploy-picked-up.md)一文中所述的步骤来返回 Data Box。 检查数据复制到 Azure 存储帐户的状态。 在状态显示为 "已完成" 后，可以验证 Azure 存储帐户中的数据。

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>将数据从 Azure 存储复制到 Azure VMware 解决方案

Data Box 的订单状态显示为 "已完成" 后，将在 Azure 存储帐户中提供已复制到 Data Box 设备的数据。 现在可以将数据复制到 Azure VMware 解决方案。 必须使用 NFS 协议将存储帐户中的数据复制到私有云的 vSAN 数据存储。 

首先，使用**AzCopy**将 Blob 存储数据复制到 Azure 中 Linux 虚拟机上的托管磁盘。 使托管磁盘可通过 NFS 提供，在私有云上以数据存储的形式装载 NFS 共享，然后复制数据。 此方法可以更快地将数据复制到私有云。

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>使用 Linux 虚拟机和托管磁盘将数据复制到私有云，然后将其作为 NFS 共享导出

1. 在 Azure 中创建[Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)，在该区域中创建存储帐户并将 Azure 虚拟网络连接到私有云。

2. 创建一个托管磁盘，其存储容量大于 blob 数据量，并[将其附加到 Linux 虚拟机](../virtual-machines/linux/attach-disk-portal.md)。  如果 blob 数据量大于可用的最大托管磁盘容量，则必须通过多个步骤或使用多个托管磁盘复制数据。

3. 连接到 Linux 虚拟机并装载托管磁盘。

4. [在 Linux 虚拟机上安装 AzCopy](../storage/common/storage-use-azcopy-v10.md)。

5. 使用 AzCopy 将数据从 Azure Blob 存储下载到托管磁盘。  命令语法： `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`。  将 `<storage-account-name>` 替换为 Azure 存储帐户名称，并使用保存通过 Data Box 复制的数据的容器 `<container-name>`。

6. 在 Linux 虚拟机上安装 NFS 服务器：

    - 在 Ubuntu/Debian 分发上： `sudo apt install nfs-kernel-server`。
    - 在企业 Linux 发行版上： `sudo yum install nfs-utils`。

7. 更改已复制 Azure Blob 存储中的数据的托管磁盘上文件夹的权限。  更改要作为 NFS 共享导出的所有文件夹的权限。

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. 通过编辑 `/etc/exports` 文件，为客户端 IP 地址分配访问 NFS 共享的权限。

    ```bash
    sudo vi /etc/exports
    ```
    
    为私有云的每个 ESXi 主机 IP 输入文件中的以下行。  如果要为多个文件夹创建共享，请添加所有文件夹。

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. 使用 `sudo exportfs -a` 命令导出 NFS 共享。

10. 使用 `sudo systemctl restart nfs-kernel-server` 命令重启 NFS 内核服务器。


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>在私有云 vCenter 群集上装载 Linux 虚拟机 NFS 共享作为数据存储，然后复制数据

你的 Linux 虚拟机中的 NFS 共享必须装载为私有云 vCenter 群集上的数据存储。 装载后，可以将数据从 NFS 数据存储复制到私有云 vSAN 数据存储。

1. 登录到你的私有云 vCenter 服务器。

2. 右键单击**数据中心**，选择 **"存储**"，选择 "**新建数据存储**"，然后选择 "**下一步**"。

   ![添加新数据存储](media/databox-migration-add-datastore.png)

3. 在 "添加数据存储" 向导的步骤1中，选择**NFS**类型。

   ![添加新的数据存储-类型](media/databox-migration-add-datastore-type.png)

4. 在向导的步骤2中，选择**nfs 3**作为 nfs 版本，然后选择 "**下一步**"。

   ![添加新的数据存储-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在向导的步骤3中，指定数据存储、路径和服务器的名称。  你可以为服务器使用 Linux 虚拟机的 IP 地址。  文件夹路径将采用 `/<folder>/<subfolder>/` 格式。

   ![添加新的数据存储-NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在向导的步骤4中，选择要将数据存储装载到的 ESXi 主机，然后选择 "**下一步**"。  在群集中，选择 "所有主机"，以确保虚拟机的迁移。

   ![添加新的数据存储-选择主机](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在向导的步骤5中，查看摘要，然后选择 "**完成**"。

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>将 NFS 数据存储中的虚拟机和虚拟机模板添加到清单

1. 从私有云 vCenter web UI 中转到 "**存储**"。  选择 Linux 虚拟机 NFS 数据存储，然后选择 "**文件**"。

    ![从 NFS 数据存储中选择文件](media/databox-migration-datastore-select-files.png)

2. 选择包含虚拟机或虚拟机模板的文件夹。  在详细信息窗格中，选择虚拟机的 .vmx 文件或虚拟机模板的 .vmtx 文件。

3. 选择 "**注册 VM** " 以在私有云 vCenter 上注册虚拟机。

    ![注册虚拟机](media/databox-migration-datastore-register-vm.png)

4. 选择要在其中注册虚拟机的数据中心、文件夹和群集/资源池。

4. 对所有虚拟机和虚拟机模板重复步骤3和4。

5. 中转到包含 ISO 文件的文件夹。  选择 ISO 文件，并选择 "**复制到**"，将文件复制到 vSAN 数据存储上的文件夹中。

虚拟机和虚拟机模板现在在私有云 vCenter 上可用。 必须先将这些虚拟机从 NFS 数据存储移动到 vSAN 数据存储，然后再将其打开。 可以使用**Storage vMotion**选项，并选择 vSAN 数据存储作为虚拟机的目标。

必须将虚拟机模板从 Linux 虚拟机 NFS 数据存储克隆到 vSAN 数据存储。

### <a name="clean-up-your-linux-virtual-machine"></a>清理 Linux 虚拟机

将所有数据复制到私有云后，可以从私有云中删除 NFS 数据存储：

1. 请确保所有虚拟机和模板都已移动并克隆到 vSAN 数据存储。

2. 从 NFS 数据存储中删除所有虚拟机模板。

3. 从私有云 vCenter 卸载 Linux 虚拟机数据存储。

4. 从 Azure 中删除虚拟机和托管磁盘。

5. 如果你不希望将 Data Box 传输的数据保留在存储帐户中，则删除 Azure 存储帐户。  
    


## <a name="next-steps"></a>后续步骤

* 详细了解[Data Box](../databox/data-box-overview.md)。
* 详细了解[将工作负载迁移到私有云](migrate-workloads.md)的不同选项。

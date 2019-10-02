---
title: 使用 Azure Data Box 进行 CloudSimple 迁移的 Azure VMware 解决方案
description: 使用 Azure Data Box 将批量数据迁移到 Azure VMware 解决方案 CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817457"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>使用 Azure Data Box 将数据迁移到 Azure VMware 解决方案 CloudSimple

使用 Microsoft Azure Data Box 云解决方案可以通过快速、经济、可靠的方式将 TB 量级的数据发送到 Azure。 通过向你发送专有的 Data Box 存储设备来加速安全数据传输。 每个存储设备的最大可用存储容量为 80 TB，并通过区域运营商传送到数据中心。 该设备具有坚固的外壳，可在运输过程中保护数据。

使用 Azure Data Box，可以将大量 VMware 数据迁移到私有云。  使用 NFS 协议将本地 vSphere 环境中的数据复制到 Data Box。  大容量数据迁移涉及将虚拟机、配置和关联数据的时间点副本复制到 Data Box 并发运到 Azure。

本文介绍以下内容：

* 设置 Azure Data Box。
* 使用 NFS 将数据从本地 VMware 环境复制到 Data Box。
* 正在准备返回 Azure Data Box。
* 通过 CloudSimple 准备要复制到 Azure VMware 解决方案的 blob 数据。
* 将数据从 Azure 复制到私有云。

## <a name="scenarios"></a>方案

对于大容量数据迁移，应在以下方案中使用 Azure Data Box。

* 通过 CloudSimple 将大量数据从本地迁移到 Azure VMware 解决方案，以便在网络上用作基准和同步差异。
* 迁移大量虚拟机（冷虚拟机）。
* 迁移虚拟机数据以设置开发和测试环境。
* 迁移大量虚拟机模板 Iso、虚拟机磁盘。

## <a name="before-you-begin"></a>开始之前

* 检查系统必备组件，并从 Azure 门户[Data Box 顺序](../databox/data-box-deploy-ordered.md)。  在订单过程中，您必须选择允许 BLOB 存储的存储帐户。  收到 Data Box 后，将其连接到本地网络，并使用可从 vSphere 管理网络访问的 IP 地址[设置 Data Box](../databox/data-box-deploy-set-up.md) 。

* 在 Azure 的同一区域中创建虚拟网络和存储帐户，并在其中预配 Azure VMware 解决方案 CloudSimple。

* 使用[使用 ExpressRoute 将 azure 虚拟网络连接到 CloudSimple](virtual-network-connection.md)一文中所述的步骤，创建从私有云到虚拟网络的[Azure 虚拟网络连接](cloudsimple-azure-network-connection.md)。

## <a name="set-up-azure-data-box-for-nfs"></a>设置 NFS Azure Data Box

使用 "**连接到设备**" 一节中所述的步骤连接到 Azure Data Box 本地 web UI，[Tutorial：电缆并连接到 Azure Data Box @ no__t。  配置 Data Box 以允许访问 NFS 客户端。

1. 在本地 Web UI 中，转到“连接和复制”页。 在“NFS 设置”下，单击“NFS 客户端访问”。 

    ![配置 NFS 客户端访问 1](media/nfs-client-access.png)

2. 输入 VMware ESXi 主机的 IP 地址，然后单击 "**添加**"。 可以通过重复此步骤来配置 vSphere 群集中所有主机的访问权限。 单击 **“确定”** 。

    ![配置 NFS 客户端访问 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **始终为要复制到共享下的文件创建一个文件夹，然后将文件复制到该文件夹**。 在块 blob 和页 blob 共享下创建的文件夹表示将数据作为 blob 上传到的容器。 无法将文件直接复制到存储帐户中的 root 文件夹。

在块 blob 和页 blob 共享下，一级实体为容器，二级实体为 blob。 在 Azure 文件共享下，一级实体为共享，二级实体为文件。

下表显示了 Data Box 上共享的 UNC 路径以及上传数据的 Azure 存储路径 URL。 最终的 Azure 存储路径 URL 可以从 UNC 共享路径派生。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 块 Blob | <li>UNC 共享路径：`//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 页 Blob  | <li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 文件       |<li>UNC 共享路径：`//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure 存储 URL：`https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> 使用 Azure 块 Blob 复制 VMware 数据。

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>装载 NFS 共享作为本地 vCenter 群集上的数据存储并复制数据

你的 Azure Data Box 中的 NFS 共享必须装载为本地 vCenter 群集上的数据存储或 VMware ESXi 主机，以便复制数据。  装载后，可以将数据复制到 NFS 数据存储。

1. 登录到本地 vCenter 服务器。

2. 右键单击**数据中心**，选择 "**存储**" "添加" "**新建数据存储**"，然后单击 "**下一步**"

   ![添加新数据存储](media/databox-migration-add-datastore.png)

3. 在 "添加数据存储" 向导的步骤1中，选择 " **NFS**" 类型。

   ![添加新的数据存储-类型](media/databox-migration-add-datastore-type.png)

4. 在步骤2中，选择**nfs 3**作为 nfs 版本，然后单击 "**下一步**"。

   ![添加新的数据存储-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在步骤3中，指定数据存储、路径和服务器的名称。  你可以使用服务器的 Data Box 的 IP 地址。  文件夹路径的格式将为 `/<StorageAccountName_BlockBlob>/<ContainerName>/`。

   ![添加新的数据存储-NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在步骤4中，选择要将数据存储装载到的 ESXi 主机，然后单击 "**下一步**"。  在群集中，选择 "所有主机"，以确保虚拟机的迁移。

   ![添加新的数据存储-选择主机](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在步骤5中，查看摘要，然后单击 "**完成**"

## <a name="copy-data-to-data-box-nfs-datastore"></a>将数据复制到 Data Box NFS 数据存储

可以将虚拟机迁移或克隆到新的数据存储。  任何需要迁移的未使用的虚拟机都可以使用**Storage vMotion**迁移到 Data Box NFS 数据存储。  可以将活动虚拟机**克隆**到 Data Box NFS 数据存储。

* 标识可**移动**的虚拟机的列表。
* 标识必须**克隆**的虚拟机的列表。

### <a name="move-virtual-machine-to-data-box-datastore"></a>将虚拟机移动到 Data Box 的数据存储

1. 右键单击要移动到 Data Box 数据存储的虚拟机，然后选择 "**迁移**"。

    ![迁移虚拟机](media/databox-migration-vm-migrate.png)

2. 选择 "仅为迁移类型**更改存储**"，然后单击 "**下一步**"。

    ![仅迁移虚拟机-存储](media/databox-migration-vm-migrate-change-storage.png)

3. 选择 "Data Box 数据存储" 作为目标，然后单击 "**下一步**"。

    ![迁移虚拟机-选择数据存储](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. 查看信息，然后单击 "**完成**"。

5. 对于其他虚拟机，请重复步骤1到4。

> [!TIP]
> 你可以选择处于相同电源状态的多个虚拟机（开机或关机）并批量迁移它们。

虚拟机的存储将从 Azure Data Box 迁移到 NFS 数据存储。  迁移所有虚拟机后，可以在准备将数据迁移到 Azure VMware 解决方案时关闭已通电的虚拟机。

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>克隆虚拟机或虚拟机模板以 Data Box 数据存储

1. 右键单击要克隆的虚拟机或虚拟机模板。  选择**克隆**，**克隆到虚拟机**

    ![虚拟机克隆](media/databox-migration-vm-clone.png)

2. 选择克隆的虚拟机或虚拟机模板的名称。

3. 选择要将克隆对象放置到的文件夹，然后单击 "**下一步**"。

4. 选择要克隆的对象所在的群集或资源池，然后单击 "**下一步**"。

5. 选择 "将 NFS 数据存储 Azure Data Box 为存储位置"，然后单击 "**下一步**"。

    ![虚拟机克隆-选择数据存储](media/databox-migration-vm-clone-select-datastore.png)

6. 如果要自定义克隆对象的任何选项，请选择 "自定义" 选项，然后单击 "**下一步**"。

7. 查看配置，并单击 "**完成**"。

8. 对于其他虚拟机或虚拟机模板，请重复步骤1到7。

将从 Azure Data Box 克隆虚拟机并将其存储在 NFS 数据存储上。  克隆虚拟机后，请确保克隆的虚拟机处于关闭状态，以便为将数据迁移到 Azure VMware 解决方案做好准备。

### <a name="copy-iso-files-to-data-box-datastore"></a>将 ISO 文件复制到 Data Box 的数据存储

1. 在本地 vCenter web UI 中，导航到 "**存储**"。  选择 "Data Box NFS 数据存储"，然后单击 "**文件**"。  创建一个用于存储 ISO 文件的**新文件夹**。

    ![Copy ISO-新建文件夹](media/databox-migration-create-folder.png)

2. 为将在其中存储 ISO 文件的文件夹提供名称。

3. 双击新创建的文件夹以访问内容。

4. 单击 "上**传文件**"，然后选择要上传的 iso。
    
    ![复制 ISO-上传文件](media/databox-migration-upload-iso.png)

> [!TIP]
> 如果你在本地数据存储中已有 ISO 文件，则可以选择这些文件并**将其复制到**Data Box NFS 数据存储。


## <a name="prepare-azure-data-box-for-return"></a>准备返回的 Azure Data Box

将所有虚拟机数据、虚拟机模板数据和任何 ISO 文件复制到 Data Box NFS 数据存储后，可将数据存储与 vCenter 断开连接。  必须先从清单中删除所有虚拟机和虚拟机模板，然后才能断开数据存储。

### <a name="remove-objects-from-inventory"></a>从清单中删除对象

1. 在本地 vCenter web UI 中，导航到 "**存储**"。  选择 "Data Box NFS 数据存储"，并单击 " **vm**"。

2. 确保所有虚拟机已关闭。

    ![从清单中删除虚拟机-断电](media/databox-migration-select-databox-vm.png)

3. 选择 "所有虚拟机"，右键单击并选择 "**从清单中删除**"。

    ![从清单中删除虚拟机](media/databox-migration-remove-vm-from-inventory.png)

4. 在顶部按钮的**文件夹中选择 VM 模板**，并重复步骤3。 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>从 vCenter 中删除 Azure Data Box NFS 数据存储

准备返回之前，必须先将 Data Box NFS 数据存储与 VMware ESXi 主机断开连接。

1. 在本地 vCenter web UI 中，导航到 "**存储**"。

2. 右键单击 Data Box NFS 数据存储，然后选择 "**卸载数据存储**"。

    ![卸载 Data Box 数据存储](media/databox-migration-unmount-datastore.png)

3. 选择要装载数据存储的所有 ESXi 主机，然后单击 **"确定"** 。

    ![卸载 Data Box 数据存储-选择主机](media/databox-migration-unmount-datastore-select-hosts.png)

4. 查看并接受所有警告，然后单击 **"确定"** 。

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>准备 Data Box 以供返回并返回 Data Box

按照[返回 Azure Data Box 和验证数据上传到 Azure](../databox/data-box-deploy-picked-up.md)一文中所述的步骤来返回 Data Box。  检查数据复制到 Azure 存储帐户的状态，在状态显示为 "已完成" 后，可以验证 Azure 存储帐户中的数据。

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>通过 CloudSimple 将数据从 Azure 存储复制到 Azure VMware 解决方案

当 Data Box 的订单状态显示为 "已完成" 时，复制到 Azure Data Box 的数据将在 Azure 存储帐户上可用。  现在可以通过 CloudSimple 将数据复制到 Azure VMware 解决方案。  必须使用 NFS 协议将存储帐户中的数据复制到私有云的 vSAN 数据存储。  首先，使用**AzCopy**将 blob 存储数据复制到 Azure 中 Linux 虚拟机上的托管磁盘。  使托管磁盘可通过 NFS 协议提供，并装载 NFS 共享作为你的私有云上的数据存储，并复制数据。  此方法可以更快地将数据复制到私有云。 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>使用 Linux 虚拟机和托管磁盘将数据复制到私有云，并将其作为 NFS 共享导出

1. 在 Azure 中创建[Linux 虚拟机](../virtual-machines/linux/quick-create-portal.md)，在该区域中创建存储帐户并将 Azure 虚拟网络连接到私有云。

2. 创建一个比 blob 数据量大的托管磁盘，并[将其附加到 Linux 虚拟机](../virtual-machines/linux/attach-disk-portal.md)。  如果 blob 数据量大于可用的最大托管磁盘量，则必须以多个步骤或使用多个托管磁盘复制数据。

3. 连接到 Linux 虚拟机并装载托管磁盘。

4. [在 Linux 虚拟机上安装 AzCopy](../storage/common/storage-use-azcopy-v10.md)。

5. 使用 AzCopy 将数据从 Azure blob 存储下载到托管磁盘。  命令语法： `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`。  将 `<storage-account-name>` 替换为 Azure 存储帐户名，并将 `<container-name>` 替换为包含 Azure Data Box 所复制数据的容器。

6. 在 linux 虚拟机上安装 NFS 服务器。

    1. 在 Ubuntu/Debian 分发上： `sudo apt install nfs-kernel-server`。
    2. 在企业 Linux 分发上： `sudo yum install nfs-utils`。

7. 更改托管磁盘上复制 Azure blob 存储中的数据的文件夹的权限。  更改要作为 NFS 共享导出的所有文件夹的权限。

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

9. 使用命令 `sudo exportfs -a` 导出 NFS 共享。

10. 使用命令 @no__t 重启 NFS 内核服务器。


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>在私有云 vCenter 群集上装载 Linux 虚拟机 NFS 共享作为数据存储并复制数据

你的 Linux 虚拟机中的 NFS 共享必须装载为私有云 vCenter 群集上的数据存储，以便复制数据。  装载后，可以将数据从 NFS 数据存储复制到私有云 vSAN 数据存储。

1. 登录到你的私有云 vCenter 服务器。

2. 右键单击**数据中心**，选择 "**存储**" "添加" "**新建数据存储**"，然后单击 "**下一步**"

   ![添加新数据存储](media/databox-migration-add-datastore.png)

3. 在 "添加数据存储" 向导的步骤1中，选择 " **NFS**" 类型。

   ![添加新的数据存储-类型](media/databox-migration-add-datastore-type.png)

4. 在步骤2中，选择**nfs 3**作为 nfs 版本，然后单击 "**下一步**"。

   ![添加新的数据存储-NFS 版本](media/databox-migration-add-datastore-nfs-version.png)

5. 在步骤3中，指定数据存储、路径和服务器的名称。  你可以将 Linux 虚拟机的 IP 地址用于服务器。  文件夹路径的格式将为 `/<folder>/<subfolder>/`。

   ![添加新的数据存储-NFS 配置](media/databox-migration-add-datastore-nfs-configuration.png)

6. 在步骤4中，选择要将数据存储装载到的 ESXi 主机，然后单击 "**下一步**"。  在群集中，选择 "所有主机"，以确保虚拟机的迁移。

   ![添加新的数据存储-选择主机](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. 在步骤5中，查看摘要，然后单击 "**完成**"

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>将 NFS 数据存储中的虚拟机和虚拟机模板添加到清单

1. 从私有云 vCenter web UI 中，导航到 "**存储**"。  选择 Linux 虚拟机 NFS 数据存储，然后单击 "**文件**"。

    ![从 NFS 数据存储中选择文件](media/databox-migration-datastore-select-files.png)

2. 选择包含虚拟机或虚拟机模板的文件夹。  从详细信息窗格中，为虚拟机模板选择 `.vmx` 文件或 `.vmtx` 文件。

3. 单击 "**注册 VM** " 以在私有云 vCenter 上注册虚拟机。

    ![注册虚拟机](media/databox-migration-datastore-register-vm.png)

4. 选择要在其中注册虚拟机的数据中心、文件夹和群集/资源池。

4. 对所有虚拟机和虚拟机模板重复步骤3和4。

5. 导航到包含 ISO 文件的文件夹。  选择 "ISO 文件"，并**将其复制到**vSAN 数据存储上的文件夹中。

虚拟机和虚拟机模板现在在私有云 vCenter 上可用。  在开机之前，必须先将这些虚拟机从 NFS 数据存储移动到 vSAN 数据存储。  你可以执行虚拟机的存储 vMotion，并选择 vSAN 数据存储作为虚拟机的目标。

必须将虚拟机模板从 Linux 虚拟机 NFS 数据存储克隆到 vSAN 数据存储。

### <a name="clean-up-of-your-linux-virtual-machine"></a>清理 Linux 虚拟机

将所有数据复制到私有云后，可以从私有云中删除 NFS 数据存储。

1. 确保所有虚拟机和模板都已移动并克隆到 vSAN 数据存储。

2. 从清单中删除来自 NFS 数据存储的所有虚拟机模板。

3. 从私有云 vCenter 卸载 Linux 虚拟机数据存储。

4. 从 Azure 中删除虚拟机和托管磁盘。

5. 如果你不希望在存储帐户上使用 Azure Data Box 传输数据，请删除 Azure 存储帐户。  
    


## <a name="next-steps"></a>后续步骤

* 详细了解[Azure Data Box](../databox/data-box-overview.md)
* 详细了解[将工作负载迁移到私有云](migrate-workloads.md)的不同选项

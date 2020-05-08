---
title: Azure Stack 边缘共享管理 |Microsoft Docs
description: 描述如何使用 Azure 门户管理 Azure Stack 边缘上的共享。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: d64ecb32c783dcee08164856aa90a43d004894a8
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570286"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-stack-edge"></a>使用 Azure 门户管理 Azure Stack 边缘上的共享

本文介绍如何在 Azure Stack 边缘管理共享。 您可以通过 Azure 门户或通过本地 web UI 来管理 Azure Stack 边缘。 使用 Azure 门户可以添加、删除和刷新共享，或者同步与共享相关联的存储帐户的存储密钥。

## <a name="about-shares"></a>关于共享

若要将数据传输到 Azure，需要在 Azure Stack 边缘上创建共享。 在 Azure Stack Edge 设备上添加的共享可以是本地共享，也可以是将数据推送到云的共享。

 - **本地共享**：如果想要在设备上本地处理数据，请使用这些共享。
 - **共享**：如果想要将设备数据自动推送到云中的存储帐户，请使用这些共享。 所有云功能（例如“刷新”和“同步存储密钥”）都适用于这些共享。********

在本文中，学习如何：

> [!div class="checklist"]
>
> * 添加共享
> * 删除共享
> * 刷新共享
> * 同步存储密钥

## <a name="add-a-share"></a>添加共享

在 Azure 门户中执行以下步骤，以创建共享。

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后前往 "**网关 > 共享**"。 在命令栏上选择“+ 添加共享”****。

    ![选择“添加共享”](media/azure-stack-edge-manage-shares/add-share-1.png)

2. 在“添加共享”中指定共享设置。**** 提供共享的唯一名称。
    
    共享名称只能包含数字、小写字母和连字符。 共享名称必须为 3 到 63 个字符长，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。

3. 选择共享的**类型**。 类型可以是“SMB”或“NFS”，默认为“SMB”。******** SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。 根据你选择 SMB 共享还是 NFS 共享，显示的选项略有不同。

4. 提供共享所在的**存储帐户**。 如果还没有容器，则使用共享名称在存储帐户中创建一个容器。 如果该容器已存在，则使用现有容器。

5. 在下拉列表中，选择块 Blob、页 Blob 或文件作为“存储服务”。**** 所选服务的类型取决于数据需要以何种格式驻留在 Azure 中。 例如，在此实例中，我们希望数据以块 Blob 的形式驻留在 Azure 中，因此选择“块 Blob”。**** 如果选择“页 Blob”，必须确保数据经过 512 字节对齐。**** 对于始终经过 512 字节对齐的 VHD 或 VHDX，请使用“页 Blob”。****

   > [!IMPORTANT]
   > 如果你使用的 Azure 存储帐户与 Azure Stack 边缘或 Data Box Gateway 设备一起使用，请确保该帐户不会对其设置永久性策略。 有关详细信息，请参阅[设置和管理 blob 存储的不可变性策略](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)。

6. 此步骤取决于你是创建 SMB 共享还是 NFS 共享。
   - **如果创建 SMB 共享** - 在“所有特权本地用户”字段中，选择“新建”或“使用现有”。************ 如果创建新的本地用户，请提供**用户名**、**密码**和确认密码。 这样就会为本地用户分配权限。 在此处分配权限以后，即可使用文件资源管理器修改这些权限。

      ![添加 SMB 共享](media/azure-stack-edge-manage-shares/add-smb-share.png)

        如果针对此共享数据勾选“仅允许读取操作”，则可以指定只读用户。
   - **如果创建 NFS 共享** - 需要提供能够访问共享的**受允许客户端的 IP 地址**。

      ![添加 NFS 共享](media/azure-stack-edge-manage-shares/add-nfs-share.png)

7. 若要轻松地从 Edge 计算模块访问共享，请使用本地装入点。 选择“将共享与 Edge 计算配合使用”，这样就会在创建共享后自动装载共享。**** 选择此选项后，Edge 模块也可将计算与本地装入点配合使用。

8. 单击“创建”**** 以创建共享。 将通知你“正在创建共享”。 使用指定的设置创建共享后，“共享”**** 边栏选项卡会更新以反映新共享。

## <a name="add-a-local-share"></a>添加本地共享

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后前往 "**网关 > 共享**"。 在命令栏上选择“+ 添加共享”****。

   ![选择“添加共享”](media/azure-stack-edge-manage-shares/add-local-share-1.png)

2. 在“添加共享”中指定共享设置。**** 提供共享的唯一名称。
    
    共享名称只能包含数字、小写字母和连字符。 共享名称必须为 3 到 63 个字符长，并且必须以字母或数字开头。 每个连字符的前后必须为非连字符字符。

3. 选择共享的**类型**。 类型可以是“SMB”或“NFS”，默认为“SMB”。******** SMB 是 Windows 客户端的标准，NFS 用于 Linux 客户端。 根据你选择 SMB 共享还是 NFS 共享，显示的选项略有不同。

4. 若要轻松地从 Edge 计算模块访问共享，请使用本地装入点。 选择“将共享与 Edge 计算配合使用”，使 Edge 模块能够将计算与本地装入点配合使用。****

5. 选择“配置为 Edge 本地共享”。**** 本地共享中的数据将保留在设备本地。 可在本地处理此数据。

6. 在“所有特权本地用户”字段中，选择“新建”或“使用现有项”。************

7. 选择“创建”。  

   ![创建本地共享](media/azure-stack-edge-manage-shares/add-local-share-2.png)

    此时会显示一条通知，指出正在创建共享。 使用指定的设置创建共享后，“共享”**** 边栏选项卡会更新以反映新共享。

   ![查看更新的“共享”边栏选项卡](media/azure-stack-edge-manage-shares/add-local-share-3.png)
    
    选择该共享，以查看其 Edge 计算模块的本地装入点。

   ![查看本地共享详细信息](media/azure-stack-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>装载共享

如果在 Azure Stack Edge 设备上配置计算之前创建了共享，则需要安装该共享。 通过以下步骤装载共享。

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后前往 "**网关 > 共享**"。 从共享列表中选择要装载的共享。 “用于计算”列会将所选共享的状态显示为“已禁用”。********

   ![选择共享](media/azure-stack-edge-manage-shares/select-share-mount.png)

2. 选择“装载”****。

   ![选择装载](media/azure-stack-edge-manage-shares/select-mount.png)

3. 出现确认提示时，选择“是”****。 此时会装载共享。

   ![确认装载](media/azure-stack-edge-manage-shares/confirm-mount.png)

4. 装载共享以后，转到共享列表。 此时会看到“用于计算”列将共享状态显示为“已启用”。********

   ![装载的共享](media/azure-stack-edge-manage-shares/share-mounted.png)

5. 再次选择共享，查看该共享的本地装入点。 Edge 计算模块使用共享的这个本地装入点。

   ![共享的本地装入点](media/azure-stack-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>卸载共享

在 Azure 门户中执行以下步骤，以卸载共享。

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后前往 "**网关 > 共享**"。

   ![选择共享](media/azure-stack-edge-manage-shares/select-share-unmount.png)

2. 从共享列表中选择要卸载的共享。 需确保卸载的共享不被任何模块使用。 如果共享被某个模块使用，则相应模块会出问题。 选择“卸载”****。

   ![选择卸载](media/azure-stack-edge-manage-shares/select-unmount.png)

3. 出现确认提示时，选择“是”****。 此时会卸载共享。

   ![确认卸载](media/azure-stack-edge-manage-shares/confirm-unmount.png)

4. 卸载共享以后，转到共享列表。 此时会看到“用于计算”列将共享状态显示为“已禁用”。********

   ![卸载的共享](media/azure-stack-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>删除共享

在 Azure 门户中执行以下步骤，以删除共享。

1. 在共享列表中，选择并单击要删除的共享。

   ![选择共享](media/azure-stack-edge-manage-shares/delete-share-1.png)

2. 单击 **“删除”** 。

   ![单击“删除”](media/azure-stack-edge-manage-shares/delete-share-2.png)

3. 当系统提示你进行确认时，单击 **“是”**。

   ![确认删除](media/azure-stack-edge-manage-shares/delete-share-3.png)

共享列表将会更新，以反映删除后的结果。

## <a name="refresh-shares"></a>刷新共享

使用刷新功能可以刷新共享的内容。 刷新共享时，将启动搜索，以查找自上次刷新以来添加到云中的所有 Azure 对象，包括 Blob 和文件。 然后，将下载这些附加文件以刷新设备上的共享内容。

> [!IMPORTANT]
>
> - 无法刷新本地共享。
> - 权限和访问控制列表 (ACL) 不会通过刷新操作保存。 

在 Azure 门户中执行以下步骤，以刷新共享。

1. 在 Azure 门户中转到“共享”。**** 选择并单击要刷新的共享。

   ![选择共享](media/azure-stack-edge-manage-shares/refresh-share-1.png)

2. 单击“刷新”。****

   ![单击“刷新”](media/azure-stack-edge-manage-shares/refresh-share-2.png)
 
3. 当系统提示你进行确认时，单击 **“是”**。 此时会启动一个作业来刷新本地共享的内容。

   ![确认刷新](media/azure-stack-edge-manage-shares/refresh-share-3.png)

4. 刷新正在进行时，上下文菜单中的刷新选项会灰显。 单击作业通知以查看刷新作业的状态。

5. 刷新所需的时间取决于 Azure 容器中的文件数以及设备上的文件数。 成功完成刷新后，共享时间戳将会更新。 即使刷新已部分失败，刷新操作也被视为成功，并时间戳会更新。 刷新错误日志也会更新。

   ![更新的时间戳](media/azure-stack-edge-manage-shares/refresh-share-4.png)
 
失败时会引发警报。 警报中详细描述了原因，以及问题的建议解决方法。 警报中还会提供某个文件的链接，该文件包含失败的完整摘要，其中指出了无法更新或删除哪些文件。

## <a name="sync-storage-keys"></a>同步存储密钥

如果已轮换存储帐户密钥，则需要同步存储访问密钥。 同步可帮助设备获取存储帐户的最新密钥。

在 Azure 门户中执行以下步骤，以同步存储访问密钥。

1. 在资源中转到“概述”。**** 在共享列表中，选择并单击与需要同步的存储帐户相关联的共享。

    ![选择包含相关存储帐户的共享](media/azure-stack-edge-manage-shares/sync-storage-key-1.png)

2. 单击“同步存储密钥”。**** 当系统提示确认时，单击“是”****。

     ![选择“同步存储密钥”](media/azure-stack-edge-manage-shares/sync-storage-key-2.png)

3. 同步完成后，请退出对话框。

>[!NOTE]
> 只需针对给定的存储帐户执行此操作一次。 对于与同一存储帐户关联的所有共享，无需重复此操作。

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理用户](azure-stack-edge-manage-users.md)。

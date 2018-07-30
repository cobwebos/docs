---
title: 使用 Azure Site Recovery 在两个 Azure 区域之间迁移 Azure VM | Microsoft Docs
description: 使用 Azure Site Recovery 将 Azure IaaS VM 从一个 Azure 区域迁移到另一个 Azure 区域。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8f081ca9a0868bee11b27e1065a72806cb8c2c9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214587"
---
# <a name="migrate-azure-vms-to-another-region"></a>将 Azure VM 迁移到另一区域

除了使用 [Azure Site Recovery](site-recovery-overview.md) 服务管理和协调本地计算机和 Azure VM 的灾难恢复以实现业务连续性和灾难恢复 (BCDR) 外，还可以使用 Site Recovery 来管理以另一个区域为目的地的 Azure VM 迁移。 若要迁移 Azure VM，请为其启用复制，并将其从主区域故障转移到所选的辅助区域。

本教程展示了如何将 Azure VM 迁移到另一个区域。 本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建恢复服务保管库
> * 为虚拟机启用复制
> * 运行故障转移来迁移 VM

本教程假定已具有 Azure 订阅。 如果尚无 Azure 订阅，请在开始前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。





## <a name="prerequisites"></a>先决条件

- 请确保在迁移的源 Azure 区域中创建了 Azure VM。
- 请确保了解[方案体系结构和组件](azure-to-azure-architecture.md)。
- 查看[支持限制和要求](azure-to-azure-support-matrix.md)。



## <a name="before-you-start"></a>开始之前

在设置复制之前，请完成以下步骤。


### <a name="verify-target-resources"></a>验证目标资源

1. 验证 Azure 订阅是否允许在用于灾难恢复的目标区域中创建 VM。 请联系支持部门，启用所需配额。

2. 确保订阅中有足够的资源，能够支持大小与源 VM 匹配的 VM。 Site Recovery 会为目标 VM 选择相同的大小或尽可能接近的大小。


### <a name="verify-account-permissions"></a>验证帐户权限

如果你刚刚创建了免费 Azure 帐户，那么你就是订阅的管理员。 如果你不是订阅管理员，请与管理员一起分配你所需的权限。 若要为新 VM 启用复制，你必须具有：

1. 在 Azure 资源中创建 VM 的权限。 “虚拟机参与者”内置角色具有这些权限，这包括：
    - 在所选资源组中创建 VM 的权限
    - 在所选虚拟网络中创建 VM 的权限
    - 向所选存储帐户进行写入的权限

2. 你还需要管理 Azure Site Recovery 操作的权限。 “Site Recovery 参与者”角色拥有管理恢复服务保管库中 Site Recovery 操作所需的全部权限。


### <a name="verify-vm-outbound-access"></a>验证 VM 的出站访问

1. 确保未使用身份验证代理来控制要迁移的 VM 的网络连接。 
2. 对于本教程，我们假定要迁移的 VM 可以访问 Internet 并且未使用防火墙代理来控制出站访问。 如果使用了防火墙代理，请检查[此处](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)的要求。

### <a name="verify-vm-certificates"></a>验证 VM 证书

检查要迁移的 Azure VM 上是否存在所有最新的根证书。 如果没有最新的根证书，则 VM 会由于安全约束而无法注册到 Site Recovery。

- 对于 Windows VM，请在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书位于该计算机上。 在未联网的环境中，请按照你的组织的标准 Windows 更新和证书更新过程执行操作。
- 对于 Linux VM，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和证书吊销列表。



## <a name="create-a-vault"></a>创建保管库

在除了源区域之外的任意区域中创建保管库。

1. 登录到 [Azure 门户](https://portal.azure.com) > **恢复服务**。
2. 单击“创建资源” > “监视和管理” > “备份和站点恢复”。
3. 在“名称”中，指定友好名称 **ContosoVMVault**。 如果有多个订阅，请选择合适的一个。
4. 创建资源组 **ContosoRG**。
5. 指定 Azure 区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
6. 若要从仪表板快速访问保管库，请单击“固定到仪表板”，然后单击“创建”。

   ![新保管库](./media/tutorial-migrate-azure-to-azure/azure-to-azure-vault.png)

新保管库将添加到“仪表板”中的“所有资源”下，以及“恢复服务保管库”主页面上。






## <a name="select-the-source"></a>选择源

1. 在“恢复服务保管库”中，单击“ConsotoVMVault” > “+复制”。
2. 在“源”中，选择“Azure”。
3. 在“源位置”中，选择当前运行 VM 的 Azure 源区域。
4. 选择“资源管理器”部署模型。 然后选择**源资源组**。
5. 单击“确定”保存设置。


## <a name="enable-replication-for-azure-vms"></a>为 Azure VM 启用复制

Site Recovery 会检索与订阅和资源组关联的 VM 列表。


1. 在 Azure 门户中，单击“虚拟机”。
2. 选择要迁移的 VM。 然后单击“确定”。
3. 在“设置”中，单击“灾难恢复”。
4. 在“配置灾难恢复” > “目标区域”中，选择要复制到的目标区域。
5. 对于本教程，接受其他默认设置。
6. 单击“启用复制”。 这将启动用于为 VM 启用复制的作业。

    ![启用复制](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="run-a-failover"></a>运行故障转移

1. 在“设置 > “复制的项”中，单击计算机，然后单击“故障转移”。
2. 在“故障转移”中，选择“最新”。 加密密钥设置与此方案无关。
3. 选择“在开始故障转移前关闭计算机”。 Site Recovery 在触发故障转移之前会尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
4. 检查 Azure VM 是否在 Azure 中按预期显示。
5. 在“复制的项”中，右键单击 VM >“提交”。 这样就完成了迁移进程。
6. 提交完成后，请单击“禁用复制”。  这样会停止 VM 的复制。



## <a name="next-steps"></a>后续步骤

在本教程中，你将 Azure VM 迁移到了一个不同的 Azure 区域。 现在，可以为迁移的 VM 配置灾难恢复了。

> [!div class="nextstepaction"]
> [在迁移后设置灾难恢复](azure-to-azure-quickstart.md)


---
title: 使用 Azure Site Recovery 服务将 Azure IaaS VM 移动到其他 Azure 区域 | Microsoft Docs
description: 使用 Azure Site Recovery 将 Azure IaaS VM 从一个 Azure 区域移动到另一个 Azure 区域。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: bbede01844f20c0240b154fd319b818a43463131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824437"
---
# <a name="move-azure-vms-to-another-region"></a>将 Azure VM 移动到另一区域

在多种场合下，我们希望将现有的 Azure IaaS 虚拟机从一个区域移到另一个区域，以提高现有 VM 的可靠性和可用性、提高可管理性，或者满足监管要求，等等。 

本教程介绍如何使用 Azure Site Recovery 将 Azure VM 移到另一区域。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * [验证先决条件](#verify-prerequisites)
> * [准备源 VM](#prepare-the-source-vms)
> * [准备目标区域](#prepare-the-target-region)
> * [将数据复制到目标区域](#copy-data-to-the-target-region)
> * [测试配置](#test-the-configuration)
> * [执行移动](#perform-the-move-to-the-target-region-and-confirm)
> * [丢弃源区域中的资源](#discard-the-resource-in-the-source-region)

> [!IMPORTANT]
> 本文档会引导你将 Azure VM 按原样从一个区域移到另一个区域。如果你的要求是通过将 VM 移到可用性区域来提高基础结构的可用性，请参阅本教程。

## <a name="verify-prerequisites"></a>验证先决条件

- 请确保要从中进行移动的源 Azure 区域中具有 Azure VM。
- 验证所选的[源区域 - 目标区域组合是否受支持](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)，并在目标区域方面做出明智的决策。
- 请确保了解[方案体系结构和组件](azure-to-azure-architecture.md)。
- 查看[支持限制和要求](azure-to-azure-support-matrix.md)。
- 验证帐户权限：如果你刚刚创建了免费 Azure 帐户，那么你就是订阅的管理员。 如果你不是订阅管理员，请与管理员一起分配你所需的权限。 若要为 VM 启用复制，并使用 Azure Site Recovery 将数据最终复制到目标，必须：

    1. 在 Azure 资源中创建 VM 的权限。 “虚拟机参与者”内置角色具有这些权限，这包括：
        - 在所选资源组中创建 VM 的权限
        - 在所选虚拟网络中创建 VM 的权限
        - 向所选存储帐户进行写入的权限

    2. 你还需要管理 Azure Site Recovery 操作的权限。 “Site Recovery 参与者”角色拥有管理恢复服务保管库中 Site Recovery 操作所需的全部权限。

## <a name="prepare-the-source-vms"></a>准备源 VM

1. 检查要移动的 Azure VM 上是否存在所有最新的根证书。 如果没有最新的根证书，则会出于安全约束的原因，无法实现将数据复制到目标区域。

    - 对于 Windows VM，请在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书位于该计算机上。 在未联网的环境中，请按照你的组织的标准 Windows 更新和证书更新过程执行操作。
    - 对于 Linux VM，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和证书吊销列表。
2. 确保未使用身份验证代理来控制要移动的 VM 的网络连接。
3. 如果要移动的 VM 无法访问 Internet，并且使用防火墙代理来控制出站访问，请检查[此处](azure-to-azure-tutorial-enable-replication.md#configure-outbound-network-connectivity)所述的要求。
4. 标识并列出源网络布局以及当前正在使用的所有资源 - 包括但不限于要验证的负载均衡器、NSG、公共 IP，等等。

## <a name="prepare-the-target-region"></a>准备目标区域

1. 验证 Azure 订阅是否允许在用于灾难恢复的目标区域中创建 VM。 如果需要，请联系支持人员启用所需的配额。

2. 确保订阅中有足够的资源，能够支持大小与源 VM 匹配的 VM。 如果使用 Site Recovery 将数据复制到目标区域，Site Recovery 会为目标 VM 选择相同的大小或尽可能接近的大小。

3. 确保为源网络布局中标识的每个组件创建目标资源。 只有这样，才能确保在转接到目标区域之后，VM 具有源中的所有功能和特性。

    > [!NOTE]
    > 为源 VM 启用复制时，Azure Site Recovery 会自动发现并创建虚拟网络和存储帐户；或者，你也可以预先创建这些资源，并在启用复制的过程中将其分配到 VM。 但是，需要按如下所述，在目标区域中手动创建其他任何资源。

     请参阅以下文档，根据源 VM 配置创建最常用的相关网络资源。

    - [网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [负载均衡器](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [公共 IP](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    对于其他任何网络组件，请参阅网络[文档](https://docs.microsoft.com/azure/#pivot=products&panel=network)。 

4. 若要在最终转接到目标区域之前测试配置，请在目标区域中手动[创建非生产网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。 这可以尽量减少对生产环境造成的干扰，也是建议的做法。
    
## <a name="copy-data-to-the-target-region"></a>将数据复制到目标区域
以下步骤引导你使用 Azure Site Recovery 将数据复制到目标区域。

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>在除了源区域之外的任意区域中创建保管库。

1. 登录到 [Azure 门户](https://portal.azure.com) > **恢复服务**。
2. 单击“创建资源” > “管理工具” > “备份和 Site Recovery”。
3. 在“名称”中，指定友好名称 **ContosoVMVault**。 如果有多个 订阅，请选择适当的订阅。
4. 创建资源组 **ContosoRG**。
5. 指定 Azure 区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
6. 在“恢复服务保管库”中，单击“概述” > “ConsotoVMVault” > “+复制”。
7. 在“源”中，选择“Azure”。
8. 在“源位置”中，选择当前运行 VM 的 Azure 源区域。
9. 选择“资源管理器”部署模型。 然后选择“源订阅”和“源资源组”。
10. 单击“确定”保存设置。

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>为 Azure VM 启用复制并开始复制数据。

Site Recovery 会检索与订阅和资源组关联的 VM 列表。

1. 在下一步骤中， 选择要移动的 VM。 然后单击“确定”。
3. 在“设置”中，单击“灾难恢复”。
4. 在“配置灾难恢复” > “目标区域”中，选择要复制到的目标区域。
5. 可以选择使用默认目标资源或使用预先创建的资源。
6. 单击“启用复制”。 这将启动用于为 VM 启用复制的作业。

    ![启用复制](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>测试配置


1. 导航到保管库，在“设置” > “复制的项”中，单击要移到目标区域的虚拟机，然后单击“+测试故障转移”图标。
2. 在“测试故障转移”中，选择要用于故障转移的恢复点：

   - **最新处理**：将 VM 故障转移到由 Site Recovery 服务处理的最新恢复点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低
   - **最新的应用一致**：此选项将所有 VM 故障转移到最新的应用一致恢复点。 将显示时间戳。
   - **自定义**：选择任何恢复点。

3. 选择要将 Azure VM 移到的目标 Azure 虚拟网络，以测试配置。 

> [!IMPORTANT]
> 我们建议使用单独的 Azure VM 网络来测试故障，而不要使用 VM 最终要移到的目标区域中的生产网络。

4. 若要开始测试移动，请单击“确定”。 若要跟踪进度，请单击 VM 以打开其属性。 或者，可以在保管库名称 >“设置” > “作业” > “Site Recovery 作业”中单击“测试故障转移”作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保 VM 正在运行、大小适当并已连接到相应的网络。
6. 若要删除测试移动期间创建的 VM，请在“复制的项”中单击“清理测试故障转移”。 在“说明”中，记录并保存与测试关联的任何观测结果。

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>执行移到目标区域的操作并确认。

1.  导航到保管库，在“设置” > “复制的项”中单击虚拟机，然后单击“故障转移”。
2. 在“故障转移”中，选择“最新”。 
3. 选择“在开始故障转移前关闭计算机”。 Site Recovery 在触发故障转移之前会尝试关闭源 VM。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。 
4. 作业完成后，检查 VM 是否按预期显示在目标 Azure 区域中。
5. 在“复制的项”中，右键单击 VM >“提交”。 这会完成移到目标区域的过程。 请等待提交作业完成。

## <a name="discard-the-resource-in-the-source-region"></a>丢弃源区域中的资源 

1. 导航到 VM。  单击“禁用复制”。  这会停止复制 VM 数据的过程。  

> [!IMPORTANT]
> 必须执行上述步骤才能避免移动后 Site Recovery 复制产生费用。

如果你不打算重复使用任何源资源，请继续执行下一组步骤。

1. 继续删除在[准备源 VM](#prepare-the-source-vms) 的步骤 4 中列出的、位于源区域中的所有相关网络资源。 
2. 删除源区域中的相应存储帐户。

## <a name="next-steps"></a>后续步骤

在本教程中，你将 Azure VM 移动到了一个不同的 Azure 区域。 现在，你可以为移动的 VM 配置灾难恢复。

> [!div class="nextstepaction"]
> [在迁移后设置灾难恢复](azure-to-azure-quickstart.md)


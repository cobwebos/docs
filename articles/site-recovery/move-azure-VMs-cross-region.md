---
title: 使用 Azure Site Recovery 服务将 Azure IaaS VM 移到另一 Azure 区域 | Microsoft Docs
description: 使用 Azure Site Recovery 将 Azure IaaS VM 从一个 Azure 区域移动到另一个 Azure 区域。
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: ec78e4c260c2ca5e0469f9373f60d8bca29ada7f
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375721"
---
# <a name="move-azure-vms-to-another-region"></a>将 Azure VM 移动到另一区域

你可能想要将 Azure 基础结构即服务 (IaaS) 虚拟机从一个区域移到另一个区域，以提高可靠性、可用性或更好地满足监管要求。 本教程介绍如何使用 Azure Site Recovery 将 VM 移到另一区域。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 验证先决条件
> * 准备源 VM
> * 准备目标区域
> * 将数据复制到目标区域
> * 测试配置
> * 执行移动
> * 丢弃源区域中的资源


> [!IMPORTANT]
> 本文介绍如何将 Azure VM 按原样从一个区域移到另一个区域。  如果你的目标是通过将 VM 移到可用性区域来提高基础结构的可用性，请参阅[将 Azure VM 移到可用性区域](move-azure-vms-avset-azone.md)。

## <a name="prerequisites"></a>先决条件

- 请确保要从中进行移动的源 Azure 区域中包含 Azure VM。 
- 验证所选的[源区域 - 目标区域组合是否受支持](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)，并慎重选择目标区域。
- 请确保了解[方案体系结构和组件](azure-to-azure-architecture.md)。
- 查看[支持限制和要求](azure-to-azure-support-matrix.md)。
- 验证帐户权限。 如果你刚刚创建了免费 Azure 帐户，那么你就是订阅的管理员。  如果你不是管理员，请咨询管理员，以获取所需的权限：
  -  若要启用 VM 复制并使用 Site Recovery 将数据复制到目标，必须有权在 Azure 资源中创建 VM。 “虚拟机参与者”内置角色具有这些权限。 使用这些权限可以：
        - 在所选资源组中创建 VM。
        - 在所选虚拟网络中创建 VM。
        - 向所选存储帐户进行写入。

  - 还需要管理 Site Recovery 操作的权限。 “Site Recovery 参与者”角色拥有管理 Azure 恢复服务保管库中 Site Recovery 操作所需的全部权限。

## <a name="prepare-the-source-vms"></a>准备源 VM

1. 检查要移动的 Azure VM 上是否存在最新的根证书。 如果没有，则会出于安全约束的原因，无法将数据复制到目标区域。

    - 对于 Windows VM，请安装最新的 Windows 更新，使所有受信任的根证书位于该计算机上。 在离线环境中，请遵循组织的标准 Windows 更新和证书更新过程。
    - 对于 Linux VM，请遵循 Linux 分销商的指导获取最新的受信任根证书和证书吊销列表。
2. 确保未使用身份验证代理来控制要移动的 VM 的网络连接。
3. 如果要移动的 VM 无法访问 Internet，并且使用防火墙代理来控制出站访问，请检查[此处](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms)所述的要求。
4. 阐述源网络布局以及当前使用的所有资源，包括（但不限于）要验证的负载均衡器、网络安全组和公共 IP 地址。

## <a name="prepare-the-target-region"></a>准备目标区域

1. 在 Azure 订阅中，验证是否允许在用于灾难恢复的目标区域中创建 VM。 如果需要，请联系支持人员启用所需的配额。

2. 确保订阅提供足够的资源用于支持源 VM。 如果使用 Site Recovery 将数据复制到目标区域，Site Recovery 会为目标 VM 选择相同的大小或最接近的可用大小。

3. 确保为源网络布局中标识的每个组件创建目标资源。 这可以确保 VM 在转接到目标区域之后，获得它们在源区域中具有的所有功能和特性。

   为源 VM 启用复制时，Azure Site Recovery 会自动发现并创建虚拟网络和存储帐户。 你也可以预先创建这些资源，并在执行启用复制的步骤期间将这些资源分配到 VM。 但是，其他任何资源必须在目标区域中手动创建。 请参阅以下文档，根据源 VM 的配置创建最常用的网络资源。

   - [网络安全组](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [负载均衡器](https://docs.microsoft.com/azure/load-balancer)
   - [公共 IP](../virtual-network/virtual-network-public-ip-address.md)
    
   对于其他任何网络组件，请参阅 [Azure 网络文档](https://docs.microsoft.com/azure/#pivot=products&panel=network)。 

4. 若要在执行移动之前测试配置，请在目标区域中手动[创建非生产网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。 测试设置可以尽量减少对生产环境造成的干扰，也是建议的做法。
    
## <a name="copy-data-to-the-target-region"></a>将数据复制到目标区域
以下步骤使用 Azure Site Recovery 将数据复制到目标区域。

### <a name="create-the-vault-in-any-region-except-the-source"></a>在除源以外的任一区域中创建保管库

1. 登录到 [Azure 门户](https://portal.azure.com) > **恢复服务**。
2. 选择“创建资源” > “管理工具” > “备份和 Site Recovery”    。
3. 在“名称”  中，指定易记名称 **ContosoVMVault**。 如果有多个订阅，请选择合适的一个。
4. 创建资源组 **ContosoRG**。
5. 指定 Azure 区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 对于“恢复服务保管库”，请选择“概述” > “ConsotoVMVault” > “+复制”。   
7. 对于“源”，请选择“Azure”。  
8. 对于“源位置”，请选择当前运行 VM 的 Azure 源区域。 
9. 选择“Azure 资源管理器”部署模型。 然后选择“源订阅”和“源资源组”。  
10. 选择“确定”以保存设置。 

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>为 Azure VM 启用复制并开始复制数据

Site Recovery 会检索与订阅和资源组关联的 VM 列表。

1. 选择要移动的 VM，然后选择“确定”。 
2. 对于“设置”，请选择“灾难恢复”。  
3. 对于“配置灾难恢复”   > “目标区域”  ，请选择要复制到的目标区域。
4. 选择使用默认目标资源或预先创建的资源。
5. 选择“启用复制”以启动作业。 

   ![启用复制](media/tutorial-migrate-azure-to-azure/settings.png)

 

## <a name="test-the-configuration"></a>测试配置


1. 转到保管库。 在“设置” > “复制的项”中，选择要移到目标区域的虚拟机。   然后选择“测试故障转移”  。
2. 在“测试故障转移”  中，选择要用于故障转移的恢复点：

   - **最新处理**：将 VM 故障转移到由 Site Recovery 服务处理的最新恢复点。 将显示时间戳。 使用此选项无需费时处理数据，因此恢复时间目标 (RTO) 会较低。
   - **最新的应用一致**：将所有 VM 故障转移到最新的应用一致性恢复点。 将显示时间戳。
   - **自定义**：选择任何恢复点。

3. 选择要将 Azure VM 移到的目标 Azure 虚拟网络，以测试配置。

   > [!IMPORTANT]
   > 我们建议使用单独的 Azure VM 网络来测试故障转移，而不要使用目标区域中的生产网络。

4. 若要开始测试移动，请选择“确定”。  若要跟踪进度，请选择 VM 以打开其“属性”。  或者，选择保管库中的“测试故障转移”作业。  然后选择“设置” > “作业” > “Site Recovery 作业”。   
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。  请确保 VM 正在运行、大小适当并已连接到相应的网络。
6. 若要删除创建的用于测试的 VM，请在“复制的项”中选择“清理测试故障转移”。  在“注释”中，记下并保存与测试相关的任何观测结果。 

## <a name="perform-the-move-and-confirm"></a>执行移动并确认

1. 转到保管库，在“设置” > “复制的项”中选择虚拟机，然后选择“故障转移”。   
1. 对于“故障转移”，请选择“最新”   。 
2. 选择“在开始故障转移前关闭计算机”  。 Site Recovery 在触发故障转移之前会尝试关闭源 VM。 但是，即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。 
3. 作业完成后，检查 VM 是否按预期显示在目标 Azure 区域中。
4. 在“复制的项”  中，右键单击 VM 并选择“提交”  。 随后会完成迁移。 请等待提交作业完成。

## <a name="discard-the-resources-from-the-source-region"></a>丢弃源区域中的资源

- 转到 VM 并选择“禁用复制”  。 这会停止复制 VM 数据的过程。

  > [!IMPORTANT]
  > 完成此步骤可避免在移动后 Site Recovery 复制产生费用。

如果不打算再次使用任何源资源，请执行以下步骤：

1. 删除在[准备源 VM](#prepare-the-source-vms) 的步骤 4 中列出的、位于源区域中的所有相关网络资源。
2. 删除源区域中的相应存储帐户。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何将 Azure VM 移到不同的 Azure 区域。 接下来，可为这些 VM 配置灾难恢复。

> [!div class="nextstepaction"]
> [在迁移后设置灾难恢复](azure-to-azure-quickstart.md)


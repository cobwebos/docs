---
title: 将虚拟机移动到另一个区域（Azure Site Recovery）
description: 了解如何将 SQL Server 虚拟机从 Azure 中的一个区域迁移到另一个区域。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3b84119cdcc1bb7e8603de64e3d23c69dac70cc3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74022288"
---
# <a name="move-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery-services"></a>使用 Azure Site Recovery 服务将 SQL Server VM 移到 Azure 中的另一个区域

本文介绍如何使用 Azure Site Recovery 将 SQL Server 虚拟机 (VM) 从 Azure 中的一个区域迁移到另一个区域。 

将 SQL Server VM 移到不同的区域需要做好以下准备工作：
1. [**准备**](#prepare-to-move)：确认源区域 SQL Server VM 和目标区域都已充分做好迁移准备。 
1. [**配置**](#configure-azure-site-recovery-vault)：移动 SQL Server VM 需要它是 Azure Site Recovery 保管库中的复制对象。 需要将 SQL Server VM 添加到 Azure Site Recovery 保管库。 
1. [**测试**](#test-move-process)：迁移 SQL Server VM 需要将它从源区域故障转移到复制的目标区域。 为了确保移动过程成功，首先需要测试 SQL Server VM 是否可以成功故障转移到目标区域。 这有助于发现任何问题，并避免在执行实际移动时出现这些问题。 
1. [**移动**](#move-the-sql-server-vm)：测试故障转移通过后，如果你可以安全地迁移 SQL Server VM，则可以将 VM 移动到目标区域。 
1. [**清除**](#clean-up-source-resources)：为了避免计费，请从保管库中删除 SQL Server VM，并将所有不必要的资源保留在资源组中。 

## <a name="verify-prerequisites"></a>验证先决条件 

- 确认[支持](../../../site-recovery/azure-to-azure-support-matrix.md#region-support)从源区域移到目标区域。  
- 查看[方案体系结构和组件](../../../site-recovery/azure-to-azure-architecture.md)以及[支持限制和要求](../../../site-recovery/azure-to-azure-support-matrix.md)。 
- 验证帐户权限。 如果你创建了免费 Azure 帐户，那么你就是订阅的管理员。 如果你不是订阅管理员，请要求管理员分配你所需的权限。 若要为 VM 启用复制并使用 Azure Site Recovery 复制数据，必须： 
    - 有权创建 VM。 “虚拟机参与者”内置角色具有这些权限，这包括： 
        - 有权在所选资源组中创建 VM。 
        - 有权在所选虚拟网络中创建 VM。 
        - 有权写入所选存储帐户。 
      - 管理 Azure Site Recovery 操作的权限。 “Site Recovery 参与者”角色拥有管理恢复服务保管库中 Site Recovery 操作所需的全部权限。  

## <a name="prepare-to-move"></a>准备移动
为移动操作准备源 SQL Server VM 和目标区域。 

### <a name="prepare-the-source-sql-server-vm"></a>准备源 SQL Server VM

- 确保要移动的 SQL Server VM 上存在所有最新的根证书。 如果没有最新的根证书，安全约束会阻止将数据复制到目标区域。 
- 对于 Windows VM，请在 VM 上安装所有最新的 Windows 更新，使所有受信任的根证书位于该计算机上。 在离线环境中，请遵循组织的标准 Windows 更新和证书更新过程。 
- 对于 Linux VM，请遵循 Linux 分销商提供的指导，在 VM 上获取最新的受信任根证书和证书吊销列表。 
- 确保未使用身份验证代理来控制要移动的 VM 的网络连接。 
- 如果尝试移动的 VM 无法访问 Internet，或使用防火墙代理来控制出站访问，请检查要求。 
- 确定源网络布局和当前正在使用的所有资源。 这包括但不限于负载均衡器、网络安全组 (NSG) 和公共 IP。 

### <a name="prepare-the-target-region"></a>准备目标区域

- 验证 Azure 订阅是否允许在用于灾难恢复的目标区域中创建 VM。 请联系支持部门，启用所需配额。 
- 请确保订阅中有足够的资源，能够支持大小与源 VM 匹配的 VM。 如果使用 Site Recovery 将数据复制到目标区域，Site Recovery 会为目标 VM 选择相同的大小或尽可能接近的大小。 
- 请确保为源网络布局中标识的每个组件创建目标资源。 此步骤对于确保目标区域中具有与源区域中相同的所有功能很重要。 
    - 为源 VM 启用复制时，Azure Site Recovery 会自动发现并创建虚拟网络。 此外可以预先创建网络，并将其分配到用户流中的 VM 以启用复制。 其他任何资源需要在目标区域中手动创建。
- 若要根据源 VM 配置创建最常用的相关网络资源，请参阅以下文档： 
    - [网络安全组](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [负载均衡器](../../../load-balancer/tutorial-load-balancer-basic-internal-portal.md)
    - [公共 IP 地址](../../../virtual-network/virtual-network-public-ip-address.md)
    - 对于其他任何网络组件，请参阅[网络文档](../../../virtual-network/virtual-networks-overview.md)。
- 若要在最终移动到目标区域之前测试配置，请在目标区域中手动创建非生产网络。 我们建议执行此步骤，因为这可以确保尽量减少对生产网络造成的干扰。 

## <a name="configure-azure-site-recovery-vault"></a>配置 Azure Site Recovery 保管库

以下步骤演示如何使用 Azure Site Recovery 将数据复制到目标区域。 在源区域以外的任何区域中创建恢复服务保管库。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 在导航窗格的左上角选择“创建资源”。 
1. **& 管理工具**中选择它，然后选择 "**备份" 和 "Site Recovery**"。 
1. 在 "**基本**信息" 选项卡上的 "**项目详细信息**" 下，在目标区域中创建新的资源组，或选择目标区域中的现有资源组。 
1. 在 "**实例详细信息**" 下，指定保管库的名称，然后从下拉范围中选择目标**区域**。 
1. 选择 "**查看 + 创建**" 创建恢复服务保管库。 
1. 选择导航窗格左上角的 "**所有服务**"，然后在 "搜索" 框中键入 "`recovery services`"。 
1. 同时选择 "**恢复服务保管库**" 旁边的星号，将其添加到快速导航栏中。 
1. 选择“恢复服务保管库”，然后选择创建的恢复服务保管库。 
1. 在“概述”窗格中选择“复制”。 

   ![配置复制](media/virtual-machines-windows-sql-move-to-new-region/configure-replication.png)

1. 选择“源”，然后选择“Azure”作为源。 为其他下拉字段（例如源 VM 的位置）选择适当的值。 “源资源组”字段中只会显示位于“源位置”区域中的资源组。 
1. 选择“虚拟机”，然后选择要迁移的虚拟机。 选择“确定”保存对 VM 所做的选择。 
1. 选择“设置”，然后从下拉列表中选择**目标位置**。 这应是前面准备好的资源组。 
1. 自定义复制后，选择“创建目标资源”以在新位置创建资源。 
1. 资源创建完成后，选择“启用复制”开始将 SQL Server VM 从源区域复制到目标区域。
1. 可以检查复制状态，方法是导航到你的恢复保管库，选择“复制的项”，然后查看 SQL Server VM 的“状态”。 “受保护”状态表示复制已完成。 

   ![检查复制状态](media/virtual-machines-windows-sql-move-to-new-region/check-replication-status.png)

## <a name="test-move-process"></a>测试移动过程
以下步骤说明如何使用 Azure Site Recovery 来测试移动过程。 

1. 在 **Azure 门户**中导航到你的[恢复服务保管库](https://portal.azure.com)，选择“复制的项”。 
1. 选择要移动的 SQL Server VM，检查“复制运行状况”是否显示为“正常”，然后选择“测试故障转移”。 

   ![VM 的测试故障转移](media/virtual-machines-windows-sql-move-to-new-region/test-failover-of-replicated-vm.png)

1. 在“测试故障转移”页上，选择用于故障转移的“最新应用一致性”恢复点，因为这是唯一可以保证 SQL Server 数据一致性的快照类型。 
1. 在“Azure 虚拟网络”下选择虚拟网络，然后选择“确定”以测试故障转移。 
   
   >[!IMPORTANT]
   > 我们建议使用单独的 Azure VM 网络来执行故障转移测试。 不要使用在启用复制时设置的生产网络和最终要将 VM 移动到其中的生产网络。 

1. 若要监视进度，请导航到你的保管库，在“监视”下选择“Site Recovery 作业”，然后选择正在进行的“测试故障转移”作业。

   ![监视故障转移测试的进度](media/virtual-machines-windows-sql-move-to-new-region/monitor-failover-test-job.png)

1. 测试完成后，在门户中导航到“虚拟机”，并查看新建的虚拟机。 确保 SQL Server VM 正在运行、大小适当并已连接到相应的网络。 
1. 删除测试过程中创建的 VM，因为在清理故障转移测试资源之前，“故障转移”选项会灰显。 导航回到保管库，依次选择“复制的项”、该 SQL Server VM、“清理测试故障转移”。 在 "**注释**" 部分记录并保存与测试关联的任何观测值，并选中 "测试完成" 旁边的复选框 **。删除测试故障转移虚拟机**。 测试后，选择“确定”以清理资源。 

   ![在故障转移测试后清理项](media/virtual-machines-windows-sql-move-to-new-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>移动 SQL Server VM 
以下步骤说明如何将 SQL Server VM 从源区域移到目标区域。 

1. 导航到“恢复服务”保管库，并依次选择“复制的项”、VM、“故障转移”。 

   ![启动故障转移](media/virtual-machines-windows-sql-move-to-new-region/initiate-failover.png)

1. 在“恢复点”下选择“最新的应用一致性”恢复点。 
1. 选中“在开始故障转移之前关闭计算机”旁边的复选框。 Site Recovery 在触发故障转移之前会尝试关闭源 VM。 即使关闭 VM 失败，故障转移也仍会继续。 
1. 选择“确定”开始故障转移。
1. 可以在上一部分所述的、监视故障转移测试时所看到的同一个“Site Recovery 作业”页上监视故障转移过程。 
1. 该作业完成后，请检查 SQL Server VM 是否按预期显示在目标区域中。 
1. 导航回到保管库，选择“复制的项”，选择该 SQL Server VM，然后选择“提交”以完成到目标区域的移动过程。 请等待提交作业完成。 
1. 向 SQL VM 资源提供程序注册你的 SQL Server VM，以在与资源提供程序关联的 Azure 门户和功能中启用**SQL 虚拟机**可管理性。 有关详细信息，请参阅[向 SQL VM 资源提供程序注册 SQL Server VM](virtual-machines-windows-sql-register-with-rp.md)。 

  > [!WARNING]
  > 只有应用一致性快照才能保证 SQL Server 数据一致性。 “最新处理”快照不可用于 SQL Server 故障转移，因为崩溃恢复快照无法保证 SQL Server 数据一致性。 

## <a name="clean-up-source-resources"></a>清理源资源
为了避免产生费用，请从保管库中删除 SQL Server VM，并删除所有不需要的相关资源。 

1. 导航回到“Site Recovery”保管库，选择“复制的项”，然后选择该 SQL Server VM。 
1. 选择“禁用复制”。 选择禁用保护的原因，然后选择“确定”以禁用复制。 

   >[!IMPORTANT]
   > 必须执行此步骤才能避免 Azure Site Recovery 复制产生费用。 

1. 如果你不打算重复使用源区域中的任何资源，请删除所有相关的网络资源和相应的存储帐户。 


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)



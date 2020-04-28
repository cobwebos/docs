---
title: 在 Azure Site Recovery 中为加密 Azure VM 启用复制
description: 本文介绍了对于其中磁盘启用了客户托管密钥 (CMK) 的 VM，如何使用 Site Recovery 配置从一个 Azure 区域到另一个区域的复制。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "75897957"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>复制其中磁盘启用了客户托管密钥 (CMK) 的计算机

本文介绍了如何将其中托管磁盘启用了客户托管密钥 (CMK) 的 Azure VM 从一个 Azure 区域复制到另一个区域。

## <a name="prerequisite"></a>先决条件
在为其中托管磁盘启用了 CMK 的虚拟机启用复制之前，必须在目标订阅的目标区域中创建磁盘加密集。

## <a name="enable-replication"></a>启用复制

在此示例中，主要的 Azure 区域是东亚的，次要区域是南部东亚。

1. 在保管库中选择“+复制”。 
2. 注意以下字段。
    - **源**：VM 的起始点，在本例中为 **Azure**。
    - **源位置**：要在其中保护虚拟机的 Azure 区域。 在此示例中，源位置为 "东亚"。
    - **部署模型**：源计算机的 Azure 部署模型。
    - **源订阅**：源虚拟机所属的订阅。 它可以是恢复服务保管库所在的同一 Azure Active Directory 租户中的任一订阅。
    - **资源组**：源虚拟机所属的资源组。 所选资源组中要保护的所有 VM 会在下一步骤中列出。

3. 在“虚拟机” > “选择虚拟机”中，选择要复制的每个 VM   。 只能选择可以启用复制的计算机。 选择“确定”。 

4. 在“设置”中，可以配置以下目标站点设置。 

    - **目标位置**：要在其中复制源虚拟机数据的位置。 Site Recovery 根据所选计算机的位置提供合适的目标区域列表。 我们建议使用与恢复服务保管库位置相同的位置。
    - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅与源订阅相同。
    - **目标资源组**：复制的虚拟机所属的资源组。 默认情况下，Site Recovery 会在目标区域中创建一个新的资源组， 其名称带有 `asr` 后缀。 如果已存在 Azure Site Recovery 创建的资源组，将会重复使用它。 此外，可按以下部分所述，选择对资源组进行自定义。 目标资源组的位置可以是除托管源虚拟机区域以外的任何 Azure 区域。
    - **目标虚拟网络**：默认情况下，Site Recovery 会在目标区域中创建一个新的虚拟网络， 其名称带有 `asr` 后缀。 此虚拟网络会映射到源网络并用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户（如果源 VM 不使用托管磁盘）** ：默认情况下，Site Recovery 会创建模拟源 VM 存储配置的新目标存储帐户。 如果已存在一个存储帐户，将重复使用它。
    - **副本托管磁盘（如果源 VM 使用托管磁盘）** ：Site Recovery 在目标区域新建托管磁盘副本，以生成和源 VM 的托管磁盘存储类型一致（标准或高级）的镜像磁盘。
    - **缓存存储帐户**：Site Recovery 需要源区域中称为“缓存存储”的额外存储帐户  。 源 VM 上的所有更改将受到跟踪并发送到缓存存储帐户。 它们随后会复制到目标位置。
    - **可用性集**：默认情况下，Site Recovery 会在目标区域中创建一个新的可用性集， 其名称带有 `asr` 后缀。 如果已存在 Site Recovery 创建的可用性集，将会重复使用它。
    - **磁盘加密集 (DES)** ：Site Recovery 需要将磁盘加密集用于副本和目标托管磁盘。 在启用复制之前，必须在目标订阅和目标区域中预先创建 DES。 默认情况下不会选择 DES。 必须单击 "自定义"，以选择每个源磁盘的 DES。
    - **复制策略**：定义恢复点保留期历史记录和应用一致性快照频率的设置。 默认情况下，Site Recovery 会使用恢复点保留期为 24 小时、应用一致性快照频率为 60 分钟的默认设置创建新的复制策略   。

    ![为其磁盘启用了 CMK 的计算机启用复制](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>自定义目标资源

遵循以下步骤修改 Site Recovery 默认目标设置。

1. 选择“目标订阅”旁边的“自定义”以修改默认目标订阅  。 从 Azure AD 租户中可用的订阅列表中选择订阅。

2. 选择“资源组、网络、存储和可用性集”旁边的“自定义”，以修改以下默认设置  ：
    - 对于“目标资源组”，请从订阅目标位置中的资源组列表中选择资源组。 
    - 对于“目标虚拟网络”，请从目标位置中的虚拟网络列表中选择网络。 
    - 对于“可用性集”，可将可用性集设置添加到 VM（如果它们是源区域中可用性集的一部分）。 
    - 对于“目标存储帐户”，请选择要使用的帐户。 

3. 选择“存储加密设置”旁边的“自定义”  ，为每个已启用客户托管密钥 (CMK) 的源托管磁盘选择目标 DES。 在选择时，还能够查看 DES 与哪个目标密钥保管库相关联。

4. 选择“创建目标资源” > “启用复制”。  
5. 为 VM 启用复制后，可以在“复制的项”下检查 VM 的运行状况  。

![为其磁盘启用了 CMK 的计算机启用复制](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>在初始复制期间，VM 状态刷新可能需要一段时间，但不显示确切的进度。 单击 "**刷新**" 以获取最新状态。

## <a name="faqs"></a>常见问题

* 我已在现有复制项上启用了 CMK，如何确保也在目标区域中应用 CMK？

    你可以找到副本托管磁盘的名称（由 Azure Site Recovery 在目标区域中创建），并将 DES 附加到此副本磁盘。 但是，一旦附加它，你将不能在“磁盘”边栏选项卡中看到 DES 详细信息。 另外，你可以选择禁用 VM 复制并重新启用它。 这将确保你可以在复制项的“磁盘”边栏选项卡中看到 DES 和密钥保管库的详细信息。

* 我将一个新的已启用 CMK 的磁盘添加到了复制项。 如何使用 Azure Site Recovery 复制此磁盘？

    不支持将新的已启用 CMK 的磁盘添加到现有复制项。 请为虚拟机禁用复制并重新启用复制。


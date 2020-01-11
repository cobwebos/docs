---
title: 在 Azure Site Recovery 中为加密的 Azure Vm 启用复制
description: 本文介绍如何使用 Site Recovery 为使用客户托管的密钥（CMK）启用的磁盘从一个 Azure 区域复制到另一个 Azure 区域的 Vm 配置复制。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897957"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>将计算机与客户托管的密钥（CMK）启用的磁盘一起复制

本文介绍如何将 Azure Vm 从一个 Azure 区域复制到另一个 Azure 区域，并将其复制到已启用客户托管的密钥（CMK）的托管磁盘。

## <a name="prerequisite"></a>先决条件
在为具有已启用 CMK 的托管磁盘的虚拟机启用复制之前，必须在目标订阅的目标区域中创建磁盘加密集。

## <a name="enable-replication"></a>启用复制

在此示例中，主要的 Azure 区域是东亚的，次要区域是南部东亚。

1. 在保管库中，选择 " **+ 复制**"。
2. 请注意以下字段。
    - **源**：VM 的起始点，在本例中为 **Azure**。
    - **源位置**：要在其中保护虚拟机的 Azure 区域。 在此示例中，源位置为 "东亚"。
    - **部署模型**：源计算机的 Azure 部署模型。
    - **源订阅**：源虚拟机所属的订阅。 它可以是与恢复服务保管库位于同一 Azure Active Directory 租户中的任何订阅。
    - **资源组**：源虚拟机所属的资源组。 所选资源组中的所有 Vm 都会在下一步中列出，以供保护。

3. 在 "**虚拟机**" > **选择 "虚拟机**"，然后选择要复制的每个虚拟机。 只能选择可以启用复制的计算机。 选择“确定”。

4. 在 "**设置**" 中，可以配置以下目标站点设置。

    - **目标位置**：复制源虚拟机数据的位置。 Site Recovery 根据所选计算机的位置提供适当目标区域的列表。 建议使用与恢复服务保管库所在位置相同的位置。
    - **目标订阅**：用于灾难恢复的目标订阅。 默认情况下，目标订阅与源订阅相同。
    - **目标资源组**：所有复制虚拟机所属的资源组。 默认情况下，Site Recovery 在目标区域中创建新的资源组。 名称获取 `asr` 后缀。 如果 Azure Site Recovery 创建的资源组已存在，则会重复使用该资源组。 你还可以选择对其进行自定义，如下一节所示。 目标资源组的位置可以是托管源虚拟机所在区域之外的任何 Azure 区域。
    - **目标虚拟网络**：默认情况下，Site Recovery 在目标区域中创建新的虚拟网络。 名称获取 `asr` 后缀。 它将映射到源网络，并用于任何将来的保护。 [详细了解](site-recovery-network-mapping-azure-to-azure.md)网络映射。
    - **目标存储帐户（如果源 vm 不使用托管磁盘）** ：默认情况下，Site Recovery 通过模拟源 vm 存储配置来创建新的目标存储帐户。 如果存储帐户已存在，则会重复使用它。
    - **副本托管磁盘（如果源 vm 使用托管磁盘）** ： Site Recovery 在目标区域中创建新的副本托管磁盘，以将相同存储类型（标准或高级）的源 vm 的托管磁盘与源 vm 的托管磁盘进行镜像。
    - **缓存存储帐户**： Site Recovery 需要在源区域中称为 "*缓存存储*" 的额外存储帐户。 源 Vm 上的所有更改都将被跟踪并发送到缓存存储帐户。 然后，将它们复制到目标位置。
    - **可用性集**：默认情况下，Site Recovery 在目标区域中创建新的可用性集。 名称具有 `asr` 后缀。 如果 Site Recovery 创建的可用性集已存在，则会重复使用。
    - **磁盘加密集（DES）** ： Site Recovery 需要将磁盘加密集用于副本和目标托管磁盘。 在启用复制之前，必须在目标订阅和目标区域中预先创建 DES。 默认情况下，不选择 DES。 必须单击 "自定义"，以选择每个源磁盘的 DES。
    - **复制策略**：定义恢复点保留历史记录和应用一致性快照频率的设置。 默认情况下，Site Recovery 会创建一个新的复制策略，其中默认设置为*24 小时*（对于应用一致性快照频率为24小时 *）和60分钟*。

    ![为启用了 CMK 的磁盘的计算机启用复制](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>自定义目标资源

按照以下步骤修改 Site Recovery 默认目标设置。

1. 选择 "目标订阅" 旁边的 "**自定义**" 来修改默认的目标订阅。 从 Azure AD 租户中可用的订阅列表中选择订阅。

2. 选择 "资源组、网络、存储和可用性集" 旁边的 "**自定义**"，以修改以下默认设置：
    - 对于 "**目标资源组**"，从订阅目标位置中的资源组列表中选择资源组。
    - 对于 "**目标虚拟网络**"，请从目标位置中的虚拟网络列表中选择网络。
    - 对于**可用性集**，如果虚拟机属于源区域中的可用性集，则可以向其添加可用性集设置。
    - 对于**目标存储帐户**，请选择要使用的帐户。

3. 选择 "存储加密设置" 旁边的 "**自定义**"，为每个已启用客户托管的密钥（CMK）的源托管磁盘选择目标 DES。 选择时，还可以查看 DES 与哪个目标密钥保管库相关联。

4. 选择 "**创建目标资源**" > "**启用复制**"。
5. 为 Vm 启用复制后，可以在 "**复制的项**" 下查看 vm 的运行状况状态。

![为启用了 CMK 的磁盘的计算机启用复制](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>在初始复制期间，状态可能需要一些时间来刷新，而不会有明显的进度。 单击 "**刷新**" 以获取最新状态。

## <a name="faqs"></a>常见问题

* 我对现有复制项启用了 CMK，如何确保 CMK 也应用于目标区域？

    你可以找到副本托管磁盘的名称（由目标区域中的 Azure Site Recovery 创建），并将 DES 附加到此副本磁盘。 但是，在附加磁盘边栏选项卡后，你将无法在其上查看 DES 详细信息。 或者，你可以选择禁用 VM 复制并再次启用它。 这将确保在复制的项的 "磁盘" 边栏选项卡中看到 DES 和密钥保管库的详细信息。

* 我已将一个新的 CMK 启用磁盘添加到复制的项。 如何将此磁盘与 Azure Site Recovery 进行复制？

    不支持将新的 CMK 启用的磁盘添加到现有复制的项。 为虚拟机禁用复制并再次启用复制。


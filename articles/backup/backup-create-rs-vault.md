---
title: 创建恢复服务保管库
description: 本文介绍如何创建用于存储备份和恢复点的恢复服务保管库。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 6ac1c7e887f80767d6ff1819476e91cb4b06bf1b
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744948"
---
# <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

若要创建恢复服务保管库，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。

2. 在左侧菜单中，选择“所有服务”  。

    ![选择“所有服务”](./media/backup-create-rs-vault/click-all-services.png)

3. 在“所有服务”对话框中，输入“恢复服务”   。 资源列表根据输入进行筛选。 在资源列表中，选择“恢复服务保管库”  。

    ![输入并选择“恢复服务保管库”](./media/backup-create-rs-vault/all-services.png)

    此时会显示订阅中的恢复服务保管库列表。

4. 在“恢复服务保管库”仪表板上，选择“添加”   。

    ![添加恢复服务保管库](./media/backup-create-rs-vault/add-button-create-vault.png)

    此时会打开“恢复服务保管库”对话框  。 提供“名称”、“订阅”、“资源组”和“位置”的值     。

    ![配置恢复服务保管库](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Name**：输入一个友好名称以标识此保管库。 名称对于 Azure 订阅必须是唯一的。 指定的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头且只能包含字母、数字和连字符。
   - **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
   - **资源组**：使用现有资源组，或创建一个新的资源组。 要查看订阅中可用的资源组列表，请选择“使用现有资源”****，然后从下拉列表框中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称  。 有关资源组的完整信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
   - **位置**：为保管库选择地理区域。 要创建保管库以保护虚拟机，保管库必须与虚拟机位于同一区域中****。

      > [!IMPORTANT]
      > 如果不确定 VM 的位置，请关闭对话框。 转到门户中的虚拟机列表。 如果虚拟机位于多个区域，请在每个区域中创建一个恢复服务保管库。 先在第一个位置创建保管库，然后再为其他位置创建保管库。 无需指定存储帐户即可存储备份数据。 恢复服务保管库和 Azure 备份服务会自动处理这种情况。
      >
      >

5. 准备好创建恢复服务保管库后，选择“创建”  。

    ![创建恢复服务保管库](./media/backup-create-rs-vault/click-create-button.png)

    创建恢复服务保管库可能需要一段时间。 可在门户右上角“通知”区域监视状态通知  。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”  。

     ![刷新备份保管库列表](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>设置存储冗余

Azure 备份会自动处理保管库的存储。 需要指定如何复制该存储。

1. 在“恢复服务保管库”**** 边栏选项卡中，单击新保管库。 在“设置”部分下，单击“属性”********。
2. 在“属性”**** 中的“备份配置”**** 下，单击“更新”****。

3. 选择存储复制类型，然后单击“保存”****。

     ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - 如果使用 Azure 作为主要备份存储终结点，则我们建议继续使用默认的“异地冗余”设置。****
   - 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。****
   - 详细了解[异地冗余](../storage/common/storage-redundancy-grs.md)和[本地冗余](../storage/common/storage-redundancy-lrs.md)。

> [!NOTE]
> 在保管库中配置备份之前，必须更改恢复服务保管库的**存储复制类型**（本地冗余/异地冗余）。 配置备份后，将禁用修改选项，并且无法更改**存储复制类型**。

## <a name="set-cross-region-restore"></a>设置跨区域还原

作为一个还原选项，跨区域还原（CRR）允许你在辅助区域（即[Azure 配对区域](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)）中还原 Azure vm。 此选项可让你：

- 有审核或符合性要求时执行演练
- 如果主要区域发生灾难，请还原 VM 或磁盘。

若要选择此功能，请从 "**备份配置**" 边栏选项卡中选择 "**启用跨区域还原**"。

对于此过程，定价会影响到存储级别。

>[!NOTE]
>开始之前：
>
>- 有关支持的托管类型和区域的列表，请参阅[支持矩阵](backup-support-matrix.md#cross-region-restore)。
>- 现在，在所有 Azure 公共区域中预览跨区域还原（CRR）功能。
>- CRR 是适用于任何 GRS 保管库的保管库级别选择功能（默认情况下处于关闭状态）。
>- 请使用以下命令将你的订阅加入此功能：<br>
>  `Register-AzProviderFeature -FeatureName CrossRegionRestore -ProviderNamespace Microsoft.RecoveryServices`
>- 如果你在公开限制预览期间载入此功能，审阅批准电子邮件将包含定价策略详细信息。
>- 选择加入后，最多可能需要48小时才能使备份项在辅助区域中可用。
>- 目前仅支持对备份管理类型-ARM Azure VM 执行 CRR （不支持经典 Azure VM）。  如果其他管理类型支持 CRR，则会**自动**注册。

### <a name="configure-cross-region-restore"></a>配置跨区域还原

使用 GRS 冗余创建的保管库包含用于配置跨区域还原功能的选项。 每个 GRS 保管库都将有一个横幅，它将链接到文档。 若要为保管库配置 CRR，请前往 "备份配置" 边栏选项卡，其中包含启用此功能的选项。

 ![备份配置横幅](./media/backup-azure-arm-restore-vms/banner.png)

1. 从门户中转到 "恢复服务保管库" > 设置 "> 属性"。
2. 单击 "**在此保管库中启用跨区域还原**" 以启用该功能。

   ![在此保管库中单击 "启用跨区域还原" 之前](./media/backup-azure-arm-restore-vms/backup-configuration1.png)

   ![在此保管库中单击 "启用跨区域还原" 后](./media/backup-azure-arm-restore-vms/backup-configuration2.png)

了解如何[查看次要区域中的备份项](backup-azure-arm-restore-vms.md#view-backup-items-in-secondary-region)。

了解如何[在次要区域中还原](backup-azure-arm-restore-vms.md#restore-in-secondary-region)。

了解如何[监视次要区域还原作业](backup-azure-arm-restore-vms.md#monitoring-secondary-region-restore-jobs)。

## <a name="modifying-default-settings"></a>修改默认设置

强烈建议在保管库中配置备份之前，查看**存储复制类型**和**安全设置**的默认设置。

- 默认情况下，**存储复制类型**设置为**异地冗余**。 配置备份后，将禁用修改的选项。 请按照以下[步骤](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)查看并修改设置。

- 默认情况下，在新创建的保管库上**启用****软删除**，以防止意外或恶意删除备份数据。 请按照以下[步骤](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#enabling-and-disabling-soft-delete)查看并修改设置。

## <a name="next-steps"></a>后续步骤

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
[了解](backup-azure-delete-vault.md)如何删除恢复服务保管库。

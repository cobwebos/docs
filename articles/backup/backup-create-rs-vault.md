---
title: 创建恢复服务保管库
description: 本文介绍如何创建用于存储备份和恢复点的恢复服务保管库。
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 144d8cdb870e12474dfc47784749b5f0e466f8bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273399"
---
# <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

若要创建恢复服务保管库，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。

2. 在左侧菜单中，选择“所有服务”。

    ![选择“所有服务”](./media/backup-create-rs-vault/click-all-services.png)

3. 在“所有服务”对话框中，输入“恢复服务”。 资源列表根据输入进行筛选。 在资源列表中，选择“恢复服务保管库”。

    ![输入并选择“恢复服务保管库”](./media/backup-create-rs-vault/all-services.png)

    此时会显示订阅中的恢复服务保管库列表。

4. 在“恢复服务保管库”仪表板上，选择“添加”。

    ![添加恢复服务保管库](./media/backup-create-rs-vault/add-button-create-vault.png)

    此时会打开“恢复服务保管库”对话框。 提供“名称”、“订阅”、“资源组”和“位置”的值。

    ![配置恢复服务保管库](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **名称**：输入一个友好名称以标识此保管库。 名称对于 Azure 订阅必须是唯一的。 指定的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头且只能包含字母、数字和连字符。
   - **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
   - **资源组**：使用现有资源组或创建新组。 要查看订阅中可用的资源组列表，请选择“使用现有资源”，然后从下拉列表框中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称。 有关资源组的完整信息，请参阅 [Azure 资源管理器概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
   - **位置**：选择保管库的地理区域。 要创建保管库以保护虚拟机，保管库必须与虚拟机位于同一区域中。

      > [!IMPORTANT]
      > 如果不确定 VM 的位置，请关闭对话框。 转到门户中的虚拟机列表。 如果虚拟机位于多个区域，请在每个区域中创建一个恢复服务保管库。 先在第一个位置创建保管库，然后再为其他位置创建保管库。 无需指定存储帐户即可存储备份数据。 恢复服务保管库和 Azure 备份服务会自动处理这种情况。
      >
      >

5. 准备好创建恢复服务保管库后，选择“创建”。

    ![创建恢复服务保管库](./media/backup-create-rs-vault/click-create-button.png)

    创建恢复服务保管库可能需要一段时间。 可在门户右上角“通知”区域监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”。

     ![刷新备份保管库列表](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>设置存储冗余

Azure 备份会自动处理保管库的存储。 需要指定如何复制该存储。

1. 从“恢复服务保管库”边栏选项卡中，单击新保管库 。 在“设置”部分下，单击“属性”。
2. 在“属性”中的“备份配置”下，单击“更新”。

3. 选择存储复制类型，然后单击“保存”。

     ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - 如果使用 Azure 作为主要备份存储终结点，则我们建议继续使用默认的“异地冗余”设置。
   - 如果不使用 Azure 作为主要的备份存储终结点，则请选择“本地冗余”，减少 Azure 存储费用。
   - 详细了解[异地冗余](../storage/common/storage-redundancy-grs.md)和[本地冗余](../storage/common/storage-redundancy-lrs.md)。

> [!NOTE]
> 在保管库中配置备份之前，必须更改恢复服务保管库的**存储复制类型**（本地冗余/异地冗余）。 配置备份后，将禁用修改选项，并且无法更改**存储复制类型**。

## <a name="modifying-default-settings"></a>修改默认设置

强烈建议在保管库中配置备份之前，查看**存储复制类型**和**安全设置**的默认设置。 
* 默认情况下，**存储复制类型**设置为**异地冗余**。 配置备份后，将禁用修改的选项。 请按照以下[步骤](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)查看并修改设置。 
* 默认情况下，在新创建的保管库上**启用** **软删除**，以防止意外或恶意删除备份数据。 请按照以下[步骤](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete)查看并修改设置。


## <a name="next-steps"></a>后续步骤

[了解](backup-azure-recovery-services-vault-overview.md)恢复服务保管库。
[了解](backup-azure-delete-vault.md)如何删除恢复服务保管库。

---
title: include 文件
description: include 文件
services: backup
author: markgalioto
ms.service: backup
ms.topic: include
ms.date: 5/14/2018
ms.author: markgal
ms.custom: include file
ms.openlocfilehash: 5590da80a1c217e7902e8e010688e40f5624898c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34664901"
---
## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
恢复服务保管库是用于存储在不同时间创建的备份和恢复点的实体。 恢复服务保管库还包含与受保护虚拟机关联的备份策略。

若要创建恢复服务保管库，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com/)中的订阅。
2. 在左侧菜单中，选择“所有服务”。

    ![选择主菜单中的“所有服务”选项](./media/backup-create-rs-vault/click-all-services.png) <br/>

3. 在“所有服务”对话框中，键入“恢复服务”。 开始键入时，会根据输入筛选资源列表。 出现“恢复服务保管库”时，请选择它。

    ![在“所有服务”对话框中键入“恢复服务”](./media/backup-create-rs-vault/all-services.png) <br/>

    此时会显示订阅中的恢复服务保管库列表。
4. 在“恢复服务保管库”菜单中，选择“添加”。

    ![创建恢复服务保管库步骤 2](./media/backup-create-rs-vault/add-button-create-vault.png)

    此时会打开“恢复服务保管库”菜单。 此窗格中会提示输入“名称”、“订阅”、“资源组”和“位置”的信息。

    ![“恢复服务保管库”窗格](./media/backup-create-rs-vault/create-new-vault-dialog.png)
5. 对于“名称”，请输入一个友好名称以标识保管库 。 名称对于 Azure 订阅必须是唯一的。 键入的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头，只能包含字母、数字和连字符。
6. 对于“订阅”，选择要使用的订阅。 如果你仅是一个订阅的成员，会显示该名称。 如果不确定要使用哪个订阅，请使用默认（或推荐）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
7. 对于“资源组”，可以使用现有资源组，也可以创建新组。 如要查看订阅中可用的资源组列表，请选择“使用现有项”，并单击下拉菜单。 若要创建新资源组，请选择“新建”，然后键入名称。 有关资源组的完整信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/resource-group-overview.md)。
8. 对于“位置”，请选择保管库的地理区域。 如要创建保管库以保护虚拟机，保管库必须与虚拟机位于同一区域中。

   > [!IMPORTANT]
   > 如果不确定 VM 的所在位置，请关闭保管库创建对话框，并转到门户中的虚拟机列表。 如果多个区域中有虚拟机，请在每个区域中创建恢复服务保管库。 请先在第一个位置创建保管库，然后转到下一个位置。 无需指定存储帐户即可存储备份数据。 恢复服务保管库和 Azure 备份服务会自动处理这种情况。
   >
   >

9. 恢复服务的创建准备就绪后，单击“创建”。

    ![备份保管库列表](./media/backup-create-rs-vault/click-create-button.png)

    创建恢复服务保管库可能需要一段时间。 监视“通知”部分（门户右上区域）中的状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果仍未看见保管库，请单击“刷新”。

     ![备份保管库列表](./media/backup-create-rs-vault/refresh-button.png)

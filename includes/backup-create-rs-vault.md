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
ms.openlocfilehash: 0ff9bf829e2dbe1bca078360ccded94bad63d9a6
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249455"
---
## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库
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
   - **资源组**：使用现有资源组或创建新组。 要查看订阅中可用的资源组列表，请选择“使用现有资源”，然后从下拉列表框中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称。 有关资源组的完整信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/resource-group-overview.md)。
   - **位置**：选择保管库的地理区域。 要创建保管库以保护虚拟机，保管库必须与虚拟机位于同一区域中。

      > [!IMPORTANT]
      > 如果不确定 VM 的位置，请关闭对话框。 转到门户中的虚拟机列表。 如果虚拟机位于多个区域，请在每个区域中创建一个恢复服务保管库。 先在第一个位置创建保管库，然后再为其他位置创建保管库。 无需指定存储帐户即可存储备份数据。 恢复服务保管库和 Azure 备份服务会自动处理这种情况。
      >
      >

5. 准备好创建恢复服务保管库后，选择“创建”。

    ![创建恢复服务保管库](./media/backup-create-rs-vault/click-create-button.png)

    创建恢复服务保管库可能需要一段时间。 可在门户右上角“通知”区域监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”。

     ![刷新备份保管库列表](./media/backup-create-rs-vault/refresh-button.png)

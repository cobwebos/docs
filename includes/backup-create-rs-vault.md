---
title: include 文件
description: include 文件
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: dacurwin
ms.custom: include file
ms.openlocfilehash: c0f717e26534151585a51badf4bb1fe1ba58eb00
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970513"
---
## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是存储一段时间内创建的恢复点的管理实体，它提供了用于执行与备份相关的操作的接口。 其中包括按需备份、执行还原和创建备份策略。

若要创建恢复服务保管库，请执行以下步骤。

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。

1. 在左侧菜单中，选择“所有服务”。

    ![选择“所有服务”](./media/backup-create-rs-vault/click-all-services.png)

1. 在“所有服务”对话框中，输入“恢复服务” 。 资源列表根据输入进行筛选。 在资源列表中，选择“恢复服务保管库”。

    ![输入并选择“恢复服务保管库”](./media/backup-create-rs-vault/all-services.png)

    此时会显示订阅中的恢复服务保管库列表。

1. 在“恢复服务保管库”仪表板上，选择“添加” 。

    ![添加恢复服务保管库](./media/backup-create-rs-vault/add-button-create-vault.png)

    此时会打开“恢复服务保管库”对话框。 提供“名称”、“订阅”、“资源组”和“位置”的值   。

    ![配置恢复服务保管库](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **名称**：输入一个友好名称以标识此保管库。 名称对于 Azure 订阅必须是唯一的。 指定的名称应至少包含 2 个字符，最多不超过 50 个字符。 名称必须以字母开头且只能包含字母、数字和连字符。
   - **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
   - **资源组**：使用现有资源组，或创建一个新的资源组。 若要查看订阅中可用的资源组列表，请选择“使用现有资源”****，然后从下拉列表中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../articles/azure-resource-manager/management/overview.md)。
   - **位置**：为保管库选择地理区域。 若要创建保管库来保护任何数据源，保管库*必须*与数据源位于同一区域。

      > [!IMPORTANT]
      > 如果不确定数据源的位置，请关闭对话框。 在门户中，前往资源列表。 如果有多个区域的数据源，请为每个区域创建一个恢复服务保管库。 在为另一个位置创建保管库之前，请先在第一个位置创建保管库。 无需指定存储帐户即可存储备份数据。 恢复服务保管库和 Azure 备份会自动处理这种情况。
      >
      >

1. 准备好创建恢复服务保管库后，选择“创建”。

    ![创建恢复服务保管库](./media/backup-create-rs-vault/click-create-button.png)

    创建恢复服务保管库可能需要一段时间。 可在门户右上角“通知”区域监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”。

     ![刷新备份保管库列表](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> 在保管库中配置备份之前，我们强烈建议检查“存储复制类型”和“安全设置”的默认设置。  有关详细信息，请参阅[设置存储冗余](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)部分。

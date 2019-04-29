---
title: 使用 Azure 门户创建 IoT 中心 | Microsoft Docs
description: 如何通过 Azure 门户创建、管理和删除 Azure IoT 中心。 包括有关定价层、缩放、安全性和消息传递配置的信息。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: robinsh
ms.openlocfilehash: 21758d112cd7eaf45b8b279cf2a6f6060c44c9c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741960"
---
# <a name="create-an-iot-hub-using-the-azure-portal"></a>使用 Azure 门户创建 IoT 中心

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

本文介绍如何使用 [Azure 门户](https://portal.azure.com)创建和管理 IoT 中心。

需要一个 Azure 订阅才能完成此教程中的步骤。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="change-the-settings-of-the-iot-hub"></a>更改 IoT 中心的设置

通过“IoT 中心”窗格创建 IoT 中心后可以更改其设置。

![显示了 IoT 中心设置的屏幕截图](./media/iot-hub-create-through-portal/iot-hub-settings-panel.png)

下面是一些可以为 IoT 中心设置的属性：

**定价和缩放**:此属性可用于迁移到其他层或设置单位的 IoT 中心的数目。 

**操作监视**:打开或关闭，不同的监视类别例如日志记录与设备到云的消息或云到设备消息相关的事件。

**IP 筛选器**:指定范围内的 IP 地址将接受或拒绝的 IoT 中心。

**属性**：提供的属性列表，你可以复制，并使用其他位置，例如资源 ID、 资源组、 位置和等等。

### <a name="shared-access-policies"></a>共享访问策略

还可查看或修改共享的访问策略的列表，方法是单击“设置”部分中的“共享访问策略”。 这些策略定义设备与服务连接到 IoT 中心所需的权限。 

单击“添加”以打开“添加共享访问策略”边栏选项卡。  可输入新的策略名称以及想要与此策略关联的权限，如下图所示：

![显示了添加共享访问策略的屏幕截图](./media/iot-hub-create-through-portal/iot-hub-add-shared-access-policy.png)

* “注册表读取”和“注册表写入”策略用于向标识注册表授予读取和写入访问权限。 选择写入选项会自动选择读取选项。

* “服务连接”策略授予访问服务终结点的权限，例如“接收设备到云”。 

* “设备连接”策略授予使用 IoT 中心的设备端终结点发送和接收消息的权限。

单击“**创建**”将此新建策略添加到现有列表。

## <a name="message-routing-for-an-iot-hub"></a>IoT 中心的消息路由

单击“消息传送”下的“消息路由”，查看消息路由窗格，可在其中定义中心的路由和自定义终结点。 通过[消息路由](iot-hub-devguide-messages-d2c.md)，可以管理数据从设备发送到终结点的方式。 第一步是添加新路由。 然后可以将现有的终结点添加到路由，或新建一个类型受支持的路由，例如 blob 存储类型。 

![“消息路由”窗格](./media/iot-hub-create-through-portal/iot-hub-message-routing.png)

### <a name="routes"></a>路由

路由是“消息路由”窗格上的第一个选项卡。 若要添加新路由，请单击“+”（添加）。 随即显示以下屏幕。 

![显示了添加新路由的屏幕截图](./media/iot-hub-create-through-portal/iot-hub-add-route-storage-endpoint.png)

为中心命名。 该名称在该中心的路由列表中必须是唯一的。 

对于“终结点”，可从下拉列表中选择一个或新添加一个。 在此示例中，已有存储帐户和容器。 若要将其添加为终结点，请单击终结点下拉列表旁的“+”（添加）并选择“Blob 存储”。 以下屏幕显示指定存储帐户和容器的位置。

![屏幕截图显示为路由规则添加存储终结点。](./media/iot-hub-create-through-portal/iot-hub-routing-add-storage-endpoint.png)

单击“选取容器”以选择存储帐户和容器。 如果已选择这些字段，会返回终结点窗格。 其余字段均采用默认值，并单击“创建”创建存储帐户的终结点并将其添加到路由规则。

**数据源**选择“设备遥测消息”。 

接下来，添加一个路由查询。 在此示例中，具有名为 `level` 的应用程序属性且其值等于 `critical` 的消息被路由到存储帐户。

![屏幕截图显示保存新路由规则这一操作](./media/iot-hub-create-through-portal/iot-hub-add-route.png)

单击“保存”以保存路由规则。 随即返回消息路由窗格，并显示新的路由规则。

### <a name="custom-endpoints"></a>自定义终结点

单击“自定义终结点”选项卡。会显示所有已创建的自定义终结点。 在此处，可添加新的终结点或删除现有终结点。 

> [!NOTE]
> 如果删除一个路由，不会删除分配到该路由的终结点。 若要删除终结点，单击“自定义终结点”选项卡，选择要删除的终结点并单击“删除”。
>

有关自定义终结点的详细信息，请阅读[参考 - IoT 中心终结点](iot-hub-devguide-endpoints.md)。

可为一个 IoT 中心定义最多 10 个自定义终结点。 

若要查看如何结合使用自定义终结点和路由的完整示例，请参阅[IoT 中心消息路由](tutorial-routing.md)。

## <a name="find-a-specific-iot-hub"></a>查找特定 IoT 中心

下面是两种在订阅中查找特定 IoT 中心的方法：

1. 如果知道 IoT 中心所属的资源组，单击“资源组”，然后从列表中选择资源组。 资源组屏幕会显示组中的所有资源，包括 IoT 中心。 单击所查找的中心。

2. 单击“所有资源”。 在“所有资源”窗格上，有一个下拉列表，默认为 `All types`。 单击该下拉列表，取消选中 `Select all`。 查找 `IoT Hub` 并选中。 单击下拉列表框将其关闭，系统将筛选所含条目，并仅显示所选的 IoT 中心。

## <a name="delete-the-iot-hub"></a>删除 IoT 中心

若要删除 IoT 中心，查找要删除的 IoT 中心，然后单击该 IoT 中心名称下的“删除”按钮。

## <a name="next-steps"></a>后续步骤

若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [IoT 中心消息路由](tutorial-routing.md)
* [IoT 中心指标](iot-hub-metrics.md)
* [操作监视](iot-hub-operations-monitoring.md)
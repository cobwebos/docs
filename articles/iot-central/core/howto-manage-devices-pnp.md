---
title: 在 Azure IoT Central 应用程序中管理设备 | Microsoft Docs
description: 如何以操作员的身份在 Azure IoT Central 应用程序中管理设备。
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b5e4e27e059cdc84370bbf7dbf7c6bc651b1968e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177354"
---
# <a name="manage-devices-in-your-azure-iot-central-application-preview-features"></a>管理 Azure IoT Central 应用程序中的设备（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍如何以操作员的身份在 Azure IoT Central 应用程序中管理设备。 操作员可以：

- 使用 "**设备**" 页可以查看、添加和删除连接到 Azure IoT Central 应用程序的设备。
- 维护最新的设备清单。
- 通过从视图中更改存储在设备属性中的值，使你的设备元数据保持最新。
- 通过从视图更新特定设备上的设置来控制设备的行为。

## <a name="view-your-devices"></a>查看设备

若要查看单个设备：

1. 选择左窗格中的 "**设备**"。 此时会显示所有设备和设备模板的列表。

1. 选择设备模板。

1. 在 "**设备**" 页的右侧窗格中，可以看到从该设备模板创建的设备列表。 选择单个设备以查看该设备的“设备详细信息”页：

    ![设备详细信息页](./media/howto-manage-devices-pnp/devicelist.png)


## <a name="add-a-device"></a>添加设备

若要将设备添加到 Azure IoT Central 应用程序：

1. 选择左窗格中的 "**设备**"。

1. 选择要从中创建设备的设备模板。

1. 选择“+ 新建”。

1. **打开或** **关闭** **模拟**切换。 真实设备是指要连接到 Azure IoT Central 应用程序的物理设备。 模拟设备包含 Azure IoT Central 生成的示例数据。

1. 单击“创建”。

1. 此设备现在显示在此模板的设备列表中。 选择设备以查看包含设备的所有视图的 "设备详细信息" 页。

## <a name="import-devices"></a>导入设备

若要将大量设备连接到应用程序，可从 CSV 文件批量导入设备。 该 CSV 文件应包含以下列和标头：

* **IOTC_DeviceID** - 设备 ID 应全部小写。
* **IOTC_DeviceName** - 此列是可选的。

若要在应用程序中批量注册设备：

1. 选择左窗格中的 "**设备**"。

1. 在左面板中，选择要为其批量创建设备的设备模板。

    > [!NOTE]
    > 如果还没有设备模板，则可以在 "**所有设备**" 下导入设备，并在不使用模板的情况下注册设备。 导入设备后，可以将其迁移到模板。

1. 选择“导入”。

    ![导入操作](./media/howto-manage-devices-pnp/bulkimport1a.png)


1. 选择包含要导入的设备 ID 列表的 CSV 文件。

1. 上传文件后，会立即开始导入设备。 可以在设备操作面板中跟踪导入状态。 此面板会在导入开始后自动显示，也可以通过右上角的钟形图标进行访问。

1. 导入完成后，"设备操作" 面板中将显示一条成功消息。

    ![导入成功](./media/howto-manage-devices-pnp/bulkimport3a.png)


如果设备导入操作失败，则会在设备操作面板上看到一条错误消息。 系统会生成一个捕获所有错误的可下载的日志文件。

**将设备迁移到模板**

如果通过在 "**所有设备**" 下启动导入来注册设备，则会在不使用任何设备模板关联的情况下创建设备。 设备必须与模板关联起来，才能浏览有关设备的数据和其他详细信息。 请按照以下步骤将设备与模板进行关联：

1. 选择左窗格中的 "**设备**"。

1. 在左侧面板中，选择 "**所有设备**"：

    ![未关联的设备](./media/howto-manage-devices-pnp/unassociateddevices1a.png)


1. 使用网格上的筛选器确定是否有任何设备的 "**设备模板**" 列中的值为 "未关联"。

1. 选择想要与模板关联的设备：

1. 选择**迁移**：

    ![关联设备](./media/howto-manage-devices-pnp/unassociateddevices2a.png)


1. 从可用模板列表中选择模板，然后选择 "**迁移**"。

1. 所选设备与所选设备模板相关联。


## <a name="export-devices"></a>导出设备

若要将真实设备连接到 IoT Central，需要其连接字符串。 你可以批量导出设备详细信息，以获取创建设备连接字符串所需的信息。 导出过程将创建一个 CSV 文件，其中包含所有选定设备的设备标识、设备名称和密钥。

从应用程序中批量导出设备：

1. 选择左窗格中的 "**设备**"。

1. 在左侧窗格中，选择要从中导出设备的设备模板。

1. 选择要导出的设备，然后选择**导出**操作。

    ![导出](./media/howto-manage-devices-pnp/export1a.png)


1. 导出过程开始。 可以使用设备操作面板来跟踪状态。

1. 导出完成后，将显示一条成功消息，并提供一个用来下载生成文件的链接。

1. 选择 "**下载文件**" 链接，将该文件下载到磁盘上的本地文件夹。

    ![导出成功](./media/howto-manage-devices-pnp/export2a.png)


1. 导出的 CSV 文件包含以下列：设备 ID、设备名称、设备密钥和 X509 证书指纹：

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

有关连接字符串和将实际设备连接到 IoT Central 应用程序的详细信息，请参阅[Azure IoT Central 中的设备连接](overview-iot-central-get-connected-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)。

## <a name="delete-a-device"></a>删除设备

若要从 Azure IoT Central 应用程序中删除真实设备或模拟设备：

1. 选择左窗格中的 "**设备**"。

1. 选择要删除的设备的设备模板。

1. 使用筛选器工具来筛选和搜索设备。 选中要删除的设备旁边的复选框。

1. 选择“删除”。 可以在设备操作面板中跟踪此删除操作的状态。

## <a name="change-a-property"></a>更改属性

云属性是与设备关联的设备元数据，如 city 和序列号。 可写属性控制设备的行为。 换而言之，设置可用于提供设备的输入。  设备属性由设备设置，在 IoT Central 中是只读的。 您可以查看和更新设备的**设备详细信息**视图的属性。

1. 选择左窗格中的 "**设备**"。

1. 选择要更改其属性的设备的设备模板，然后选择 "目标设备"。

1. 选择包含设备属性的视图，通过此视图，可以输入值，并选择页面顶部的 "**保存**"。 此时会看到设备的属性及其当前值。 云属性和可写属性具有可编辑的字段，而设备属性是只读的。 对于可写属性，可在字段底部查看其同步状态。 

1. 将属性修改为所需的值。 你可以一次修改多个属性并同时更新它们。

1. 选择“保存”。 如果保存了可写属性，则会将值发送到设备。 当设备确认可写属性的更改时，状态返回 "已**同步**"。 如果保存了云属性，则值会更新。


## <a name="next-steps"></a>后续步骤

了解如何在 Azure IoT Central 应用程序中管理设备后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [如何使用设备组](tutorial-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

<!-- Next how-tos in the sequence -->

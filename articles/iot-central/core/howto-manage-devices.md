---
title: 在 Azure IoT Central 应用程序中管理设备 | Microsoft Docs
description: 如何以操作员的身份在 Azure IoT Central 应用程序中管理设备。 了解如何管理单个设备，并对应用程序中的设备进行批量导入和导出。
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperfq2
ms.openlocfilehash: 1782982c75e502ea8df70818a134b5b009188959
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91850092"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>在 Azure IoT Central 应用程序中管理设备

本文介绍如何在 Azure IoT Central 应用程序中管理设备，作为操作员。 操作员可以：

- 使用“设备”页查看、添加和删除与 Azure IoT Central 应用程序连接的设备。****
- 批量导入和导出设备。
- 维护最新的设备清单。
- 通过从视图中更改存储在设备属性中的值，使你的设备元数据保持最新。
- 通过在视图中更新特定设备的设置，来控制设备的行为。

若要了解如何管理自定义的设备组，请参阅 [教程：使用设备组分析设备遥测](tutorial-use-device-groups.md)。

## <a name="view-your-devices"></a>查看设备

若要查看单个设备：

1. 在左侧窗格中选择“设备”。**** 在此处可以看到所有设备和设备模板的列表。

1. 选择一个设备模板。

1. 在“设备”页的右侧窗格中，可以看到从该设备模板创建的设备列表。**** 选择单个设备以查看该设备的“设备详细信息”页：

    ![设备详细信息页](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>添加设备

若要将设备添加到 Azure IoT Central 应用程序：

1. 在左侧窗格中选择“设备”。****

1. 选择要从中创建设备的设备模板。

1. 选择“+ 新建”。****

1. 将“模拟”开关切换为“打开”或“关闭”。************ 真实设备是指要连接到 Azure IoT Central 应用程序的物理设备。 模拟设备包含 Azure IoT Central 生成的示例数据。

1. 选择“创建”  。

1. 此设备随即显示在此模板的设备列表中。 选择设备查看设备详细信息页，其中包含该设备的所有视图。

## <a name="import-devices"></a>导入设备

若要将大量设备连接到应用程序，可从 CSV 文件批量导入设备。 该 CSV 文件应包含以下列和标头：

* **IOTC_DeviceID** - 设备 ID 应全部小写。
* **IOTC_DeviceName** - 此列是可选的。

若要在应用程序中批量注册设备：

1. 在左侧窗格中选择“设备”。****

1. 在左面板中，选择要为其批量创建设备的设备模板。

    > [!NOTE]
    > 如果还没有设备模板，则可以在 " **所有设备** " 下导入设备，并在不使用模板的情况下注册设备。 导入设备后，可将其关联到某个模板。

1. 选择“导入”。

    ![导入操作](./media/howto-manage-devices/bulkimport1a.png)


1. 选择包含要导入的设备 ID 列表的 CSV 文件。

1. 上传文件后，会立即开始导入设备。 可以在“设备操作”面板中跟踪导入状态。 导入开始后，此面板会自动显示；也可以通过右上角的钟形图标访问此面板。

1. 导入完成后，“设备操作”面板中会显示成功消息。

    ![导入成功](./media/howto-manage-devices/bulkimport3a.png)

如果设备导入操作失败，“设备操作”面板中会显示错误消息。 系统会生成一个捕获所有错误的可下载的日志文件。

## <a name="migrate-devices-to-a-template"></a>将设备迁移到模板

如果通过在“所有设备”下启动导入来注册设备，则无需任何设备模板关联即可创建设备。**** 设备必须与模板关联起来，才能浏览有关设备的数据和其他详细信息。 请按照以下步骤将设备与模板进行关联：

1. 在左侧窗格中选择“设备”。****

1. 在左侧面板中选择“所有设备”：****

    ![未关联的设备](./media/howto-manage-devices/unassociateddevices1a.png)

1. 使用网格上的筛选器来确定 **设备模板** 列 **中的值对于任何** 设备是否无关。

1. 选择想要与模板关联的设备：

1. 选择 **迁移**：

    ![关联设备](./media/howto-manage-devices/unassociateddevices2a.png)

1. 从可用模板列表中选择模板，然后选择“迁移”****。

1. 所选设备与所选设备模板相关联。

## <a name="export-devices"></a>导出设备

若要将真实设备连接到 IoT Central，需要其连接字符串。 可以批量导出设备详细信息，以获取创建设备连接字符串所需的信息。 导出过程会为所有选定的设备创建一个 CSV 文件，其中包含设备标识、设备名称和密钥。

从应用程序中批量导出设备：

1. 在左侧窗格中选择“设备”。****

1. 在左侧面板中，选择要从中导出设备的设备模板。

1. 选择要导出的设备，然后选择“导出”**** 操作。

    ![导出](./media/howto-manage-devices/export1a.png)

1. 导出过程开始。 可以使用“设备操作”面板跟踪状态。

1. 导出完成后，将显示一条成功消息，并提供一个用来下载生成文件的链接。

1. 选择“下载文件”链接**** 将文件下载到磁盘上的本地文件夹。

    ![导出成功](./media/howto-manage-devices/export2a.png)

1. 导出的 CSV 文件包含以下列：设备 ID、设备名称、设备密钥和 X509 证书指纹：

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

有关连接字符串以及如何将实际设备连接到 IoT Central 应用程序的详细信息，请参阅 [Azure IoT Central 中的设备连接](concepts-get-connected.md)。

## <a name="delete-a-device"></a>删除设备

若要从 Azure IoT Central 应用程序中删除真实设备或模拟设备：

1. 在左侧窗格中选择“设备”。****

1. 选择要删除的设备的设备模板。

1. 使用筛选工具来筛选和搜索设备。 选中要删除的设备旁边的框。

1. 选择 " **删除**"。 可以在“设备操作”面板中跟踪此删除操作的状态。

## <a name="change-a-property"></a>更改属性

云属性是与设备关联的设备元数据，例如城市和序列号。 云属性仅存在于 IoT Central 的应用程序中，不会同步到你的设备。 可写属性控制设备的行为，并允许你远程设置设备的状态，例如，设置恒温器设备的目标温度。  设备属性由设备设置，在 IoT Central 中是只读的。 可以在设备的“设备详细信息”视图中查看和更新属性。****

1. 在左侧窗格中选择“设备”。****

1. 选择要更改其属性的设备的设备模板，然后选择目标设备。

1. 选择包含设备属性的视图，可在其中输入值并选择页面顶部的“保存”。**** 此处会显示设备的属性及其当前值。 云属性和可写属性包含可编辑的字段，而设备属性是只读的。 对于可写属性，可在字段底部查看其同步状态。 

1. 将属性修改为所需的值。 可以一次性修改多个属性，并可以同时更新所有属性。

1. 选择“保存”  。 如果保存了可写属性，值将会发送到设备。 当设备确认对可写属性的更改时，状态将恢复为“已同步”。**** 如果保存了云属性，值将会更新。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Azure IoT Central 应用程序中管理设备，接下来要介绍如何为你的设备[配置规则](howto-configure-rules.md) 。

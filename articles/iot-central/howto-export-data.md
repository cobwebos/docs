---
title: 导出你的数据从 Azure IoT Central |Microsoft Docs
description: 如何从 Azure IoT Central 应用程序中导出数据
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 98e19cccff1c02f653022f2061854697ee11d1a2
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2019
ms.locfileid: "57759893"
---
# <a name="export-your-data-from-azure-iot-central"></a>从 Azure IoT Central 导出你的数据

*本主题适用于管理员。*

本文介绍如何使用 Azure IoT Central 中的连续数据导出功能将数据导出到你自己的 **Azure Blob 存储**、**Azure 事件中心**和 **Azure 服务总线**实例。 可以将“度量”、“设备”和“设备模板”导出至自己的目标，以便进行暖路径和冷路径分析。 可以将数据导出到 Blob 存储在 Microsoft Power BI 中，运行长期趋势分析或将数据导出到事件中心和服务总线来转换和扩充以使用 Azure 逻辑应用或 Azure Functions 的近实时数据。

> [!Note]
> 启用连续数据导出时，只能获得从那时之后的数据。 目前，关闭连续数据导出后将暂时无法检索数据。 若要保留更多的历史数据，请及早启用连续数据导出。

## <a name="prerequisites"></a>必备组件

在 IoT Central 应用程序中，你必须是管理员

## <a name="export-to-blob-storage"></a>导出到 Blob 存储

每分钟一次将度量、设备和设备模板数据导出到存储帐户，每个文件包含自上次导出文件以来所做的批量更改。 导出的数据采用 [Apache AVRO](https://avro.apache.org/docs/current/index.html) 格式。

详细了解[如何导出到 Blob 存储](howto-export-data-blob-storage.md)。

## <a name="export-to-event-hubs-and-service-bus"></a>导出到事件中心和服务总线

度量、设备和设备模板数据导出到事件中心或服务总线队列或主题。 导出的度量数据以近实时方式到达，并且包含设备发送到 IoT Central 的消息的全部内容，不只是度量值本身。 导出的设备数据会分批到达（每分钟一个批次），并且会包含对所有设备的属性和设置所做的更改，而导出的设备模板则包含对所有设备模板的更改。

详细了解如何[导出到事件中心和服务总线](howto-export-data-event-hubs-service-bus.md)。

## <a name="set-up-export-destination"></a>设置导出目标

如果没有现有的存储/事件中心/服务总线，因此无法将内容导出到其中，请执行以下步骤：

### <a name="create-storage-account"></a>创建存储帐户

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以在 [Azure 存储文档](https://aka.ms/blobdocscreatestorageaccount)中进行详细的了解。

2. 对于帐户类型，选择“常规用途”或“Blob 存储”。

3. 选择订阅。

    > [!Note]
    > 可以将数据导出到其他订阅，此类订阅**不同于**那些适用于即用即付 IoT Central 应用程序的订阅。 在这种情况下使用的连接字符串连接。

4. 在存储帐户中创建容器。 转到存储帐户。 在“Blob 服务”下选择“浏览 Blob”。 选择顶部的“+ 容器”以创建新容器。

### <a name="create-event-hubs-namespace"></a>创建事件中心命名空间

1. [在 Azure 门户中创建新的事件中心命名空间](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 可以在 [Azure 事件中心文档](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)中进行详细的了解。

2. 选择订阅。

    > [!Note]
    > 可以将数据导出到其他订阅，此类订阅**不同于**那些适用于即用即付 IoT Central 应用程序的订阅。 在这种情况下使用的连接字符串连接。

3. 在事件中心命名空间中创建事件中心。 转到命名空间，选择顶部的“+ 事件中心”，以便创建事件中心实例。

### <a name="create-service-bus-namespace"></a>创建服务总线命名空间

1. [在 Azure 门户中创建新的服务总线命名空间](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 可以在 [Azure 服务总线文档](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal)中进行详细的了解。

2. 选择订阅。

    > [!Note]
    > 可以将数据导出到其他订阅，此类订阅**不同于**那些适用于即用即付 IoT Central 应用程序的订阅。 在这种情况下使用的连接字符串连接。

3. 转到服务总线命名空间，选择顶部的“+ 队列”或“+ 主题”，以便创建要向其导出内容的队列或主题。

## <a name="set-up-continuous-data-export"></a>设置连续数据导出

有了可向其导出数据的存储/事件中心/服务总线以后，即可执行以下步骤，以设置连续数据导出。

1. 登录到 IoT Central 应用程序。

2. 在左侧菜单中，选择**连续数据导出**。

    > [!Note]
    > 如果在左侧菜单中看不到“连续数据导出”，则说明你在应用中不是管理员。 请与管理员联系以设置数据导出。

    ![创建新的事件中心](media/howto-export-data/export_menu.png)

3. 选择 **+ 新建**在右上角的按钮。 从 **Azure Blob 存储**、**Azure 事件中心**或 **Azure 服务总线**中选择一个，作为导出的目标。

    > [!NOTE]
    > 每个应用的最大导出数目是 5。

    ![创建新的连续数据导出](media/howto-export-data/export_new.png)

4. 在下拉列表框中，选择自己的**存储帐户/事件中心命名空间/服务总线命名空间**。 也可选取列表中的最后一个选项，即“输入连接字符串”。 

    > [!NOTE]
    > 你只会看到存储帐户/事件中心命名空间/服务总线命名空间中的**与你的 IoT Central 应用相同的订阅**。 若要导出到此订阅外部的某个目标，请选择“输入连接字符串”，然后参阅步骤 5。

    > [!NOTE]
    > 若要通过 7 天试用期的应用来配置连续事件导出，则唯一的方式是使用连接字符串。 这是因为 7 天试用期的应用没有关联的 Azure 订阅。

    ![创建新的 cde 事件中心](media/howto-export-data/export_create.png)

5. （可选）如果选中了“输入连接字符串”，则会出现一个用于粘贴连接字符串的新框。 若要获取连接字符串，请执行以下操作：
    - 如果连接字符串是针对存储帐户的，请转到 Azure 门户中的存储帐户。
        - 下**设置**，选择**访问密钥**
        - 复制 key1 连接字符串或 key2 连接字符串
    - 如果连接字符串是针对事件中心或服务总线的，请转到 Azure 门户中的命名空间。
        - 下**设置**，选择**共享访问策略**
        - 选择默认的 **RootManageSharedAccessKey**，或者创建一个新的
        - 复制主连接字符串或辅助连接字符串

6. 从下拉列表框中选择一个容器/事件中心/队列或主题。

7. 在“要导出的数据”下，通过将类型设置为“打开”来指定要导出的各类数据。

8. 若要启用连续数据导出，请确保将“数据导出”设置为“打开”。 选择“保存”。

    ![配置连续数据导出](media/howto-export-data/export_list.png)

9. 后几分钟，你的数据将出现在所选目标。

## <a name="next-steps"></a>后续步骤

了解如何导出数据后，继续进行下一步：

> [!div class="nextstepaction"]
> [将数据导出到 Azure Blob 存储](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [将数据导出到 Azure 事件中心和 Azure 服务总线](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [如何在 Power BI 中直观显示数据](howto-connect-powerbi.md)

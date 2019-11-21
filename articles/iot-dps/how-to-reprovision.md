---
title: Reprovision devices in Azure IoT Hub Device Provisioning Service
description: Learn how to reprovision devices with your device provisioning service instance, and why you might need to do this.
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: f28dc20a107e9dfdbf252ea614ed4007eafddcd4
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229709"
---
# <a name="how-to-reprovision-devices"></a>如何重新预配设备

在 IoT 解决方案的生命周期中，设备在 IoT 中心之间频繁移动。 此项移动的原因可能包括以下情况：

* **地理位置**：当设备在两个位置之间移动时，通过将设备迁移到离每个位置更近的 IoT 中心来改善网络延迟。

* **多租户**：可在同一 IoT 解决方案中使用设备，但将其重新分配或租赁给新的客户或客户站点。 可使用不同的 IoT 中心为这位新客户提供服务。

* **解决方案更改**：可将设备移到新版或更新后的 IoT 解决方案中。 这种重新分配可能需要设备与连接到其他后端组件的新 IoT 中心通信。 

* **隔离**：类似于解决方案更改。 出现故障、被盗用或已过时的设备可能会重新分配到 IoT 中心，该设备只能在此处进行更新并恢复其符合性。 一旦设备正常运行，它就会迁移回主中心。

For more a more detailed overview of reprovisioning, see [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md).


## <a name="configure-the-enrollment-allocation-policy"></a>配置注册分配策略

分配策略决定了在重新预配与注册关联的设备之后如何将其分配到 IoT 中心。

下面的步骤可配置设备注册项的分配策略：

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到设备预配服务实例。

2. 单击“管理注册”，再单击要配置进行重新预配的注册组或单个注册项。 

3. 在“选择要如何将设备分配到中心”下，选择下述一个分配策略：

    * **最低延迟**：此策略将设备分配到所链接的 IoT 中心，这可使设备与 IoT 中心之间的通信延迟降至最低。 此选项允许设备根据位置与最近的 IoT 中心进行通信。 
    
    * **均匀加权分发**：此策略根据分配到所链接的每个 IoT 中心的分配加权跨链接的 IoT 中心分发设备。 通过此策略，你可根据所链接的一组中心上设置的分配加权跨这些中心实现设备的负载均衡。 如果只将设备预配到一个 IoT 中心，推荐使用此设置。 此设置为默认设置。 
    
    * **静态配置**：此策略规定对于要预配的设备，必须在注册项中列出所需的 IoT 中心。 通过此策略，你可指定要向其分配设备的单个特定 IoT 中心。

4. 在“选择可将此组分配到的 IoT 中心”下，选择要随附分配策略包含在内的已链接的 IoT 中心。 （可选）使用“链接新的 IoT 中心”按钮添加新链接的 IoT 中心。

    通过“最低延迟”分配策略，延迟评估时将纳入你所选的中心，从而确定用于设备分配的最近中心。

    通过“均匀加权分发”分配策略，设备将根据其配置的分配加权和当前设备负载在不同的中心之间实现负载均衡。

    而通过“静态配置”分配策略，可选择要向其分配设备的 IoT 中心。

4. 单击“保存”或继续到下一部分来设备重新预配策略。

    ![选择注册分配策略](./media/how-to-reprovision/enrollment-allocation-policy.png)



## <a name="set-the-reprovisioning-policy"></a>设置重新预配策略

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到设备预配服务实例。

2. 单击“管理注册”，再单击要配置进行重新预配的注册组或单个注册项。

3. 在“选择在重新预配到其他 IoT 中心时要如何处理设备数据”下，选择下述一个重新配置策略：

    * **重新预配并迁移数据**：当与注册项关联的设备提交新的预配请求时，此策略将执行操作。 根据注册项配置，可将设备重新分配给其他 IoT 中心。 如果设备正在更改 IoT 中心，则将删除初始 IoT 中心内的设备注册。 来自该初始 IoT 中心的所有设备状态信息都将迁移到新的 IoT 中心。 迁移期间，设备的状态将报告为“正在分配”

    * **重新预配并重置为初始配置**：当与注册项关联的设备提交新的预配请求时，此策略将执行操作。 根据注册项配置，可将设备重新分配给其他 IoT 中心。 如果设备正在更改 IoT 中心，则将删除初始 IoT 中心内的设备注册。 向新的 IoT 中心提供预配设备时预配服务实例接收到的初始配置数据。 迁移期间，设备的状态将报告为“正在分配”。

4. 单击“保存”，开始根据所作更改进行设备的重新预配。

    ![选择注册分配策略](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>发送来自设备的预配请求

为根据前述部分中所作的配置更改重新预配设备，这些设备必须请求重新预配。 

设备提交预配请求的频率由具体方案而定。 但是，建议设定计划，让设备在重启时向预配服务实例发送预配请求，同时还支持按需手动触发预配的[方法](../iot-hub/iot-hub-devguide-direct-methods.md)。 此外，还可设置[所需属性](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example)来触发预配。 

注册项上的重新预配策略将确定设备预配服务实例如何处理这些预配请求，以及是否应在重新预配期间迁移设备状态数据。 单个注册和注册组可使用相同的策略：

要通过示例代码了解如何在启动顺序期间发送来自设备的预配请求，请参阅[自动预配模拟设备](quick-create-simulated-device.md)。


## <a name="next-steps"></a>后续步骤

- To learn more Reprovisioning, see [IoT Hub Device reprovisioning concepts](concepts-device-reprovision.md) 
- To learn more Deprovisioning, see [How to deprovision devices that were previously auto-provisioned](how-to-unprovision-devices.md) 












---
title: 如何在 Azure IoT 中心设备预配服务中重新预配设备 | Microsoft Docs
description: 如何使用设备预配服务实例重新预配设备
author: wesmc7777
ms.author: wesmc
ms.date: 08/15/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 4fedc5b8b3b69ff364374dbef0460930e1f631a6
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56869065"
---
# <a name="how-to-reprovision-devices"></a>如何重新预配设备

在 IoT 解决方案的生命周期中，设备在 IoT 中心之间频繁移动。 此项移动的原因可能包括以下情况：

* **地理位置**:当在不同位置间移动设备，网络利用率提高通过让设备迁移到更接近每个位置的 IoT 中心。

* **多租户**：设备无法使用相同的 IoT 解决方案中，但，重新分配或租用到新客户或客户站点。 可使用不同的 IoT 中心为这位新客户提供服务。

* **解决方案更改**:可将设备移到新的或更新的 IoT 解决方案。 这种重新分配可能需要设备与连接到其他后端组件的新 IoT 中心通信。 

* **隔离**:类似于解决方案的更改。 出现故障、被盗用或已过时的设备可能会重新分配到 IoT 中心，该设备只能在此处进行更新并恢复其符合性。 一旦设备正常运行，它就会迁移回主中心。

有关更详细的重新预配概述，请参阅 [IoT 中心设备重新预配概念](concepts-device-reprovision.md)。


## <a name="configure-the-enrollment-allocation-policy"></a>配置注册分配策略

分配策略决定了在重新预配与注册关联的设备之后如何将其分配到 IoT 中心。

下面的步骤可配置设备注册项的分配策略：

1. 登录到 [Azure 门户](https://portal.azure.com)，并导航到设备预配服务实例。

2. 单击“管理注册”，再单击要配置进行重新预配的注册组或单个注册项。 

3. 在“选择要如何将设备分配到中心”下，选择下述一个分配策略：

    * **最低延迟**:此策略将设备分配给链接的 IoT 中心，将导致设备与 IoT 中心之间的低延迟通信。 此选项允许设备根据位置与最近的 IoT 中心进行通信。 
    
    * **均匀加权分发**:此策略在链接的 IoT 中心基于分配给每个链接的 IoT 中心的分配权重分配的设备。 通过此策略，你可根据所链接的一组中心上设置的分配加权跨这些中心实现设备的负载均衡。 如果只将设备预配到一个 IoT 中心，推荐使用此设置。 此设置为默认设置。 
    
    * **静态配置**:此策略要求所需的 IoT 中心设备预配的注册项中列出。 通过此策略，你可指定要向其分配设备的单个特定 IoT 中心。

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

    * **重新设置并将数据迁移**:此策略与注册项关联的设备提交新的预配请求时执行操作。 根据注册项配置，可将设备重新分配给其他 IoT 中心。 如果设备正在更改 IoT 中心，则将删除初始 IoT 中心内的设备注册。 来自该初始 IoT 中心的所有设备状态信息都将迁移到新的 IoT 中心。 迁移期间，设备的状态将报告为“正在分配”

    * **重新预配和重置为初始配置**:此策略与注册项关联的设备提交新的预配请求时执行操作。 根据注册项配置，可将设备重新分配给其他 IoT 中心。 如果设备正在更改 IoT 中心，则将删除初始 IoT 中心内的设备注册。 向新的 IoT 中心提供预配设备时预配服务实例接收到的初始配置数据。 迁移期间，设备的状态将报告为“正在分配”。

4. 单击“保存”，开始根据所作更改进行设备的重新预配。

    ![选择注册分配策略](./media/how-to-reprovision/reprovisioning-policy.png)



## <a name="send-a-provisioning-request-from-the-device"></a>发送来自设备的预配请求

为根据前述部分中所作的配置更改重新预配设备，这些设备必须请求重新预配。 

设备提交预配请求的频率由具体方案而定。 但是，建议设定计划，让设备在重启时向预配服务实例发送预配请求，同时还支持按需手动触发预配的[方法](../iot-hub/iot-hub-devguide-direct-methods.md)。 此外，还可设置[所需属性](../iot-hub/iot-hub-devguide-device-twins.md#desired-property-example)来触发预配。 

注册项上的重新预配策略将确定设备预配服务实例如何处理这些预配请求，以及是否应在重新预配期间迁移设备状态数据。 单个注册和注册组可使用相同的策略：

要通过示例代码了解如何在启动顺序期间发送来自设备的预配请求，请参阅[自动预配模拟设备](quick-create-simulated-device.md)。


## <a name="next-steps"></a>后续步骤

- 若要了解有关重新设置的详细信息，请参阅 [IoT 中心设备重新设置概念](concepts-device-reprovision.md) 
- 若要了解更多取消设置，请参阅[如何取消预配了以前自动预配的设备](how-to-unprovision-devices.md) 












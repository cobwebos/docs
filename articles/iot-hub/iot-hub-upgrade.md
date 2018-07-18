---
title: 升级 Azure IoT 中心 | Microsoft Docs
description: 更改 IoT 中心的定价和缩放层，以便获取更多的消息传递和设备管理功能。
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 472115f166adc5385b6f46b2f3ac5ef75a6cde92
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637270"
---
# <a name="how-to-upgrade-your-iot-hub"></a>如何升级 IoT 中心

当 IoT 解决方案扩大时，可以通过 Azure IoT 中心进行纵向扩展。 Azure IoT 中心提供两个层，即基本层 (B) 和标准层 (S)，以便满足希望使用不同功能的客户的需求。 每个层中有三种大小（1、2、3），决定了每天可以发送的消息的数目。 

当设备增多并需要更多功能时，可以通过三种方式来调整 IoT 中心，使之满足自己的需求：

* 在 IoT 中心添加单元。 例如，在 B1 IoT 中心每增加一个单元，每天的消息数就可以增加 400,000。 
* 更改 IoT 中心的大小。 例如，从 B1 层迁移到 B2 层即可增加每个单元每天能够支持的消息数。
* 升级到更高层。 例如，从 B1 层升级到 S1 层时，消息传递容量不变，但可以使用标准层中提供的高级功能。

这些更改均可在不中断现有操作的情况下进行。

若要将 IoT 中心降级，可以删除单元以及缩小 IoT 中心的大小。 不过，不能降级到更低的层。 例如，可以从 S2 层移到 S1 层，但不能从 S2 层移到 B1 层。 

这些示例旨在演示如何根据解决方案的变化来调整 IoT 中心。 有关每个层的功能的具体信息，则应始终参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。 

## <a name="upgrade-your-existing-iot-hub"></a>升级现有的 IoT 中心 

1. 登录 [Azure 门户](https://portal.azure.com/)，导航到 IoT 中心。 
2. 选择“定价和缩放”。 

   ![定价和缩放](./media/iot-hub-upgrade/pricing-scale.png)

3. 若要更改中心的层，请选择“定价和缩放层”。 选择新层，然后单击“选择”。

   ![定价和缩放](./media/iot-hub-upgrade/select-tier.png)

4. 若要更改中心的单元数，请在“IoT 中心单元数”下输入新值。 
5. 选择“保存”以保存更改。 

现在，IoT 中心已进行调整，但配置未更改。 

## <a name="next-steps"></a>后续步骤

详细了解[如何选择正确的 IoT 中心层](iot-hub-scaling.md)。 

